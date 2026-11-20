# Changelog

All notable changes to this project will be documented in this file. It uses the
[Keep a Changelog] format, and this project adheres to [Semantic Versioning].

  [Keep a Changelog]: https://keepachangelog.com/en/1.1.0/
  [Semantic Versioning]: https://semver.org/spec/v2.0.0.html
    "Semantic Versioning 2.0.0"

## [v0.3.3] — 2026-11-20

### 🪲 Bug Fixes

*   Added commands to update the Homebrew and Chocolatey installs before
    installing Postgres, allowing Postgres 19 to work on launch day on
    Homebrew (sorry to be a day late in the release!), and for the latest
    version to always be used when available.

  [v0.3.3]: https://github.com/pgxn/postgres-action/compare/v0.3.2...v0.3.3

## [v0.3.2] — 2026-11-19

### 🪲 Bug Fixes

*   Fixed installing Postgres 19 so that it no longer fails with the error
    `'resolute-pgdg-snapshot' is invalid for APT::Default-Release` by first
    running `apt.postgresql.org.sh -y`, then re-running
    `apt-get install -y postgresql-common`, then finally installing the
    specified version with `apt.postgresql.org.sh -i -v $version`. Thanks
    to PGDG Apt maintainer Christoph Berg for this workaround.

  [v0.3.2]: https://github.com/pgxn/postgres-action/compare/v0.3.1...v0.3.2


## [v0.3.1] — 2026-11-17

### ⚡ Improvements

*   Added a PostgreSQL server log file and added the `PGLOGFILE` environment
    variable to exports.
*   Added `-A trust` to the `initdb` calls to suppress the warning about
    defaulting to trusted access.
*   Moved the `PGDATA` path on Linux to the same location as on macOS and
    Windows: `$RUNNER_TEMP/pgdata$version`.
*   Added the `-w` option to the `pg_ctl` calls and a conditional to `cat` the
    lot file if Postgres fails to start.

### 📚 Documentation

*   Marked the `version` variable as required in the [README].

  [v0.3.1]: https://github.com/pgxn/postgres-action/compare/v0.3.0...v0.3.1

## [v0.3.0] — 2026-10-31

The theme of this release is *Defaults.*

### ⚡ Improvements

*   Added a test for and documented using `pgxn/postgres-action` twice: once
    with `start: false`, performing another task such as compiling an
    extension, then starting Postgres. the example includes the compiled
    extension in `shared_preload_libraries` when starting.
*   Added the `PGDATA` environment variable to exports.

### 🪲 Bug Fixes

*   Reverted the default superuser name to "postgres", as it's typically
    expected by various tooling and is the name of the default database.
*   Removed the `owner` input; it was superfluous. The current user (`runner`)
    now always owns the cluster in `$PGDATA`.

  [v0.3.0]: https://github.com/pgxn/postgres-action/compare/v0.2.0...v0.3.0

## [v0.2.0] — 2026-10-31

The theme of this release is *Ease of use.*

### ⚡ Improvements

*   Now creates a cluster owned by the system current user rather than
    "postgres".
*   Added the `owner` input; set to "postgres" to restore cluster ownership to
    the "postgres" system user.
*   Added the `config` input to append [server configuration] to
    `postgresql.conf` before starting the server.

### 📚 Documentation

*   Fixed typos in the [README].

  [v0.2.0]: https://github.com/pgxn/postgres-action/compare/v0.1.3...v0.2.0
  [README]: README.md
  [server configuration]: https://www.postgresql.org/docs/current/runtime-config.html
    "PostgreSQL Docs: Server Configuration"

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
