.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _getting_started-label:

**************************************
Getting started with Radiuss-Shared-CI
**************************************

Radiuss-Shared-CI is a repo containing only Gitlab CI YAML files and
documentation. There is no installation and the CI files contained here do not
define a CI for this project, meaning there is no test for this repo.

Radiuss-Shared-CI is meant to live on LC GitLab instance. The main repo, hosted
on GitHub for accessibility and visibility, is mirrored on LC GitLab. To
include files from Radiuss-Shared-CI, we recommend pointing to the mirror repo
on GitLab rather than the GitHub one. We only document that option.

=================
The short version
=================

.. code-block:: bash

   git clone https://github.com/LLNL/radiuss-shared-ci.git
   cd my_project
   cp ../radiuss-shared-ci/customization/gitlab-ci.yml .gitlab-ci.yml
   mkdir -p .gitlab
   cp ../radiuss-shared-ci/customization/custom-*.yml .gitlab
   cp ../radiuss-shared-ci/example-extra-jobs/*-extra.yml .gitlab
   vim .gitlab/custom-*.yml
   # customize CI
   vim .gitlab/*-extra.yml
   # edit extra jobs
   mkdir -p scripts/gitlab
   vim scripts/gitlab/build-and-test
   # write CI script

Jump to the corresponding section to deal with :ref:`customize-ci`,
:ref:`edit-extra-jobs` and :ref:`write-ci-script`.

====================
The detailed version
====================

Our CI implementation can be divided in four parts:

* shared files
* customization files
* extra jobs
* local build-and-test script

Setting up the CI will basically consist in four corresponding phases.

Start by cloning the project locally, for example next to the project you intend
to add CI to.

.. code-block:: bash

   git clone https://github.com/LLNL/radiuss-shared-ci.git
   cd my_project


Core CI implementation
======================

By default, GitLab expects a ``.gitlab-ci-yml`` file to interpret the CI setup.
We provide one that projects can copy-paste in ``customization/gitlab-ci.yml``,
just be sure to place it at the root of your project, with a dot (``.``) at the
beginning of the name.

.. code-block:: bash

   cp ../radiuss-shared-ci/customization/gitlab-ci.yml .gitlab-ci.yml


Your CI is now setup to include remote files from the GitLab mirror of
Radiuss-Shared-CI.

We now have to complete the interface with the shared CI config. Indeed,
``.gitlab-ci.yml`` also expects some files to be present locally. Those are the
next steps.

.. _customize-ci:

Customize CI
============

We provide templates for the required customization files. We need to copy
them in the ``.gitlab`` directory.

.. code-block:: bash

   mkdir -p .gitlab
   cp ../radiuss-shared-ci/customization/custom-*.yml .gitlab

We will now browse the files to see what changes they may require to suit your
needs.

``.gitlab/custom-pipelines.yml``
------------------------

In this file, you will select the machines you want to run tests on. Comment
the jobs (sections) corresponding to machines you don't want, or don't have
access to.

``.gitlab/custom-jobs.yml``
------------------------

No change is strictly required to get started here.

In this file, you may add configuration to the ``.custom_build_and_test`` job
that will then be included to all you CI jobs. This can be used for example to
`export jUnit test reports`_.

``.gitlab/custom-variables.yml``
------------------------

We should now have a look at ``.gitlab/custom-variables.yml``. Here is a table
to describe each variable present in the file. Some more details can be found
in the file itself.

 ========================================== ==========================================================================================================================
  Parameter                                  Description
 ========================================== ==========================================================================================================================
  ``LLNL_SERVICE_USER``                      Service Account used in CI
  ``CUSTOM_CI_BUILD_DIR``                    Where to locate build directories (prevent overquota)
  ``GIT_SUBMODULES_STRATEGY``                Controls strategy for the clone performed by GitLab. Consider ``recursive`` if you have submodules, otherwise comment it.
  ``BUILD_ROOT``                             Location (path) where the projects should be built. We provide a sensible default.
  ``ALLOC_NAME``                             Name of the shared allocation. Should be unique, our default should be fine.
  ``<MACHINE>_BUILD_AND_TEST_SHARED_ALLOC``  Parameters for the shared allocation. You may extend the resource and time.
  ``<MACHINE>_BUILD_AND_TEST_JOB_ALLOC``     Parameters for the job allocation. You may extend the resource and time within the scope of the shared allocation.
 ========================================== ==========================================================================================================================

.. note::
   If a variable is blank in the template file, then it does not require a
   value. If a variable has a value there, it does require one.

.. note::
   We strongly recommend that you set your CI to use a service account.

.. _edit-extra-jobs:

Edit extra jobs
===============

We provide templates for the extra jobs files. Those files are required as soon
as the associated machine has been activated in ``.gitlab/custom-pipelines``.

If no extra-jobs is needed (the shared jobs are automatically included), then
you should add the extra-jobs files as-is, with a simple variable definition to
avoid it to be empty.

If you need to define extra-jobs specific to your projects, then you may remove
the variable definition, uncomment the template job and complete it with the
required information:

* A job name, unique, that will appear in CI.
* A Spack spec used by ``build-and-test`` to know what to build.

.. note::
   Gitlab supports long and complex job names. Make sure to pick a unique name
   not to override a shared job.

.. _write-ci-script:

Write CI Script
===============

The last step is to provide a CI script. You may already have one you can
adapt, the requirements are:

* The script should be named ``build-and-test`` and located in
  ``scripts/gitlab``, and it should take a Spack spec as input through the
  environment variable ``$SPEC``.

* The script should use that spec to instruct Spack to install the
  dependencies. Then you can build your project using those. This is the
  workflow documented in `Radiuss CI`_ and we encourage you to use it.

Umpire, RAJA, CHAI, MFEM each have their own script you could easily adapt. All
these projects use Uberenv to drive Spack. Umpire, RAJA and CHAI share the
Spack configuration files in `Radiuss-Spack-Configs`_ in order to keep building
with the same tool-chains.

.. _Radiuss CI: https://radiuss-ci.readthedocs.io/en/latest/index.html
.. _Radiuss-Spack-Configs: https://github.com/LLNL/radiuss-spack-configs
.. _export jUnit test reports: https://github.com/LLNL/Umpire/blob/develop/.gitlab/custom-jobs.yml
