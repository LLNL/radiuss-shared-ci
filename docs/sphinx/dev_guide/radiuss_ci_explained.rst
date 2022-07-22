.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _radiuss_ci_explained-label:

***************************
RADIUSS Shared CI explained
***************************

Radiuss-Shared-CI is an infrastructure and documentation repository created to
help RADIUSS projects adopt the Gitlab CI workflow designed for them.

=================
Project structure
=================

Shared CI files
===============

This project hosts the shared CI configuration, which can be found in
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
``.gitlab/${CI_MACHINE}-build-and-test-extra.yml``. We provide a working
minimal template that should always be provided.

.. note::
   Strictly speaking, the extra jobs file is only needed once the associated
   sub-pipeline is reference to ``.gitlab-ci.yml`` through
   ``customization/custom-pipelines.yml``. We make it presence mandatory for
   the sake of simplicity.

Other files
=============

The documentation source code is in the ``docs`` directory, while ``cmake``
aims at receiving BLT submodule to manage the local build of the docs.

==========
References
==========

`LC specific documentation for Gitlab <https://gitlab.llnl.gov>`_. In
particular, the "Getting Started" and "Setup Mirroring" sub-pages.


