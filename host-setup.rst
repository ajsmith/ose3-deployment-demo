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
