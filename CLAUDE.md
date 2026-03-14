# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

This is the **fm-development** repo — a collection of shell scripts for bootstrapping the Flexemarkets development environment. It is part of the `adhocmarkets` GitHub organization. There is no build system, test suite, or linter — just standalone bash scripts.

## Scripts

- **fm-development-setup** — Clones all FM repositories (fm-data/fm-server, fm-ui, fm-robots, fm-sdk, fm-administration) from adhocmarkets, sets up fork-based git remotes (upstream = org, origin = user fork), and installs toolchain dependencies (SDKMAN for Java/Maven, nvm for Node.js, Claude Code).
- **fm-database-build** — Builds PostgreSQL from source, runs initdb, configures trust auth, sets up a systemd service (Linux) or gives launchctl guidance (macOS), and creates the `flexemarkets` database with roles `jan` and `u4cv4dsie00kdu`. Requires sudo.
- **fm-database-load** — Loads pg_restore backup files. Database name is derived from the filename prefix (before the first dash). Default owner is `u4cv4dsie00kdu`.

## Conventions

- All scripts use `set -euo pipefail` (or should — fm-database-load currently uses plain `#!/bin/bash`).
- Scripts operate relative to their parent directory (`$SCRIPT_DIR/..`), not the current working directory.
- The `step()` function pattern (`step() { echo "==> $*"; }`) is used for progress output.
- Git remote convention: `upstream` = adhocmarkets org repo, `origin` = developer's personal fork.

## Related Repositories (cloned by fm-development-setup)

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
