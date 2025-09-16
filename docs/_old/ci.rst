.. ## Copyright (c) 2019-2021, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)

.. _ci:

===============
GitLab CI Guide
===============

This section documents the use of GitLab CI for various projects under the
RADIUSS scope. All RADIUSS projects are hosted on GitHub. Their repositories
are mirrored at LLNL using a self-managed GitLab instance to test the software
on Livermore Computing (LC) machines.

The figure illustrates the dual CI/CD operations: Pull requests (PRs) and
software releases trigger CI both in the Cloud and at LLNL.

.. figure images/DevOpsCycle.png
   :scale: 40 %
   :alt: GitLab CI complements Cloud CI with testing on Livermore Computing (LC) systems.
   :align: center

   GitLab CI executed on Livermore Computing (LC) machines complements
   project CI in the Cloud.

Configuration
=============

GitLab CI configuration is provided in the ``.gitlab-ci.yml`` file at the root
of the project. Variables and stages are defined at a high level in the file
while details are generally maintained separately to avoid having a large,
monolithic configuration file. Different hardware architectures and software
stacks are supported by LC so machine-specific GitLab runner ``tags`` are
included to support limiting some jobs to specific machines.

The logically separated configuration blocks are maintained in files under the
``.gitlab`` subdirectory. Key stages are defined in separate ``YAML`` files
included in the configuration with the ``include`` keyword. Shell script files
defining the work performed by GitLab runners are specified under the
``script`` entry of the corresponding job.

Again, the configuration is split in logical blocks instead of having only one
unique linear file. This is permitted by local ``includes`` feature in GitLab
CI, and all the included files are gathered in ``.gitlab`` directory.

.. code-block:: bash

  $ ls -c1 .gitlab
  butte-jobs.yml
  butte-templates.yml
  lassen-jobs.yml
  lassen-templates.yml
  quartz-jobs.yml
  quartz-templates.yml

Machine names is a logical divider in the configuration of our CI: different
machines allow to test on various hardware architectures and software stacks,
they are identified with runner ``tags``. It is also a practical divider
because script and in particular allocation method varies with the machine.

All jobs for a given machine: <machine>-jobs.yml
------------------------------------------------

For each machine, build and test jobs are gathered in ``<machine>-jobs.yml``.
There, a job definition looks like:

.. code-block:: yaml

  clang_9_0_0 (build and test on quartz):
    variables:
      SPEC: "%clang@9.0.0"
    extends: .build_and_test_on_quartz

With 3 elements:

* The jobs name, which has to be unique. Here ``clang_9_0_0 (build and test on
  quartz)``.
* The ``extends:`` section with the inherited properties.
* The rest: everything specific to this particular job.

The goal is to use "inheritance" to share as much as possible, keeping the final
job definition as short as possible.

.. note:
  This is taking `Umpire <https://github.com/LLNL/Umpire>` as an example

So, what is ``.build_and_test_on_quartz``? It is defined in the corresponding
``<machine>-templates.yml``.

Machine specific templates: <machine>-templates.yml
---------------------------------------------------

.. code-block:: yaml

  .on_quartz:
    tags:
      - shell
      - quartz
    rules:
      - if: '$CI_COMMIT_BRANCH =~ /_qnone/ || $ON_QUARTZ == "OFF"' #run except if ...
        when: never
      - if: '$CI_JOB_NAME =~ /release_resources/'
        when: always
      - when: on_success

  .build_and_test_on_quartz:
    stage: q_build_and_test
    extends: [.build_toss_3_x86_64_ib_script, .on_quartz]

Inheritance can be nested and multiple. In practice it is pretty much working as
an ordered inclusion where the lowest level keys are overridden by the latest if
conflicting, higher level keys being merged.

At this point, our job looks like this:

.. code-block:: yaml

  clang_9_0_0 (build and test on quartz):
    variables:
      SPEC: "%clang@9.0.0"
    stage: q_build_and_test
    extends: [.build_toss_3_x86_64_ib_script]
    tags:
      - shell
      - quartz
    rules:
      - if: '$CI_COMMIT_BRANCH =~ /_qnone/ || $ON_QUARTZ == "OFF"' #run except if ...
        when: never
      - if: '$CI_JOB_NAME =~ /release_resources/'
        when: always
      - when: on_success

