.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

.. _run_spack-label:

****************************************************************
Run Spack to install dependencies and configure a project build
****************************************************************

The first step in adopting RADIUSS Shared CI infrastructure is to run 
Spack to install your project dependencies and generate a *host-config*
file (i.e., CMake cache file) to build your project.
