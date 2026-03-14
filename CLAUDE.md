# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

This is the **fm-development** repo — a single script for bootstrapping the Flexemarkets development environment. It is part of the `adhocmarkets` GitHub organization. There is no build system, test suite, or linter.

## fm-develop-setup

A unified CLI with subcommands:

- **(no args)** — Install toolchain (SDKMAN/Java/Maven, nvm/Node.js, Claude Code) and clone all repos.
- **repo install** — Clone FM repositories and configure fork remotes (upstream = org, origin = user fork).
- **repo update** — Fetch all remotes and pull latest code for all repositories.
- **database build** — Install build deps, clone/update PostgreSQL source, compile, and install binaries + contrib. Requires sudo.
- **database install** — Run initdb, configure trust auth, set up systemd service (Linux) or launchctl guidance (macOS), create roles (`jan`, `u4cv4dsie00kdu`) and the `flexemarkets` database. Requires sudo.
- **database load [-o OWNER] \<dir|files\>** — Load pg_restore backups. DB name derived from filename prefix (before first dash). Default owner: `u4cv4dsie00kdu`.
- **database delete** — Stop service, remove data directory, binaries, and source. Requires typing `yes` to confirm.

Global options: `-u`/`--user` (GitHub fork username), `--source` (PG source dir), `--prefix` (PG install prefix).

## Conventions

- Uses `set -euo pipefail` and `#!/usr/bin/env bash`.
- Operates relative to parent directory (`$SCRIPT_DIR/..`), not the current working directory.
- The `step()` function pattern (`step() { echo "==> $*"; }`) is used for progress output.
- Git remote convention: `upstream` = adhocmarkets org repo, `origin` = developer's personal fork.

## Related Repositories (cloned by repo install)

| Repo name | Local directory | Description |
|---|---|---|
| fm-data | fm-server | Server/backend |
| fm-ui | fm-ui | Frontend UI |
| fm-robots | fm-robots | Trading robots |
| fm-robots-container | fm-robots-container | Robot container (user fork only) |
| fm-sdk | fm-sdk | SDK |
| fm-administration | fm-administration | Administration tools |

## PostgreSQL Setup

- Default install prefix: `/usr/local/pgsql`
- Data directory: `/usr/local/pgsql/data`
- Socket directory: `/tmp`
- Connect with: `psql -h /tmp flexemarkets`
- Auth method: trust (local and localhost)
