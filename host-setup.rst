==========
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
    >>> run_playbook('-i hosts.cfg -f 1 -b -T 20 ansible/setup-hosts.yaml')
    <BLANKLINE>
    PLAY [gather facts about hosts] ************************************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [demo-ose-installer]
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    ...: [demo-ose-dns]
    <BLANKLINE>
    PLAY [setup DNS server] ********************************************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [net-common : configure eth0] *********************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [net-common : configure hostname] *****************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [net-common : configure dhclient] *****************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [rhn : register system to RHN] ********************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [rhn : attach subscription] ***********************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [rhn : disable all repos] *************************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [rhn : enable RHEL repo] **************************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [rhn : update RHEL] *******************************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [dns-server : install BIND packages] **************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [dns-server : configure BIND] *********************************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [dns-server : add zone file for osedemo.internal] *************************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [dns-server : add reverse zone file for osedemo.internal] *****************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [dns-server : add zone file for osedemo.example.com] **********************
    ...: [demo-ose-dns]
    <BLANKLINE>
    TASK [dns-server : start and enable named service] *****************************
    ...: [demo-ose-dns]
    <BLANKLINE>...
    PLAY [setup OSE nodes] *********************************************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [net-common : configure eth0] *********************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [net-common : configure hostname] *****************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [net-common : configure dhclient] *****************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [rhn : register system to RHN] ********************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [rhn : attach subscription] ***********************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [rhn : disable all repos] *************************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [rhn : enable RHEL repo] **************************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [rhn : update RHEL] *******************************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [ose-common : enable OSE3.2 repos] ****************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [ose-common : install base packages] **************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [docker : install docker] *************************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [docker : configure docker storage backend] *******************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [docker : configure docker] ***********************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>
    TASK [docker : enable docker] **************************************************
    ...: [demo-ose-node-1]
    ...: [demo-ose-node-2]
    ...: [demo-ose-node-3]
    ...: [demo-ose-node-4]
    <BLANKLINE>...
    PLAY [setup OSE masters] *******************************************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [net-common : configure eth0] *********************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [net-common : configure hostname] *****************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [net-common : configure dhclient] *****************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [rhn : register system to RHN] ********************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [rhn : attach subscription] ***********************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [rhn : disable all repos] *************************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [rhn : enable RHEL repo] **************************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [rhn : update RHEL] *******************************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [ose-common : enable OSE3.2 repos] ****************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [ose-common : install base packages] **************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [docker : install docker] *************************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [docker : configure docker storage backend] *******************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [docker : configure docker] ***********************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [docker : enable docker] **************************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>
    TASK [ose-master : add htpasswd file] ******************************************
    ...: [demo-ose-master-1]
    ...: [demo-ose-master-2]
    ...: [demo-ose-master-3]
    <BLANKLINE>...
    PLAY [setup load balancers] ****************************************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [net-common : configure eth0] *********************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [net-common : configure hostname] *****************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [net-common : configure dhclient] *****************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [rhn : register system to RHN] ********************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [rhn : attach subscription] ***********************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [rhn : disable all repos] *************************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [rhn : enable RHEL repo] **************************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>
    TASK [rhn : update RHEL] *******************************************************
    ...: [demo-ose-lb-1]
    ...: [demo-ose-lb-2]
    <BLANKLINE>...
    PLAY [setup OSE installer] *****************************************************
    <BLANKLINE>
    TASK [setup] *******************************************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [net-common : configure eth0] *********************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [net-common : configure hostname] *****************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [net-common : configure dhclient] *****************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [rhn : register system to RHN] ********************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [rhn : attach subscription] ***********************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [rhn : disable all repos] *************************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [rhn : enable RHEL repo] **************************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [rhn : update RHEL] *******************************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [ose-common : enable OSE3.2 repos] ****************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [ose-common : install base packages] **************************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [ose-installer : configure ssh options] ***********************************
    ...: [demo-ose-installer]
    <BLANKLINE>
    TASK [ose-installer : add inventory file] **************************************
    ...: [demo-ose-installer]
    <BLANKLINE>...
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
