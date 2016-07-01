================
OSE Installation
================

Once all hosts are configured, we're able install OSE using the advanced
installation method. The installer is actually run remotely on the OSE
installer host, using the following Ansible playbook.

.. literalinclude:: ansible/deploy-ose.yaml
   :caption:
   :language: yaml

Now let's run it.

.. doctest::

    >>> import ansible.cli.playbook
    >>> def run_playbook(cmd):
    ...     """Helper function to run playbooks using the Playbook CLI."""
    ...     argv = shlex.split(cmd)
    ...     cli = ansible.cli.playbook.PlaybookCLI(['ansible-playbook'] + argv)
    ...     cli.parse()
    ...     return cli.run()
    >>> run_playbook('ansible/deploy-ose.yaml')
