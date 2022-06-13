.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _ci_setup_explained-label:

***********************************
Specificities of the implementation
***********************************

Radiuss-Shared-CI is a configuration repo created to help RADIUSS projects
adopt the Gitlab CI workflow designed for them.

=================
Project structure
=================

Shared CI files
===============

This projects hosts the shared CI configuration, which can be found in
the YAML files at the root of the project: ``<machine>-build-and-test.yml``.

Each file contains both the configuration and the jobs for one machine. They
assume that some entries will be provided by including the customization files.

Customization files
===================

The ``customization`` directory holds all the files allowing to customize the
pipeline.

The files ``custom-pipelines.yml``, ``custom-variables`` and
``custom-jobs.yml`` are all included in the configuration in ``gitlab-ci.yml``.

Project must use ``gitlab-ci.yml`` as a base for the ``.gitlab-ci.yml`` at the
root of their project. This files does not require any change, but can receive
additional stages if needed by the project.

.. note::
   We could share ``.gitlab-ci.yml`` directly in Radiuss-Shared-CI. However
   that would require projects to configure GitLab, through the UI, to use that
   file. This is not considered a good idea at the moment, and we preffer
   projects to have the capability to easily add other stages to their CI
   configuration.

Both ``custom-pipelines.yml`` and ``custom-variablse.yml`` are included
globally. They will affect all the CI workflow. The file ``custom-jobs.yml`` is
included in the ``build-and-test`` sub-pipelines and will only affect those
ones.

Extra jobs
==========

Extra jobs can be defined by the user appending
``.gitlab/${CI_MACHINE}-build-and-test-extra.yml``. Since those files are
always included in the sub-pipelines in ``.gitlab-ci.yml``, they should always
be provided. They can’t be empty neither, which is why we provide a working
minimal template.

Other files
=============

The documentation source code is in the ``docs`` directory, while ``cmake``
aims at receiving BLT submodule to manage the local build of the docs.
