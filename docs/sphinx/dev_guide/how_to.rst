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

.. _add-a-new-machine:

=================
Add a new machine
=================

Adding a new machine can be done directly in this project so that the
configuration is shared with all.

The sub-pipeline definition
===========================

To add a new machine, create a corresponding ``pipelines/<machine>.yml`` file
describing the sub-pipeline. There are two main cases: whether the machine uses
Slurm/Flux or LSF Spectrum as a scheduler.

Machines using Slurm/Flux scheduler
-----------------------------------

For machines using Slurm (or Flux) scheduler, use ``ruby`` (or ``corona``) as a
starting point. Then replace all the instances of "RUBY" and "ruby" with the
new machine name.

Then go to ``customization/custom-jobs-and-variables.yml`` and add the
variables:

* ``<MACHINE>_SHARED_ALLOC`` with allocation options sufficient for the shared
  allocation (``salloc``, ``flux --parent alloc``) to cover for all the jobs.
* ``<MACHINE>_JOB_ALLOC`` with allocation options for any of the
  jobs (srun) the machine will take. The job will run under the shared
  allocation. Lastly, do not prescribe a number of cores here, as they should
  be defined at Spack and Make/CMake level.

.. note::
   Use the values we have for ruby and corona as guides, but adapt the
   partition, number of cpus per task and duration coherently with the machine.
   The job allocation specied should cover for most job. If you have only a few
   jobs that need longer allocation, you should override them in your local
   jobs and set specific allocation options for them.

Machines using LSF Spectrum scheduler
-------------------------------------

For machines using LSF Spectrum scheduler, use ``lassen`` as a starting point.
Then replace all the instances of "LASSEN" and "lassen" with the new machine
name.

Then go to ``customization/custom-jobs-and-variables.yml`` and add the
variables:

* ``<MACHINE>_JOB_ALLOC`` with allocation options covering for any of the
  jobs the machine will take.

.. note::
   Use the values we have for lassen as guides, but adapt the partition and
   duration coherently with the machine.

Reference the new sub-pipeline
==============================

In ``customization/subscribed-pipelines.yml``, add a new section corresponding
to the new machine. This is used by ``customization/gitlab-ci.yml`` to control
which sub-pipelines are effectively generated.

Changelog
=========

Don’t forget to provide a quick description of your changes in the
``CHANGELOG.md``.

New tag
=======

Once the new machine setup is tested and valid, submit a PR. We will review it
and merge it. We may create a new tag although it is not required for a new
machine. Indeed, using a new machine is a opt-in change for users: they will
have to activate it in ``customization/subscribed-pipelines.yml`` the same way
you did above (which only is a suggested template).

