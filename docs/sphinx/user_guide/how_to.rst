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
