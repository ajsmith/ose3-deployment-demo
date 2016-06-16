================================
OSE Advanced Enablement Homework
================================

The purpose of this project is to document and demonstrate an OSE 3.2
deployment in an HA configuration using a cloud provider, such as AWS or
OpenStack.


System Requirements
===================

Python 2.7 or higher is required to build the documentation and run the
examples. Python virtualenv is also recommended.

This project has been tested on Fedora 23, but should work on most modern Red
Hat Linuxes.

On recent versions of Fedora, these can be installed using::

    # dnf install python python-virtualenv redhat-rpm-config openssl-devel

On RHEL or older versions of Fedora, install by running::

    # yum install python python-virtualenv redhat-rpm-config openssl-devel

If you simply wish to read the documentation without fancy rendering or running
the examples, only a basic text editor is required.


Installation
============

To build documentation and run the examples, the following Python packages are
installed:

  - Ansible
  - OpenStack Client
  - Shade
  - Sphinx

Follow the steps below to perform the installation.

*Note: Installation does not require root privileges.*

 #. Set up a virtualenv site::

    $ virtualenv ~/py

 #. Activate the virtualenv::

    $ source ~/py/bin/activate

 #. Install tools::

    $ pip install -r requirements.txt


Building the Documentation
==========================

To build the HTML documentation, do the following::

    $ source ~/py/bin/activate
    $ make html

The rendered documentation can now be found in `_build/html/`.

Or to render as a PDF, do::

    $ source ~/py/bin/activate
    $ make latexpdf

*Note: Additional texlive packages may need to be installed for PDF
generation.*


Demo
====

The documentation presented here isn't just for reading. You can run it too!
Running this document will demonstrate the full OSE3 deployment. Aside from a
few manual steps to setup your environment, the deployment is fully automated.

To tell which steps are manual and which are executed:

  - Examples prefixed with `>>>` are executable and will be run as part of the
    demo.

  - Shell instructions (prefixed with `$` or `#`) are *not* executable and must
    be performed manually.


Environment Setup
-----------------

Running the examples depend on your environment being set up to authenticate
with an OpenStack environment. You will need to download your "openrc.sh" file
from the OpenStack dashboard. To do so:

 #. Sign in to the OpenStack Dashboard
 #. Go to the "Access & Security" page
 #. Click on the "API Access" tab
 #. Click the "Download OpenStack RC File" button

Once you've downloaded your RC file, add to your environment::

    $ source myproject-openrc.sh


Running the Demo
================

Once you've performed the installation and environment setup steps above,
you'll be ready to run the demo!

To run the thing, do::

    $ source ~/py/bin/activate
    $ make doctest
