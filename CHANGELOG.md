# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a
Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Next]

### Added

- A job that tests whether the machine is up, and reports the failure to GitHub otherwise.

### Changed

- Speed-up CI by not cloning the project in jobs only reporting to GitHub.
- Improved documentation, fixed links and syntax.
- BREAKING: now require to specify GitHub project name and organization for status reports.

### Fixed

## [v2022.09.0 - 2022-09-09]

### Added

- New HowTo articles in documentation

### Changed

- Modified file layout to work around issue with variable override
- Update xl compiler
- Update cuda is some xl specs

### Fixed

- Coherent job naming

## [v2022.08.1 - 2022-08-xx]

### Added

- Documentation regarding known issues.
- Report status for each sub-pipeline.
- Project can now specify global (custom) variants and dependencies.
- A How-To about global variants and dependencies.
- A How-To about choosing a spack reference in Uberenv.

### Changed

- Slurm allocation now uses command line option on project side (--overlap)
  instead of environment variable (SLURM_OVERLAP) in shared CI.
- Corona now uses Flux scheduler.

### Fix

- Wrong defaults in custom-variables file.
- Sub-pipeline status report works around Gitlab issue 216629.
- Corona used to include ruby allocation options.

## [v2022.08.0 - 2022-08-03]

### Changed

- Use protected double quotes in Spack specs, see https://github.com/LLNL/uberenv/pull/84.
- Dropped intel 18 compiler.

## [v2022.06.0 - 2022-06-13]

### Added

- Provide configuration for ruby, lassen and corona.

### Documentation

- Provide a first version of the documentation with user and developer guides.
