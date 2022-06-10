.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

#################
RADIUSS Shared CI
#################

A shared GitLab CI implementation for RADIUSS projects using Spack.

Radiuss-Shared-CI is a sub-project from the RADIUSS initiative providing a
shareable template of CI implementation designed to run on the Livermore
Computing (LC) GitLab instance.  By sharing the CI definition, projects share
the burden of maintaining it. In addition, with Radiuss-Shared-CI, they share a
core set of toolchains (spack specs) to ensure that they keep running tests
with similar configurations.

LLNL's RADIUSS project (Rapid Application Development via an Institutional
Universal Software Stack) aims to broaden usage across LLNL and the open source
community of a set of libraries and tools used for HPC scientific application
development.


=========================
Background and Motivation
=========================

Projects belonging to the RADIUSS scope are targeting the same machines and
use Spack as a packaging system. We want them to ensure they build with
similar toolchains.

Sharing the CI framework started with `sharing spack configuration files`_. In
order to avoid duplication, we now also share most of the CI implementation
itself.

By externalizing the CI configuration, we create the need for an interface.
We try to keep this interface minimalistic, while allowing customization.

.. toctree::
   :hidden:
   :caption: User Documentation

   sphinx/user_guide/index

.. toctree::
   :hidden:
   :caption: Developer Documentation

   sphinx/dev_guide/index

.. _sharing spack configuration files: https://github.com/LLNL/radiuss-spack-configs
