.. ##
.. ## Copyright (c) 2022-25, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for
.. ## details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _user_guide-label:

##########
User Guide
##########

The RADIUSS Shared CI project provides a *templated CI infrastructure for
GitLab* that we intend to be sufficiently flexible and general to be adopted
and shared by any LLNL open-source projects hosted on GitHub.

The infrastructure is project-agnostic. Each machine we support has a templated
pipeline ready for use. Users only need to add jobs for machines of interest
and set some variables according to their needs.

.. figure:: images/Shared-CI-Infrastructure.png
   :scale: 40 %
   :align: center

   RADIUSS Shared CI provides a templated CI infrastructure.
   Pipeline files are to be included remotely to the project CI configuration,
   while templated configuration files must be copied over and completed.

The figure illustrates the setup of a project using the RADIUSS Shared CI
Infrastructure.

In this documentation, we will describe how to set up RADIUSS Shared CI for
your project, including initial configuration and integration within your
repository.

.. toctree::
   :maxdepth: 2

   setup_ci

We also provide "How To" sections to perform various usage tasks.

.. toctree::
   :maxdepth: 2

   how_to

.. _RADIUSS Spack Configs: https://radiuss-spack-configs.readthedocs.io/en/latest/index.html
.. _radiuss-spack-configs: https://github.com/LLNL/radiuss-spack-configs
.. _radiuss-shared-ci: https://github.com/LLNL/radiuss-shared-ci
.. _Uberenv: https://github.com/LLNL/uberenv
.. _Spack: https://github.com/Spack/Spack