Machine agnostic templates are left in .gitlab-ci.yml
-----------------------------------------------------

The remaining ``.build_toss_3_x86_64_ib_script`` is to be found in the root
``.gitlab-ci.yml`` because it describes properties for the job shared on all
machines:

.. code-block:: yaml

  .build_toss_3_x86_64_ib_script:
    script:
      - echo ${ALLOC_NAME}
      - export JOBID=$(squeue -h --name=${ALLOC_NAME} --format=%A)
      - echo ${JOBID}
      - srun $( [[ -n "${JOBID}" ]] && echo "--jobid=${JOBID}" ) -t 15 -N 1 scripts/gitlab/build_and_test.sh
    artifacts:
      reports:
        junit: junit.xml

So that, in the end, our job full definition is:

.. code-block:: yaml

  clang_9_0_0 (build and test on quartz):
    variables:
      SPEC: "%clang@9.0.0"
    stage: q_build_and_test
    script:
      - echo ${ALLOC_NAME}
      - export JOBID=$(squeue -h --name=${ALLOC_NAME} --format=%A)
      - echo ${JOBID}
      - srun $( [[ -n "${JOBID}" ]] && echo "--jobid=${JOBID}" ) -t 15 -N 1 scripts/gitlab/build_and_test.sh
    artifacts:
      reports:
        junit: junit.xml
    tags:
      - shell
      - quartz
    rules:
      - if: '$CI_COMMIT_BRANCH =~ /_qnone/ || $ON_QUARTZ == "OFF"' #run except if ...
        when: never
      - if: '$CI_JOB_NAME =~ /release_resources/'
        when: always
      - when: on_success

Multi-Project Testing Workflow
==============================

Multi-Project Testing consists, for example, in testing a new version of a
dependency (e.g. Umpire library) in a project that depends on it (CHAI) by
triggering the latter CI with the latest version of the former.

This capability is permitted by "pipeline triggers" feature in GitLab, and
illustrated here with Umpire/CHAI/RAJA.

.. image:: images/MultiProjectTestingWorkflow.png
   :scale: 32%
   :alt: Multi-Project Testing applied to testing a dependency by triggering the CI pipeline of two projects depending on it, with a newer/custom version of it.
   :align: center

GitLab configuration for Multi-Project Testing
----------------------------------------------

.. code-block:: yaml

  trigger-chai:
    stage: multi_project
    rules:
      - if: '$CI_COMMIT_BRANCH == "develop" || $MULTI_PROJECT == "ON"' #run only if ...
    variables:
      UPDATE_UMPIRE: develop
    trigger:
      project: radiuss/chai
      branch: develop
      strategy: depend

Here, in Umpire, CHAI CI pipeline is triggered when on ``develop`` branch. CHAI
pipeline will know what version of Umpire to use through the environment
variable ``UPDATE_UMPIRE``.

.. note::
  In CHAI, Umpire is installed with Spack (see Uberenv Guide, CI usage), but
  Spack can’t take a specific commit at the moment as a version specifier, so
  we are limited to branches declared in the package.

The update mechanism relies on a small change in CHAI CI build script:

.. code-block:: bash

  umpire_version=${UPDATE_UMPIRE:-""}

  if [[ -n ${umpire_version} ]]
  then
      extra_deps="${extra_deps} ^umpire@${umpire_version}"
  fi

.. _templates:

GitLab CI template example
==========================

gitlab-ci.yml
-------------

.. literalinclude:: ../../ci_template/gitlab-ci.yml
  :language: yaml
  :lines: 8-

quartz-templates.yml
--------------------

.. literalinclude:: ../../ci_template/quartz-templates.yml
  :language: yaml
  :lines: 8-

quartz-jobs.yml
---------------

.. literalinclude:: ../../ci_template/quartz-jobs.yml
  :language: yaml
  :lines: 8-

lassen-templates.yml
--------------------

.. literalinclude:: ../../ci_template/lassen-templates.yml
  :language: yaml
  :lines: 8-

lassen-jobs.yml
---------------

.. literalinclude:: ../../ci_template/lassen-jobs.yml
  :language: yaml
  :lines: 8-
