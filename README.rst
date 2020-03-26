
.. Links
.. _main repository: https://github.com/fledge-iot/fledge


*****************************
Packaging for Fledge SDK
*****************************

This repo contains the scripts used to create a Fledge development package.

We have separated the package build from the `main repository`_ in order to provide a more flexible packaging and to maintain the smallest possible footprint of the main project, even in terms of source code and development.


Internal Structure
==================

The repository contains the following set of files:

- Files named with **make_** prefix, such as ``make_deb``, are the shell scripts used to build the package. The scripts accept the architecture to build as argument (currently *x86* and *arm*).
- The **packages** folder contains the list package types to build. At the moment, the only package type we provide is *Debian*.

  - Inside the *packages/Debian* folder, we have the **architecture** folders, plus a *common* folder containing files that are common to all the architectures. The architectures that we provide at the moment are *armhf* and *x86_64*.

    - Inside the architecture folder we have the DEBIAN folder, which contains all the Debian-based files, i.e. control, pre/post inst/rm, needed for the creation of the package.

  - After the first build, the *packages/Debian* will also contain a **build** folder. This folder contains a copy of what will be used to build the package (in a directory with the same name of the package) and the package itself.

    - In the *build* folder, folders and files that have a sequence number are a previous package build.


The make_deb Script
===================

.. code-block:: console

  $ ./make_deb help
  make_deb [help|clean|cleanall]
  This script is used to create the Debian package of Fledge SDK
  Arguments:
   help     - Display this help text
   clean    - Remove all the old versions saved in format .XXXX
   cleanall - Remove all the versions, including the last one
  $


Building a Package
==================

First, make sure Fledge source code is available somewhere on your environment.
Use FLEDGE_ROOT environment variable to set the source tree location and run ``make`` command in that path.
NOTE: existing Fledge installation via ``make install`` can not be used as it lacks the needed header files.
Next, select the architecture to use, *x86* or *arm*.
Finally, run the ``make_deb`` command:

Complete example:

.. code-block:: console

  $ pwd
  /home/fledge/fledge-pkg
  $ export FLEDGE_ROOT=/home/fledge/source/fledge
  $ cd /home/fledge/source/fledge
  $ make
  ...
  ...
  $ cd /home/fledge/fledge-pkg
  $ ./make_deb
  - Fledge Dev package (headers and libraries) -
  The package root directory is : /home/fledge/fledge-pkg
  The Fledge directory is       : /usr/local/fledge
  The Fledge version is         : 1.7.0
  The package will be built in  : /home/fledge/fledge-pkg/packages/Debian/build/x86_64
  The architecture is set as    : x86_64
  The package name is           : fledge-dev-1.7.0-x86_64

  Populating the package and updating version in control file ...
  - Fledge header files added.
  - Third-party header files added (rapidjson).
  - Third-party header files added (Simple-Web-Server).
  - Fledge library files added.
  Done.

  Building the new package...
  dpkg-deb: building package 'fledge-dev' in 'fledge-dev-1.7.0-x86_64.deb'.
  Building complete.
  $
  
The result will be:
  
.. code-block:: console

  $ ls -l packages/Debian/build/x86_64/
  total 956
  drwxrwxr-x 4 ubuntu ubuntu   4096 Mar 26 11:43 fledge-dev-1.7.0-x86_64
  -rw-r--r-- 1 ubuntu ubuntu   971676 Mar 26 11:43 fledge-dev-1.7.0-x86_64.deb
  $

The package contains two paths:

- ./usr/include/fledge/ (Fledge header files)
- ./usr/lib/fledge/     (Fledge libraries)

If you execute the ``make_deb`` command again, you will see:

.. code-block:: console

  $ ./make_deb
  - Fledge Dev package (headers and libraries) -
  The package root directory is : /home/fledge/fledge-pkg
  The Fledge directory is       : /usr/local/fledge
  The Fledge version is         : 1.7.0
  The package will be built in  : /home/fledge/fledge-pkg/packages/Debian/build/x86_64
  The architecture is set as    : x86_64
  The package name is           : fledge-dev-1.7.0-x86_64

  Saving the old working environment as fledge-dev-1.7.0-x86_64.0001

  Populating the package and updating version in control file ...
  - Fledge header files added.
  - Third-party header files added (rapidjson).
  - Third-party header files added (Simple-Web-Server).
  - Fledge library files added.
  Done.

  Saving the old package as fledge-dev-1.7.0-x86_64.deb.0001
  Building the new package...
  dpkg-deb: building package 'fledge-dev' in 'fledge-dev-1.7.0-x86_64.deb'.
  Building complete.
  $

  $ ls -l packages/Debian/build/x86_64
  total 1904
  drwxrwxr-x 4 ubuntu ubuntu   4096 Mar 26 11:48 fledge-dev-1.7.0-x86_64
  drwxrwxr-x 4 ubuntu ubuntu   4096 Mar 26 11:43 fledge-dev-1.7.0-x86_64.0001
  -rw-r--r-- 1 ubuntu ubuntu   965372 Mar 26 11:48 fledge-dev-1.7.0-x86_64.deb
  -rw-r--r-- 1 ubuntu ubuntu   971676 Mar 26 11:43 fledge-dev-1.7.0-x86_64.deb.0001
  $
   
... where the previous build is now marked with the suffix *.0001*.


Cleaning the Package Folder
===========================

Use the ``clean`` option to remove all the old packages and the files used to make the package.
Use the ``cleanall`` option to remove all the packages and the files used to make the package.
