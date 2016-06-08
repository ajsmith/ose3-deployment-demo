================================
OSE Advanced Enablement Homework
================================

The purpose of this project is to document and demonstrate an OSE 3.2
deployment in an HA configuration using a cloud provider, such as AWS or
OpenStack.


Requirements
============

Python 2.7 or higher is required to build the documentation and run the
examples. Python virtualenv is also recommended.

The tooling has been tested on Fedora 23, but should work on most modern Red
Hat Linuxes.

On recent versions of Fedora, these can be installed using::

    # dnf install python python-virtualenv

On RHEL or older versions of Fedora, install by running::

    # yum install python python-virtualenv

If you simply wish to read the documentation without fancy rendering or running
the examples, only a basic text editor is required.


Installation
============

To install the tools needed to build documentation, follow the steps
below.

*Note: The installation method described here uses Python virtualenv and does
**not** require root privileges.*


 #. Set up a virtualenv site::

        $ virtualenv py

 #. Activate the virtualenv::

        $ source py/bin/activate

 #. Install tools::

        $ pip install -r requirements.txt


Building the Documentation
==========================


To build the HTML documentation, do the following::

    $ source py/bin/activate
    $ make html

The rendered documentation can now be found in `_build/html/`.

To render as a PDF, do::

    $ make latexpdf

*Note: Additional texlive packages may need to be installed for PDF
generation.*


Running the Examples
====================

To run the examples, do the following::

    $ source py/bin/activate
    $ make doctest
