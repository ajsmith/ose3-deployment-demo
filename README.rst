================================
OSE Advanced Enablement Homework
================================

The purpose of this project is to document and demonstrate an OSE 3.2
deployment in an HA configuration using a cloud provider, such as AWS or
OpenStack.


Building the Documentation
==========================

Sphinx is used to render these documents. The recommended way to install Sphinx
is as follows::

  $ virtualenv py
  $ source py/bin/activate
  $ pip install -r requirements.txt

Now that you have Sphinx installed, you can build the docs as so::

  $ make html

The rendered documentation can now be found in `_build/html/`.
