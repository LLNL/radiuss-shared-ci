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

====================================================
Deal with project specific variants and dependencies
====================================================

The use case
============

Projects often have a variant they use most of the time, but is not set by
default to keep the default Spack spec simple.

For Umpire, there is ``+fortran``, or ``+openmp`` for RAJA. Those variants
cannot be shared in Radiuss-Shared-CI because they are likely not implemented
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

Shared specs for machine ``ruby`` can be listed directly in Radiuss-Shared-CI:

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
============

``--spec=`` is used to define how your project will be built. It should be the
same as a Spack spec, without the project name:

* ``--spec=%clang@9.0.0``
* ``--spec=%clang@8.0.1+cuda``

The directory that will hold the Spack instance and the installations can also
be customized with ``--prefix=``:

* ``--prefix=<Path to uberenv build directory (defaults to ./uberenv_libs)>``

Building dependencies can take a long time. If you already have a Spack instance
you would like to reuse (in supplement of the local one managed by Uberenv), you
can do so with the ``--upstream=`` option:

* ``--upstream=<path_to_my_spack>/opt/spack ...``

===========================================
Choose a Spack reference (commit or branch)
===========================================

Uberenv needs to know which version of Spack to clone locally. In general,
using the latest Spack release should be the default strategy. But things can
quickly get complicated.

Among the decision parameters:

* Need for a newer Spack feature / fix.

* Need for a newer package version, for example supporting the latest release
  of a given product.

* Coherency with other projects.

Let’s take the example of Umpire/RAJA/CHAI. Those projects work together and
have synchronized releases. They all use Uberenv for their CI.

For those projects we try to:

* Use the same Spack reference (behave coherently in tests).

* Use a Spack reference as new as possible, without changing it every month
  (for now).

* Limit local patching of Spack packages.

Limiting local patching of Spack packages
=========================================

Uberenv allows projects to duplicate any Spack package locally and patch it.
It is important to limit the amount of patching however. Every local patch
creates a divergence between the developer / CI configuration and the one the
user gets from through upstream Spack.

Typical use case for a local package patch:

* Test changes to the package that will be necessary for the next release.

* Fix a bug, test a tweak in a toolchain configuration (we have seen the need
  for flags, or hip / cuda tweaks in the past).

In any case, those local changes should be pushed to upstream Spack as soon as
possible.

Spack reference during the release process
==========================================

As discussed above, when a projects wants to do a release, the release has to
happen before it can be added to Spack.

Then, we want:

* To limit the use of a local patch: after a release there should be no local
  patching needed.

* To make sure we keep testing our code as close as possible to the user
  configuration: only the latest Spack package has the logic to build the
  latest release, users will want that.

For the project, that means we will have to update the Spack reference for
Uberenv as soon as the Spack package has been updated.

.. note::
   Upstream of the release, we might want to test the upcoming Spack package
   changes in spack@develop. In other words, we could anticipate the creation
   of a pull request in Spack and use it as a reference in Uberenv. However, it
   is not advised to create the release with this setting, because uberenv now
   points to a PR in Spack that will likely disappear in the future.

In a nutshell
=============

The chosen Spack reference used in uberenv should evolve in time as follow:

* After a project release, when the upstream Spack package gets updated, and
  Uberenv should point to the corresponding Spack merge commit.

* Then, when a new Spack release comes out, it will have our latest changes and
  should be used as a reference.

* Approaching a new release, Uberenv should point to the latest Spack release,
  but we might want to anticipate some testing with spack@develop, without
  merging that change.
