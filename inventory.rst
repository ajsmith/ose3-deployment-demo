=========
Inventory
=========

Now that we've provisioned our servers, let's write an inventory file for them.

First, let's create an OpenStack client instance.

    >>> import shade.inventory
    >>> inventory = shade.inventory.OpenStackInventory()

Then build a list of our instance names to filter by.

    >>> server_names = """\
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
    ... masters
    ... nodes
    ... lbs
    ... dns
    ... [hosts:vars]
    ... ansible_user=cloud-user
    ... [OSEv3:children]
    ... masters
    ... nodes
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
