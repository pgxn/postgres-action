# GitHub Action to Install and Configure PostgreSQL

[![⚖️ PostgreSQL]][pg] [![🎬 Action]][action] [![🧪 Test]][ci]

This action sets up a PostgreSQL server on the GitHub runner VM to enable the
automated testing of PGXN extensions against multiple versions of PostgreSQL.
It currently supports:

*   Ubuntu Runners with PGDG-installed PostgreSQL 8.2-19
*   macOS Runners with Homebrew-installed PostgreSQL 14-18
*   Windows Runners with Chocolatey-installed PostgreSQL 10-18[^win-pgxs]

In addition, each provides the [pgxn client] to simplify installing additional
extension from [PGXN]. The Windows images also adds `sudo` to minimize
differences in the commands required to install extension on each OS.

We recommend also using [pgxn-matrix-action] to generate the test matrix.
Example workflow:

``` yaml
name: 🧪 Test
on:
  push:
defaults:
  run: { shell: bash }
jobs:
  matrix:
    # Use pgxn/matrix-action to generate OS/arch/Postgres version matrix.
    name: 📋 Generate Matrix
    runs-on: ubuntu-latest
    outputs:
      matrix: ${{ steps.matrix.outputs.matrix }}
    steps:
      - name: Generate Matrix
        id: matrix
        uses: pgxn/matrix-action@v0
        with:
          min-version: 12
          beta: true
          no-os: windows
  test:
    name: ${{ matrix.pg.emoji }} ${{ matrix.pg.os }}/${{ matrix.pg.arch }} 🐘 v${{ matrix.pg.version }}
    needs: matrix
    runs-on: ${{ matrix.pg.runner }}
    strategy:
      fail-fast: false
      matrix:
        pg: ${{ fromJson(needs.matrix.outputs.matrix) }}
    steps:
      - name: Check out the repo
        uses: actions/checkout@v7
      - name: Start Postgres ${{ matrix.pg.version }}
        uses: pgxn/postgres-action@v0
        with: { version: "${{ matrix.pg.version }}" }
      - name: Build
        run:  make
      - name: Install
        run:  sudo make install
      - name: Test
        id:   test
        run:  make installcheck
      - name: Show Diffs
        if:   failure() && steps.test.outcome == 'failure'
        run:  find . -name regression.diffs -exec cat {} +
```

## Input Parameters

This action takes the following parameters:

| Key        | Type    | Default  | Description                                            |
| ---------- | ------- | -------- |------------------------------------------------------- |
| `version`  | string  | ""       | PostgreSQL major version to install                    |
| `port`     | integer | 55432    | Port on which PostgreSQL should listen for connections |
| `packages` | string  | ""       | List of additional OS-specific packages to install     |
| `start`    | boolean | true     | Start the PostgreSQL server after installing           |
| `encoding` | string  | ""       | The encoding to use for databases in the cluster       |
| `locale`   | string  | ""       | The locale to use for databases in the cluster         |
| `config`   | string  | ""       | [Server configuration] to append to `postgresql.conf`  |

The `locale`, `encoding`, and `config` inputs have no effect if `start` is
false.

Use block scalar syntax for multi-line [server configuration] in `config`,
e.g.:

```yaml
config: |
  timezone = 'Asia/Tokyo'
  statement_timeout = 5400
```

For the `packages` input, use package names specific to the OS packaging
system:

*   Linux: [Debian Packages]
*   macOS: [Homebrew Formulae]
*   Windows: [Chocolatey Packages]

Setting `start` can be handy to eliminate the overhead of initializing a
cluster and starting Postgres if you don't need a running server. It can also
be useful to defer startup until some other step has been taken; simply use
the action a second time. For example, to build and install an extension, then
add it to `shared_preload_libraries` when starting Postgres:

``` yaml
jobs:
  test:
    steps:
      - name: Check out the repo
        uses: actions/checkout@v7
      - name: Install Postgres ${{ matrix.pg.version }}
        uses: pgxn/postgres-action@v0
        with:
          version: ${{ matrix.pg.version }}
          start: false
      - name: Build
        run:  make
      - name: Install
        run:  sudo make install
      - name: Start Postgres ${{ matrix.pg.version }}
        uses: pgxn/postgres-action@v0
        with:
          version: ${{ matrix.pg.version }}
          config: shared_preload_libraries = 'my_extension'
      - name: Test
        id:   test
        run:  make installcheck
```

## Environment Variables

On completion, this action sets the following environment variables:

| Variable    | Value        | Description                                        |
| ----------- | ------------ | -------------------------------------------------- |
| `PGUSER`    | "postgres"   | The name of the PostgreSQL super user              |
| `PGPORT`    | `input.port` | The port on which the PostgreSQL server listens    |
| `PG_CONFIG` | varies       | The path to `pg_config`, used to build extensions  |
| `PGDATA`    | varies       | The path to the cluster data directory             |

## Path

On completion, this action adds the path to the PostgreSQL executables to the
`PATH` environment variable, so they can be called without needing to know the
full, often version-specific path.

## Prior Art/Inspirations

*   [pgxn-tools]: Old PGXN Linux/amd64-only OCI image for testing extensions
*   [petere/pguint]: Commit converting to GitHub actions using `apt.postgresql.org`
*   [ikalnytskyi/action-setup-postgres]: Setup PostgreSQL for Linux, macOS and
    Windows runner machines

  [^win-pgxs]: Although currently the standard `include $(PGXS)` pattern in
    `Makefiles` appears to work only on Postgres 17 and later, because
    `pg_config --pgxs` returns a path with spaces in it on earlier versions.

  [⚖️ PostgreSQL]: https://img.shields.io/badge/License-PostgreSQL-blue.svg "⚖️ PostgreSQL License"
  [pg]: https://opensource.org/license/postgresql "⚖️ PostgreSQL License"
  [🧪 Test]: https://github.com/pgxn/postgres-action/actions/workflows/test.yml/badge.svg "🧪 Test Status"
  [ci]: https://github.com/pgxn/postgres-action/actions/workflows/test.yml "🧪 Test Status"
  [🎬 Action]: https://img.shields.io/badge/Marketplace-Action-orange.svg "[🎬 Marketplace Action]"
  [action]: https://github.com/marketplace/actions/pgxn-postgres-action "[🎬 Marketplace Action]"
  [Debian Packages]: https://packages.debian.org/index
  [Homebrew Formulae]: https://formulae.brew.sh
  [Chocolatey Packages]: https://community.chocolatey.org/packages/
  [pgxn client]: https://pgxn.github.io/pgxnclient/
  [PGXN]: https://pgxn.org/ "PostgreSQL Extension Network"
  [pgxn-matrix-action]: https://github.com/pgxn/matrix-action/
    "Generate a matrix of Postgres versions on Workflow OSes and architectures"
  [pgxn-tools]: https://github.com/pgxn/docker-pgxn-tools/ "Test image for PostgreSQL & PGXN extensions"
  [petere/pguint]: https://github.com/petere/pguint/commit/bcc3335
    "petere/pguint@bcc3335 Convert CI from Cirrus to GitHub Actions"
  [ikalnytskyi/action-setup-postgres]: https://github.com/ikalnytskyi/action-setup-postgres/
    "Setup a PostgreSQL for Linux, macOS and Windows runner machines"
  [Server configuration]: https://www.postgresql.org/docs/current/runtime-config.html
    "PostgreSQL Docs: Server Configuration"
