.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _user_guide-label:

##########
User Guide
##########

The RADIUSS Shared CI project contains an *automated CI infrastructure based
on GitLab* that we intend be sufficiently flexible and general to be adopted
and shared by RADIUSS projects. The infrastructure *uses Spack to setup project
dependencies and generate build configuration files*. This allows projects to
easily *share the full context of their builds*. A project is then built
and tested as usual and *most of the CI infrastructure is shared* to reduce
duplicated effort and ease maintenance of testing for individual projects.

.. image:: images/UberenvWorkflow.png
   :scale: 32 %
   :alt: RADIUSS Shared CI infrastructure in a project repository.
   :align: center

   The figure illustrates the various software packages involved (orange boxes)
   and their relationships to a project that uses them. 

A project that uses the shared CI framework typically has two Git submodules: 
`Uberenv <https://github.com/LLNL/uberenv>`_ and
`RADIUSS Spack Configs <https://github.com/LLNL/radiuss-spack-configs>`_. 
*Uberenv* contains a Python script that helps automate building third-party
dependencies for a project. It drives Spack in Unix-based and macOS 
environments, and Vcpkg on Windows. *RADIUSS Spack Configs* contains a set of 
compiler and package configurations that projects share to construct uniform 
build configurations.

Shared GitLab CI is this RADIUSS Shared CI project, which is hosted on GitHub
and is mirrored on the Livermore Computing (LC) CZ GitLab instance. A project
points to the GitLab mirror to access RADIUSS Shared CI configurations.

RADIUSS Shared CI design revolves around three steps a project must follow to 
adopt the shared CI methodology. These are described in the following sections.

.. toctree::
   :maxdepth: 2

   use_spack
   build_and_test
   setup_ci

We also provide "How To" sections to perform various usage tasks.

.. toctree::
   :maxdepth: 2

   how_to

.. warning::
   Due to its GitLab CI sharing goal, radiuss-shared-ci is meant to live on 
   the LC GitLab instance. The main repo is hosted on GitHub for accessibility 
   and visibility and is mirrored on LC GitLab. To include files from
   radiuss-shared-ci, we recommend pointing to the mirror repo on GitLab rather
   than the GitHub projects. We only document that option.
