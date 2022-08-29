# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a
Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Next

### Changed

- Update xl compiler
- Update cuda is some xl specs

## [v2022.08.1 - 2022-08-xx]

### Added

- Documentation regarding known issues.
- Report status for each sub-pipeline.
- Project can now specify global (custom) variants and dependencies.
- A How-To about global variants and dependencies.

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
