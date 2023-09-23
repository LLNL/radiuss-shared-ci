.. ##
.. ## Copyright (c) 2022-23, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for
.. ## details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _user_how_to-label:

***********
How To...
***********

This section describes how to perform various maintenance tasks once you have
the RADIUSS Shared CI framework set up for your project.


.. _update-shared-ci:

===========================
Update the CI configuration
===========================

Updating RADIUSS Shared CI
==========================

Updating RADIUSS shared CI is straightforward. the Shared CI version to use is
defined by a the reference (``ref:`` section) in the remote includes. We
recommend using a variable to set this reference only once, and then update
radiuss-shared-ci easily.



.. _radiuss-spack-configs: https://github.com/LLNL/radiuss-spack-configs
.. _Uberenv: https://github.com/LLNL/uberenv
.. _Spack: https://github.com/spack/spack
