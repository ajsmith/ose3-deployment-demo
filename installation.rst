================
OSE Installation
================

Once all hosts are configured, we're able install OSE using the advanced
installation method. The installer is actually run remotely on the OSE
installer host, using the following Ansible playbook.

.. literalinclude:: ansible/deploy-ose.yaml
   :caption:
   :language: yaml

Now let's run it. (Note the output is not checked. We're simply running the
thing for now.)

