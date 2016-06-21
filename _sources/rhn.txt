=================
RHN Subscriptions
=================

RHN subscriptions are required to update the instances to the latest version of
RHEL and install OSE3 packages. The :doc:`setup instructions in the README
<README>` describe how to configure your RHN credentials for system
registration, and is required to run the following examples. RHN registration
is implemented as an Ansible Role, which all hosts utilize.

The tasks mainly serve to register the system, attach a subscription, set up
base RHEL repositories, and update the system.

.. literalinclude:: ansible/roles/rhn/tasks/main.yaml
   :caption:
   :language: yaml

The following variables need to be configured in order to register systems.

.. literalinclude:: ansible/roles/rhn/vars/main.yaml.example
   :caption:
   :language: yaml

If a system receives updates, it may need to be rebooted. The RHN role provides
a handler for that as well.

.. literalinclude:: ansible/roles/rhn/handlers/main.yaml
   :caption:
   :language: yaml
