# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

This is the **fm-development** repo — a single script for bootstrapping the Flexemarkets development environment. It is part of the `adhocmarkets` GitHub organization. There is no build system, test suite, or linter.

## fm-develop-setup

A unified CLI with subcommands:

- **(no args)** — Install toolchain and clone all repos (runs toolchain + `repo install`).
- **repo install** — Clone FM repositories and configure fork remotes (upstream = org, origin = user fork).
- **repo update** — Fetch all remotes and pull latest code for all repositories.
- **repo remotes** — Show the configured remotes for all repositories.
- **aliases** — Wire the FM application aliases into the shell rc file. Appends a `source` line pointing at `fm-robots/etc/fm/fm-aliases.sh`, which defines one alias per `~/.fm/fm-*.jar` (`fm-server`, `fm-robots-server`, `fm-maker`, …) plus `fm` → `fm-manager`. Sourced by path, not copied, so alias changes from `repo update` take effect on the next shell, and newly built jars appear without re-running anything. Idempotent.
- **pg build** — Install build deps, clone/update PostgreSQL source, compile, install binaries + contrib, and configure PATH. Requires sudo.
- **pg install** — Run initdb, configure trust auth, set up systemd service (Linux) or launchctl guidance (macOS), create roles (`jan`, `u4cv4dsie00kdu`) and the `flexemarkets` database. Requires sudo.
- **pg load [-o OWNER] \<dir|files\>** — Load pg_restore backups. DB name derived from filename prefix (before first dash). Default owner: `u4cv4dsie00kdu`.
- **pg delete** — Stop service, remove data directory, binaries, and source. Requires typing `yes` to confirm.

The PostgreSQL group accepts `pg`, `postgresql`, or `database` as the command word; `pg` is what the script's own `--help` advertises.

Global options: `-u`/`--user` (GitHub fork username), `--source` (PG source dir), `--prefix` (PG install prefix).

## Toolchain

Installed by the default command (no args):
- **SDKMAN** — Java (latest) and Maven
- **nvm** — Node.js (latest LTS)
- **Claude Code** — via npm
- **Heroku CLI** — via official standalone installer (not npm; the npm package is outdated)

## Conventions

- Uses `set -euo pipefail` and `#!/usr/bin/env bash`.
- Operates relative to a resolved source root, not the current working directory. The root is the script's own directory when the script sits loose in the root, or its parent when the script is run from inside the `fm-development` checkout. `FM_SRC_DIR` overrides both.
- The `step()` function pattern (`step() { echo "==> $*"; }`) is used for progress output.
- Git remote convention: `upstream` = the org repo, `origin` = developer's personal fork. The org is per-repo (the 4th `REPOS` field), not a global constant — most repos are under `adhocmarkets`, but `fm-sdk` is under `flexemarkets`.
- Organization affects the clone URL only. The local layout is flat: every repo sits directly under the source root, with no per-org directories.
- PostgreSQL prefix variable is `PG_PREFIX`, not `PREFIX` — nvm conflicts with `PREFIX` env var.

## Related Repositories (cloned by repo install)

| Repo name | Org | Local directory | Description |
|---|---|---|---|
| fm-data | adhocmarkets | fm-server | Server/backend |
| fm-ui | adhocmarkets | fm-ui | Frontend UI |
| fm-robots | adhocmarkets | fm-robots | Trading robots |
| fm-robots-container | adhocmarkets | fm-robots-container | Robot container (user fork only) |
| fm-sdk | flexemarkets | fm-sdk | SDK |
| fm-administration | adhocmarkets | fm-administration | Administration tools |
| fm-development | adhocmarkets | fm-development | This repo — the development setup script |

## PostgreSQL Setup

- Default install prefix: `/usr/local/pgsql` (binaries at `/usr/local/pgsql/bin`)
- Data directory: `/usr/local/pgsql/data`
- Socket directory: `/tmp`
- Connect with: `psql -h /tmp flexemarkets`
- Auth method: trust (local and localhost)
- PATH configured in `~/.bashrc` (falling back to `~/.profile` if absent) by `database build` and `database install`. `~/.profile` alone is not enough: it is read only by login shells, so a normal terminal never picks up `/usr/local/pgsql/bin`.
