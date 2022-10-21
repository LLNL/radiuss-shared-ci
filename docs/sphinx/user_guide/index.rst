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

The RADIUSS Shared CI project contains an automated *CI infrastructure based 
on GitLab* that we intend be sufficiently flexible and general to be shared 
among RADIUSS projects. The infrastructure *uses Spack to setup project 
dependencies and generate build configuration files*. This allows projects to 
easily *share the full context of their builds*. The project is then built 
and tested as usual and most of *the CI infrastructure is shared* to avoid 
duplication and ease maintenance.

.. image:: images/UberenvWorkflow.png
   :scale: 32 %
   :alt: RADIUSS Shared CI infrastructure in a project repository.
   :align: center

The design revolves around three steps a project must follow to adopt the 
RADIUSS Shared CI methodology:

  * Run Spack to install dependencies and configure a project build
  * Build and test the code
  * Set up Gitlab CI for the project using the RADIUSS Shared CI infrastructure

These steps are described in the following sections. 

.. toctree::
   :maxdepth: 2

   use_spack
   build_and_test
   setup_ci

We also provide an "How To" section.

.. toctree::
   :maxdepth: 2

   how_to

.. warning::
   Due to its GitLab CI sharing goal, radiuss-shared-ci is meant to live on LC
   GitLab instance. The main repo, hosted on GitHub for accessibility and
   visibility, is mirrored on LC GitLab. To include files from
   radiuss-shared-ci, we recommend pointing to the mirror repo on GitLab rather
   than the GitHub one. We only document that option.
