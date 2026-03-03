# PR: use mise for external dependencies

## Goal

Standardize the repository on one documented way to install and pin external
tools while keeping `uv` as the Python dependency workflow.

## Initial state

Before this branch, the repository mixed two different setup stories:

- ad hoc shell installers in `Makefile` and CI
- an undocumented `default.nix` shell that was not wired into the current
  contributor workflow

That made the external toolchain harder to reason about and left the project
with duplicated installation logic.

## Changes

This branch:

- introduces `mise.toml` and `mise.lock` as the source of truth for external
  tool versions
- rewrites `Makefile` setup and test commands to run through `mise exec`
- updates CI workflows to use `mise` instead of hand-installed tools
- updates contributor docs to describe the `mise + uv` workflow
- removes the stale `default.nix` shell

## Why remove `default.nix`

`default.nix` was no longer part of the documented or automated workflow. It
also referred to stale tooling like `poetry`, `black`, and a `pip`-prefix setup
that no longer matches the repository standard of `mise + uv + ruff`.

We still like and use Nix, but for this repository it had become an orphaned
second setup story rather than an integrated contributor path.

## Final state

After this branch:

- external tools are pinned in one place
- local development and CI use the same tool bootstrap path
- `Makefile` commands no longer carry bespoke installer logic
- contributor setup is simpler to explain and reproduce

## Validation

The branch updates both the local development workflow and the workflow files,
so the main value is consistency across `Makefile`, CI, and contributor docs.
