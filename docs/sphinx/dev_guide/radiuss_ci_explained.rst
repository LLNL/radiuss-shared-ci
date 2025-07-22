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

RADIUSS Shared CI is an infrastructure and documentation repository created to
help LLNL open-source projects adopt the Gitlab CI workflow designed for them.

=================
Project structure
=================

Shared CI files
===============

This project hosts the shared CI configuration, which can be found in
the YAML files in the pipelines directory: ``pipelines/<machine>.yml``.

Each file contains both the configuration and the job template for one machine.
They assume that some entries will be provided by including and completing the
customization files.

Customization files
===================

The ``customization`` directory holds all the files allowing to customize the
pipeline.

The ``custom-jobs-and-variables.yml`` should be copied locally to the project
and completed. This file is included as a local file by the template definition
of the subpipeline trigger job, named ``.build-and-test:`` (see
``gitlab-ci.yml``).

Projects must use ``gitlab-ci.yml`` as a base for their own ``.gitlab-ci.yml``,
placing it at the root of the repository. You should give a value to some of
the variables there, according to your use case, and specify the reference of
the `radiuss-shared-ci`_ repository you would like to use
(``${SHARED_CI_REF}``). *This can receive additional stages if needed by the
project.*

.. note::
   We could share ``.gitlab-ci.yml`` directly in RADIUSS Shared CI. However
   that would require projects to configure GitLab, through the UI, to use that
   file. This is not considered a good idea at the moment, and we prefer
   projects to have the capability to easily add other stages to their CI
   configuration.

The ``subscribed-pipelines.yml`` file should be copied over and completed. This
file includes the jobs to check each machines availability, as well as the
actuall subpipeline trigger jobs, extending the template defined in
``.gitlab-ci.yml``.

Local jobs
==========

Local jobs should be defined by the user appending in dedicated files, e.g.
``.gitlab/jobs/${CI_MACHINE}.yml``. We provide a minimal template that can be
copied and duplicated for each machine you would like to set CI for.

.. note::
   Strictly speaking, the local jobs file is only needed once the associated
   sub-pipeline is reference to ``.gitlab-ci.yml`` through
   ``customization/custom-pipelines.yml``. We make it presence mandatory for
   all machines for the sake of simplicity.

Other files
===========

The documentation source code is in the ``docs`` directory, while ``cmake``
aims at receiving BLT submodule to manage the local build of the docs.

==========
References
==========

`LC specific documentation for Gitlab <https://gitlab.llnl.gov>`_ is an
excellent source of information. In particular, the "Getting Started" and
"Setup Mirroring" sub-pages.

.. _radiuss-shared-ci: https://github.com/LLNL/radiuss-shared-ci
