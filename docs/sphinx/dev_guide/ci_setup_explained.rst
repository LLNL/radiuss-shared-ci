.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _ci_setup_explained-label:

**************************************
Specificities of the CI implementation
**************************************

Radiuss-Clingo-Install is a CI only repo installing Clingo on LC systems for a
given user. It leverages GitLab capabilities to automate complex actions while
allowing easy customization and user-friendly visual rendering in the UI.

=================
Project structure
=================

CI files
========

The CI core file is ``.gitlab-ci.yml``. We use this file to handle the
conditional inclusion of a **configuration file** and the **pipeline definiton**.

We suggest that the **configuration file** path match
``configs/<config-name>.yml`` although there is no mechanism to enforce that.
The ``configs`` directory is intended to *users*.

The ``Pipeline definition`` is described by the file ``.gitlab/pipeline.yml``
and the associated sub-files, all located in ``.gitlab``. The ``.gitlab``
directory is intended to *developers*.

Other files
=============

The ``scripts`` directory gathers scripts. ``get-spack`` is used to clone Spack
using the variables defined in the **configuration file**. ``print-variables`` is
useful to print the CI variables of interest at the beginning of jobs.

The documentation source code is in the ``docs`` directory, while ``cmake``
aims at receiving BLT submodule to manage the local build of the docs.
