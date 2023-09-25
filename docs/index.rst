.. ## Copyright (c) 2019-2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

#################
RADIUSS Shared CI
#################

The RADIUSS Shared CI project provides a *templated CI infrastructure for
GitLab* that we intend to be sufficiently flexible and general to be adopted
and shared by any LLNL open-source projects hosted on GitHub.

The infrustructure is project-agnostic. Each machine we support has a templated
pipeline ready for use. Users only need to add jobs for machines of interest
and set some variables according to their needs.

Projects using the RADIUSS Shared CI infrastructure keep control other their
build and test process. The pre-requisite being that this process can be
invoked through a single command line.

A project is then built and tested as usual and *most of the CI infrastructure
is shared* to reduce duplicated effort and ease maintenance for individual
projects.

.. note:: LLNL's RADIUSS project (Rapid Application Development via an
   Institutional Universal Software Stack) aims to broaden usage across LLNL
   and the open source community of a set of libraries and tools used for HPC
   scientific application development.

========
Overview
========

The steps necessary to adopt the RADIUSS Shared CI methodology are documented in the :doc:`RADIUSS Shared CI User Guide <sphinx/user_guide/index>`.

In the  :doc:`RADIUSS Shared CI Developer Guide <sphinx/dev_guide/index>`,
we discuss the layout of the RADIUSS Shared CI infrastructure and how the
different pieces work together. Technical choices are also explained there.

=======================================
RADIUSS Shared CI User Documentation
=======================================

  * :doc:`RADIUSS Shared CI User Guide <sphinx/user_guide/index>`

============================================
RADIUSS Shared CI Developer Documentation
============================================

  * :doc:`RADIUSS Shared CI Developer Guide <sphinx/dev_guide/index>`

=========================
Background and Motivation
=========================

LLNL open-source projects are prevalently hosted on GitHub. However, for those
meant to run on Livermore Computing (LC) systems, testing on LC GitLab instance
on an automated manner is paramount.

Initially, the RADIUSS Shared CI infrastructure was designed for RADIUSS
projects also using Spack to describe the toolchain and build the dependencies.
This "build infrastructure" is defined in `RADIUSS Spack Configs`_
documentation.

===========================================
Cool features provided by RADIUSS Shared CI
===========================================

* A shared configuration, for reduced maintenance.
* Ability to define your jobs command and add as many jobs as desired.
* Uses GitLab child pipelines to keep the pipeline readable.
* Each child pipeline reports independently to GitHub.
* Project can extend their pipeline at will with additional stages.
* Print an exact reproducer in the logs.
* Optimize machine usage with shared allocation (optional).
* Filter out pipelines coming from a mirrored draft Pull Request (optional).

.. toctree::
   :hidden:
   :caption: User Documentation

   sphinx/user_guide/index

.. toctree::
   :hidden:
   :caption: Developer Documentation

   sphinx/dev_guide/index

.. _RADIUSS Spack Configs: https://radiuss-spack-configs.readthedocs.io/en/latest/index.html
