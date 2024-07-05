# TestCoverage

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.2.0] - 2024-07-05

### Added
- #14: Added a straightforward way to find and track coverage on all interoperability processes in the current namespace ([#14](https://github.com/intersystems/TestCoverage/issues/14))

## [3.1.0] - 2024-01-17

### Changed
- #23: Allow CoverageClasses and CoverageRoutines to be specified as %DynamicArray in addition to $ListBuild() lists.

### Fixed
- #24: Whenever a new tag is created, a new release will be published using the tag string as its version. The release also comes with an export of the TestCoverage package in XML.

## [3.0.0] - 2023-12-01

### Changed
- #25: As a consequence of this change, the minimum supported platform version is 2019.1.

### Fixed
- #18: EOL normalization in coverage.list
- #19: Update CI to latest IRIS community (and corresponding test updates)
- #25: Fix so the tool works on 2023.1

## [2.1.3] - 2022-03-30
- Last released version before CHANGELOG existed.