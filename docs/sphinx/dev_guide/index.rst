.. ##
.. ## Copyright (c) 2022, Lawrence Livermore National Security, LLC and
.. ## other RADIUSS Project Developers. See the top-level COPYRIGHT file for details.
.. ##
.. ## SPDX-License-Identifier: (MIT)
.. ##

###############
Developer Guide
###############

For the most part, this project should not require too much additional
development. It consists in a CI configuration with entry points for projects
to customize it to their need. The project is light and does not require any
installation.

However, there are technical details that are worth understanding, e.g. why are
the jobs launched in sequential order and not in parallel, etc.

This section of the documentation illustrates some of those technical details.

.. toctree::
   :maxdepth: 2

   ci_setup_explained
