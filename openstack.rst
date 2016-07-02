=======================
Deployment on OpenStack
=======================

Here, we will provision the servers and other resources needed for the OSE3
installation. This deployment is targeted for use with the OS1 Public
cloud. Other OpenStack clouds may be used but are untested. Modifications may
be needed for these examples to work in other OpenStack environments.

These examples assume you're starting from scratch and have no existing
instances. If you have existing instances, you'll have to adjust these examples
for your specific environment.


Architecture
============

The HA deployment of OSE 3.2 will consist of the following hosts:

- 1 x Installer
- 3 x Masters
- 4 x Nodes (2 Infra, 2 App)
- 2 x Load Balancers
- 1 x DNS

Below are the hardware specs for each host type:

+-----------+------+----------+-------------+-------------+
| Node Type | CPUs | RAM (GB) | Disk 1 (GB) | Disk 2 (GB) |
+===========+======+==========+=============+=============+
| Installer | 2    | 4        | 20          | None        |
+-----------+------+----------+-------------+-------------+
| Master    | 2    | 4        | 40          | 15          |
+-----------+------+----------+-------------+-------------+
| Compute   | 2    | 4        | 25          | 15          |
+-----------+------+----------+-------------+-------------+
| LB        | 2    | 4        | 20          | None        |
+-----------+------+----------+-------------+-------------+
| DNS       | 2    | 4        | 20          | None        |
+-----------+------+----------+-------------+-------------+


OpenStack Environment
=====================

The following examples require that your environment be configured with your
OpenStack login credentials. This is typically accomplished by running:

.. code-block:: shell

    $ source openrc.sh

You can download the above file from the "Access & Security" page in the
OpenStack dashboard.


Prerequisites
=============


Images
------

A valid RHEL 7 base image must be available prior to running these examples,
and it must be named 'RHEL7'. This image will be used to provision the servers
in these examples.

.. doctest::

    >>> import openstackclient.shell
    >>> import shlex
    >>> openstackclient.shell.main(shlex.split('image show RHEL7'))
    +------------------+------------------------------------------------------+
    | Field            | Value                                                |
    +------------------+------------------------------------------------------+
    ...
    | name             | RHEL7                                                |
    ...
    +------------------+------------------------------------------------------+
    0


Flavors
-------

All instances use the "m1.medium" flavor, which has 2 CPUs and 4GB RAM.

.. doctest::

    >>> openstackclient.shell.main(shlex.split('flavor show m1.medium'))
    +----------------------------+--------------------------------------+
    | Field                      | Value                                |
    +----------------------------+--------------------------------------+
    ...
    | name                       | m1.medium                            |
    ...
    | ram                        | 4096                                 |
    ...
    | vcpus                      | 2                                    |
    +----------------------------+--------------------------------------+
    0


Security Groups
---------------

The "default" security group allows SSH and ICMP (ping).

    >>> openstackclient.shell.main(shlex.split('security group show default'))
    +-------------+--------------------------------------------------------------------------+
    | Field       | Value                                                                    |
    +-------------+--------------------------------------------------------------------------+
    ...
    | name        | default                                                                  |
    ...
    +-------------+--------------------------------------------------------------------------+
    0

The "dns" security group allows DNS server traffic.

    >>> openstackclient.shell.main(shlex.split('security group show dns'))
    +-------------+-------------------------------------------------------------------------+
    | Field       | Value                                                                   |
    +-------------+-------------------------------------------------------------------------+
    ...
    | name        | dns                                                                     |
    ...
    +-------------+-------------------------------------------------------------------------+
    0


Provisioning
============

At this point, we can begin provisioning. We'll use the Ansible OpenStack
modules. Here's our playbook:

.. literalinclude:: ansible/provision-openstack.yaml
   :caption:
   :language: yaml

Now let's run it.

