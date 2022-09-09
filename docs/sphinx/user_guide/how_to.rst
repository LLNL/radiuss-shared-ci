.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _user_how_to-label:

******
How To
******

.. _compare_ci_configs:

============================================
Compare the CI configuration of two projects
============================================

Let’s say you want to be in sync in terms of CI configuration with another
project. We summarize here the checklist you should follow to make sure both
configurations are exactly the same, or find the difference between those.

We don’t want to duplicate the whole CI configuration but to check that the
tested specs are the same.

RADIUSS Shared CI reference used to import configuration
========================================================

In ``.gitlab-ci.yml`` look for the reference used for radiuss-shared-ci.

.. code-block:: yaml
   :emphasize-lines: 7

  .build-and-test:
    stage: build-and-test
    trigger:
      include:
        - local: '.gitlab/custom-jobs-and-variables.yml'
        - project: 'radiuss/radiuss-shared-ci'
          ref: v2022.09.0
          file: '${CI_MACHINE}-build-and-test.yml'
        - local: '.gitlab/${CI_MACHINE}-build-and-test-extra.yml'
      strategy: depend
      forward:
        pipeline_variables: true

Two different versions can have differences between the shared specs tested,
you may compare files named ``<MACHINE>-build-and-test.yml`` between the two
references.

.. code-block:: bash

   cd radiuss-shared-ci
   git diff <ref1> <ref2> -- <MACHINE>-build-and-test.yml

Extra jobs added by the projects
================================

Compare each ``.gitlab/<MACHINE>-build-and-test-extra`` between the two
projects and look for:

* differences between jobs with the same name.

* jobs overrides: an "extra" job, if it has the same name as a shared
job, overrides the shared job.

* jobs present only in one of the two projects.

Reference used by RADIUSS Spack Configs to import Spack configuration
=====================================================================

In ``.uberenv_config.json``, the entry ``spack-config-path`` designate the
directory receiving Spack configuration. This in fact point to a submodule:
a clone of `radiuss-spack-configs`_. Check the status of this submodule to
look for differences.

.. note::
   The hash used to checkout out a submodule is also visible in
   ``.gitmodules``.

The Spack configuration can affect the external packages to use, the default
versions for a dependencies to build, etc.

Reference used by Uberenv to clone Spack
========================================

In ``.uberenv_config.json``, the reference used to clone `Spack`_ can be set
with either ``spack_branch`` or ``spack_commit``.

It is basically impossible to ensure identical builds if the Spack versions are
different.

.. _update-shared-ci:

=====================================================================
Update the CI configuration, Spack, Uberenv, or RADIUSS Spack Configs
=====================================================================

RADIUSS Shared CI rely on three other components to work properly: `Spack`_,
`Uberenv`_ and `radiuss-spack-configs`_. As general rule, any update of one
may or will result in the update of the others.

Updating RADIUSS Shared CI
==========================

RADIUSS Shared CI is bound to the versions of `radiuss-spack-configs` and
`Spack` because the shared specs requires the package versions to exist.

If you have overridden shared specs in your extra jobs, you need to check
for changes in the original shared spec after the update: Is the job still
there? Has the spec changed? Is there still a need to override it?

Updating Spack
==============

Spack may be updated without updating radiuss-shared-ci, however there are
regularly changes in the Spack configuration formatting and options that will
force you to update `radiuss-spack-configs`_ and `Uberenv`_.

Updating radiuss-spack-configs
==============================

Be aware of incompatibilities between `Spack`_ and `radiuss-spack-configs`
versions. In radiuss-spack-configs, we use tags to mark changes in the required
version of Spack.

Updating Uberenv
================

In general, `Uberenv`_ is lagging behind in terms of compatibility with
`Spack`_. That's obviously not a guarantee.

====================================================
Deal with project specific variants and dependencies
====================================================

The use case
============

Projects often have a variant they use most of the time, but is not set by
default to keep the default Spack spec simple.

For Umpire, there is ``+fortran``, or ``+openmp`` for RAJA. Those variants
cannot be shared in radiuss-shared-ci because they are likely not implemented
or relevant by default in other projects.

Similarly, Umpire and RAJA require ``^blt@develop`` on corona, which is not
exportable to every projects.

The solution
============

Variables ``PROJECT_<MACHINE>_VARIANTS`` and ``PROJECT_<MACHINE>_DEPS`` can be
set in ``custom-variables.yml`` to define a global variant or dependency to
apply to all the shared specs.

The flip side
=============

If a you want to build a given shared spec without those global variants
or dependencies, you need to duplicate the original job from ``radiuss-shared-ci``
and remove those variables from the spec.

.. note::
   You can keep the same job name and only the spec without global variants and
   dependencies will be built. Or you can rename it to build both specs.

===========================
List the Spack specs tested
===========================

RADIUSS Shared CI uses Spack specs to express the types of builds that should
be tested. We aim at sharing those specs so that projects build with similar
configurations. However we allow projects to add extra specs to test locally.

Shared specs for machine ``ruby`` can be listed directly in radiuss-shared-ci:

.. code-block:: bash

  cd radiuss-shared-ci
  git grep SPEC ruby-build-and-test.yml

Extra ``ruby`` specs, specific to one project, are defined locally to the
project in ``.gitlab/ruby-build-and-test-extra.yml``

.. code-block:: bash

  cd <project>
  git grep SPEC .gitlab/ruby-build-and-test-extra.yml

===========
Use Uberenv
===========

.. code-block:: bash

  $ ./scripts/uberenv/uberenv.py

.. note::
  On LC machines, it is good practice to do the build step in parallel on a
  compute node. Here is an example command: ``srun -ppdebug -N1 --exclusive
  ./scripts/uberenv/uberenv.py``

Unless otherwise specified Spack will default to a compiler. It is recommended
to specify which compiler to use: add the compiler spec to the ``--spec=``
Uberenv command line option.

Some options
^^^^^^^^^^^^

``--spec=`` is used to define how your project will be built. It should be the
same as a spack spec, without the project name:

* ``--spec=%clang@9.0.0``
* ``--spec=%clang@8.0.1+cuda``

The directory that will hold the Spack instance and the installations can also
be customized with ``--prefix=``:

* ``--prefix=<Path to uberenv build directory (defaults to ./uberenv_libs)>``

Building dependencies can take a long time. If you already have a Spack instance
you would like to reuse (in supplement of the local one managed by Uberenv), you
can do so with the ``--upstream=`` option:

* ``--upstream=<path_to_my_spack>/opt/spack ...``

======================================
Allow failure for a spec known to fail
======================================

If a RADIUSS Shared CI pipeline comes with a particular spec that is known to
fail, you may want to allow this spec to fail in CI.

To do so, you will have to duplicate the job in the corresponding
``.gitlab/<MACHINE>-build-and-test-extra.yaml`` keeping the exact same job name
and then add ``allow_failure: true`` to the job definition.

This is a job override. The flip side is that you will have to manually check
for changes in the original shared job when updating RADIUSS Shared CI. See
`update-shared-ci`_ for details.


.. _radiuss-spack-configs: https://github.com/LLNL/radiuss-spack-configs
.. _Uberenv: https://github.com/LLNL/uberenv
.. _Spack: https://github.com/spack/spack
