.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _dev_how_to-label:

******
How To
******

=================
Add a new machine
=================

Adding a new machine can be done directly in this project so that the
configuration is shared with all. However it the associated Spack configuration
must first be added to `Radiuss-Spack-Configs`_.

The sub-pipeline definition
===========================

To add a new machine, create a corresponding ``<machine>-build-and-test.yml``
file describing the sub-pipeline. There are two main cases: whether the machine
uses Slurm or LSF Spectrum as a scheduler.

Machines using Slurm scheduler
------------------------------

For machines using Slurm scheduler, use ``ruby`` (or ``corona``) as a starting
point. Then replace all the instances of "RUBY" and "ruby" with the new machine
name.

Then go to ``customization/custom-variables.yml`` and add the variables:

* ``<MACHINE>_BUILD_AND_TEST_SHARED_ALLOC`` with allocation options sufficient
  for the shared allocation (salloc) to contain all the jobs.
* ``<MACHINE>_BUILD_AND_TEST_JOB_ALLOC`` with allocation options for any of the
  jobs (srun) the machine will take. The job will run under the shared
  allocation, also, do not prescribe a number of cores here, as they should be
  defined at Spack and Make/CMake level.

.. note::
   Use the values we have for ruby and corona as guides, but adapt the
   partition, number of cpus per task and duration coherently with the machine.

Machines using LSF Spectrum scheduler
-------------------------------------

For machines using LSF Spectrum scheduler, use ``lassen`` as a starting point.
Then replace all the instances of "LASSEN" and "lassen" with the new machine
name.

Then go to ``customization/custom-variables.yml`` and add the variables:

* ``<MACHINE>_BUILD_AND_TEST_JOB_ALLOC`` with allocation options for any of the
  jobs the machine will take.

.. note::
   Use the values we have for lassen as guides, but adapt the partition and
   duration coherently with the machine.

Reference the new sub-pipeline
==============================

In ``customization/custom-pipelines.yml``, add a new section corresponding to
the new machine. This is used by ``customization/gitlab-ci.yml`` to control
which sub-pipelines are effectively generated.

Changelog
=========

Don’t forget to provide a quick description of your changes in the
``CHANGELOG.md``.

New tag
=======

Once the new machine setup is tested and valid, submit a PR. We will review it
and merge it. We may create a new tag although it is not required for a new
machine. Indeed, using a new machine is a voluntary change for users: they will
have to activate it in ``customization/custom-pipelines.yml`` the same way you
did above (which is a suggested template).

.. _Radiuss-Spack-Configs: https://github.com/LLNL/radiuss-spack-configs