.. doctest::
   :options: +NORMALIZE_WHITESPACE, +ELLIPSIS

    >>> import ansible.cli.playbook
    >>> def run_playbook(cmd):
    ...     """Helper function to run playbooks using the Playbook CLI."""
    ...     argv = shlex.split(cmd)
    ...     cli = ansible.cli.playbook.PlaybookCLI(['ansible-playbook'] + argv)
    ...     cli.parse()
    ...     return cli.run()
    >>> run_playbook('ansible/provision-openstack.yaml')
    <BLANKLINE>
    PLAY [provision server resources in OpenStack] *********************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [localhost]
    <BLANKLINE>
    TASK [create OSE installer root volume] ****************************************
    ...: [localhost]
    <BLANKLINE>
    TASK [create OSE master root volumes] ******************************************
    ...: [localhost] => (item=ose-master-1-root-vol)
    ...: [localhost] => (item=ose-master-2-root-vol)
    ...: [localhost] => (item=ose-master-3-root-vol)
    <BLANKLINE>
    TASK [create OSE node root volumes] ********************************************
    ...: [localhost] => (item=ose-node-1-root-vol)
    ...: [localhost] => (item=ose-node-2-root-vol)
    ...: [localhost] => (item=ose-node-3-root-vol)
    ...: [localhost] => (item=ose-node-4-root-vol)
    <BLANKLINE>
    TASK [create LB root volumes] **************************************************
    ...: [localhost] => (item=ose-lb-1-root-vol)
    ...: [localhost] => (item=ose-lb-2-root-vol)
    <BLANKLINE>
    TASK [create DNS root volume] **************************************************
    ...: [localhost]
    <BLANKLINE>
    TASK [create Docker storage volumes for OSE masters] ***************************
    ...: [localhost] => (item=master-1-docker-vol)
    ...: [localhost] => (item=master-2-docker-vol)
    ...: [localhost] => (item=master-3-docker-vol)
    <BLANKLINE>
    TASK [create Docker storage volumes for OSE nodes] *****************************
    ...: [localhost] => (item=ose-node-1-docker-vol)
    ...: [localhost] => (item=ose-node-2-docker-vol)
    ...: [localhost] => (item=ose-node-3-docker-vol)
    ...: [localhost] => (item=ose-node-4-docker-vol)
    <BLANKLINE>
    TASK [create OSE installer instance] *******************************************
    ...: [localhost]
    <BLANKLINE>
    TASK [create OSE master instances] *********************************************
    ...: [localhost] => (item=ose-master-1)
    ...: [localhost] => (item=ose-master-2)
    ...: [localhost] => (item=ose-master-3)
    <BLANKLINE>
    TASK [create OSE node instances] ***********************************************
    ...: [localhost] => (item=ose-node-1)
    ...: [localhost] => (item=ose-node-2)
    ...: [localhost] => (item=ose-node-3)
    ...: [localhost] => (item=ose-node-4)
    <BLANKLINE>
    TASK [create LB instances] *****************************************************
    ...: [localhost] => (item=ose-lb-1)
    ...: [localhost] => (item=ose-lb-2)
    <BLANKLINE>
    TASK [create DNS instance] *****************************************************
    ...: [localhost]
    <BLANKLINE>
    PLAY RECAP *********************************************************************
    localhost                  : ok=...   changed=...    unreachable=0    failed=0
    <BLANKLINE>
    0


Inventory
=========

Now that we've provisioned our servers, let's write an inventory file for them.

First, let's create an OpenStack client instance.

    >>> import shade.inventory
    >>> inventory = shade.inventory.OpenStackInventory()

Then build a list of our instance names to filter by.

    >>> server_names = """\
    ... demo-ose-installer
    ... demo-ose-master-1
    ... demo-ose-master-2
    ... demo-ose-master-3
    ... demo-ose-node-1
    ... demo-ose-node-2
    ... demo-ose-node-3
    ... demo-ose-node-4
    ... demo-ose-lb-1
    ... demo-ose-lb-2
    ... demo-ose-dns
    ... """.split()
    >>> server_names = set(server_names)

Get a list of hosts, selecting only the ones above.

    >>> hosts = filter(lambda e: e[u'human_id'] in server_names, inventory.list_hosts())

Build a map of server name to IPs.

    >>> address_map = dict((e[u'human_id'], e[u'accessIPv4']) for e in hosts)

Then write out the inventory file.

    >>> inventory_file = open('hosts.cfg', 'w')
    >>> inventory_file.write("""\
    ... [hosts:children]
    ... installer
    ... masters
    ... nodes
    ... lbs
    ... dns
    ... [hosts:vars]
    ... ansible_user=cloud-user
    ... [OSEv3:children]
    ... masters
    ... nodes
    ... [installer]
    ... demo-ose-installer ansible_host={demo-ose-installer}
    ... [masters]
    ... demo-ose-master-1 ansible_host={demo-ose-master-1}
    ... demo-ose-master-2 ansible_host={demo-ose-master-2}
    ... demo-ose-master-3 ansible_host={demo-ose-master-3}
    ... [nodes]
    ... demo-ose-node-1 ansible_host={demo-ose-node-1}
    ... demo-ose-node-2 ansible_host={demo-ose-node-2}
    ... demo-ose-node-3 ansible_host={demo-ose-node-3}
    ... demo-ose-node-4 ansible_host={demo-ose-node-4}
    ... [lbs]
    ... demo-ose-lb-1 ansible_host={demo-ose-lb-1}
    ... demo-ose-lb-2 ansible_host={demo-ose-lb-2}
    ... [dns]
    ... demo-ose-dns ansible_host={demo-ose-dns}
    ... """.format(**address_map))
    >>> inventory_file.close()

