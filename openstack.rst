=======================
Deployment on OpenStack
=======================

Here, we will provision the servers and other resources needed for the OSE3
installation, specified in the :doc:`architecture overview <architecture>`.

This deployment is targeted for use with the OS1 Public cloud. Other OpenStack
clouds may be used but are untested. Modifications may be needed for these
examples to work in other OpenStack environments.

These examples assume you're starting from scratch and have no existing
instances. If you have existing instances, you'll have to adjust these examples
for your specific environment.


Environment
===========

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
    ...: [localhost] => (item=master-4-docker-vol)
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
