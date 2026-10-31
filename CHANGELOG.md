# Changelog

All notable changes to this project will be documented in this file. It uses the
[Keep a Changelog] format, and this project adheres to [Semantic Versioning].

  [Keep a Changelog]: https://keepachangelog.com/en/1.1.0/
  [Semantic Versioning]: https://semver.org/spec/v2.0.0.html
    "Semantic Versioning 2.0.0"

## [v0.2.0] — Unreleased

### ⚡ Improvements

*   Now creates a cluster owned by the system current user rather than
    "postgres".
*   Added the `owner` input; set to "postgres" to restore cluster ownership to
    the "postgres" system user.

### 📚 Documentation

*   Fixed typos in the [README].

  [v0.2.0]: https://github.com/pgxn/postgres-action/compare/v0.1.3...v0.2.0
  [README]: README.md

## [v0.1.3] — 2026-10-18

The theme of this release is *Welcome to the Matrix.*

### ⚡ Improvements

*   Switched to [pgxn/matrix-action] to generate the matrix of Postgres
    versions to start in both the [README] and the test workflow.

  [v0.1.3]: https://github.com/pgxn/postgres-action/compare/v0.1.2...v0.1.3
  [pgxn/matrix-action]: https://github.org/pgxn/matrix-action
  [README]: README.md

## [v0.1.2] — 2026-10-17

The theme of this release is *Disable main.*

### ⚡ Improvements

*   Disabled creation of the "main" database cluster, which which is not used,
    saving a few seconds from the Postgres installation on Linux.

  [v0.1.2]: https://github.com/pgxn/postgres-action/compare/v0.1.1...v0.1.2

## [v0.1.1] — 2026-10-16

The theme of this release is *No such file or directory.*

### 🪲 Bug Fixes

*   Fixed a bug where Postgres could fail to start because
    `/etc/postgresql-common/createcluster.conf` was missing.

### 📚 Documentation

*   Added missing `install` after `sudo make` to the [README].

  [v0.1.1]: https://github.com/pgxn/postgres-action/compare/v0.1.0...v0.1.1
  [README]: README.md

## [v0.1.0] — 2026-09-16

The theme of this release is *Simplify, simplify.*

### ⚡ Improvements

*   First release
*   Supports Linux, macOS, and Windows GitHub runners
*   Allows custom port, encoding, locale
*   Optionally install additional OS-specific packages.

### 🏗️ Build Setup

*   Made it a GitHub Action!

### 📚 Documentation

*   Wrote a [README]

  [v0.1.0]: https://github.com/pgxn/postgres-action/compare/e284f8d...v0.1.0
  [README]: README.md