Finally, let's check that it works by calling Ansible to check our hosts.

    >>> import ansible.cli.adhoc
    >>> import shlex
    >>> def run_ansible(cmd):
    ...     """Helper function to run Ansible using the Ansible CLI."""
    ...     argv = shlex.split(cmd)
    ...     cli = ansible.cli.adhoc.AdHocCLI(['ansible'] + argv)
    ...     cli.parse()
    ...     return cli.run()

To check, let's simply tell all of our hosts to report their hostname.

    >>> run_ansible('-i hosts.cfg -f 1 -o -m command -a "hostname -s" all')
    demo-ose-installer | SUCCESS | rc=0 | (stdout) demo-ose-installer
    demo-ose-master-1 | SUCCESS | rc=0 | (stdout) demo-ose-master-1
    demo-ose-master-2 | SUCCESS | rc=0 | (stdout) demo-ose-master-2
    demo-ose-master-3 | SUCCESS | rc=0 | (stdout) demo-ose-master-3
    demo-ose-node-1 | SUCCESS | rc=0 | (stdout) demo-ose-node-1
    demo-ose-node-2 | SUCCESS | rc=0 | (stdout) demo-ose-node-2
    demo-ose-node-3 | SUCCESS | rc=0 | (stdout) demo-ose-node-3
    demo-ose-node-4 | SUCCESS | rc=0 | (stdout) demo-ose-node-4
    demo-ose-lb-1 | SUCCESS | rc=0 | (stdout) demo-ose-lb-1
    demo-ose-lb-2 | SUCCESS | rc=0 | (stdout) demo-ose-lb-2
    demo-ose-dns | SUCCESS | rc=0 | (stdout) demo-ose-dns
    0


Host Setup
==========

OSE hosts must be configured to meet the prerequisites given in the OSE
installation documentation. This basically amounts to installing the required
packages and configuring Docker.

Here, we'll also configure the additional instances needed to support our
environment, which includes the DNS server, load balancers, and the OSE
installer.

Below is the Ansible playbook used to perform these setup tasks.

.. literalinclude:: ansible/setup-hosts.yaml
   :caption:
   :language: yaml

All of the work in this playbook is done by the roles declared by each
play. Tags are also applied to allow for re-running plays for specific hosts
without needing to execute the entire playbook. All of the roles used are
defined in `ansible/roles/`.

Now let's run it.

.. doctest::
   :options: +NORMALIZE_WHITESPACE, +ELLIPSIS

    >>> import ansible.cli.playbook
    >>> import shlex
    >>> def run_playbook(cmd):
    ...     """Helper function to run playbooks using the Playbook CLI."""
    ...     argv = shlex.split(cmd)
    ...     cli = ansible.cli.playbook.PlaybookCLI(['ansible-playbook'] + argv)
    ...     cli.parse()
    ...     return cli.run()
    >>> run_playbook('-i hosts.cfg -b -T 20 ansible/setup-hosts.yaml')
    <BLANKLINE>
    PLAY [gather facts about hosts] ************************************************
    ...
    PLAY [setup DNS server] ********************************************************
    ...
    PLAY [setup OSE nodes] *********************************************************
    ...
    PLAY [setup OSE masters] *******************************************************
    ...
    PLAY [setup load balancers] ****************************************************
    ...
    PLAY [setup OSE installer] *****************************************************
    ...
    PLAY RECAP *********************************************************************
    demo-ose-dns               : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-installer         : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-lb-1              : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-lb-2              : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-master-1          : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-master-2          : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-master-3          : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-node-1            : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-node-2            : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-node-3            : ok=...   changed=...    unreachable=0    failed=0
    demo-ose-node-4            : ok=...   changed=...    unreachable=0    failed=0
    <BLANKLINE>
    0


OSE Installation
================

Once all hosts are configured, we're able install OSE using the advanced
installation method. The installer is actually run remotely on the OSE
installer host, using the following Ansible playbook.

.. literalinclude:: ansible/deploy-ose.yaml
   :caption:
   :language: yaml

To perform the installation, run:

.. code-block:: shell

    $ ansible-playbook -i hosts.cfg ansible/deploy-ose.yaml

Once complete, the OSE environment will be installed and running.
