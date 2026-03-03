# PR: make mike preview address configurable

## Goal

Make local documentation preview easier to use when the default port is already
occupied.

## Initial state

Before this branch, `make docs_serve` assumed `localhost:8000`. That worked for
the happy path, but it made the docs loop awkward whenever that port was in use
or when a contributor wanted to run multiple local services at once.

The docs guidance also still described an older flow instead of the current
`mike`-based preview workflow.

## Changes

This branch:

- adds `DOCS_DEV_ADDR ?= localhost:8000` to `Makefile`
- makes `make docs_serve` pass that value through to `mike serve`
- updates the command output to show the effective preview address
- refreshes contributor docs to describe `make docs_serve`
- documents how to override the preview address from the command line

## Final state

After this branch, documentation preview is still simple by default, but it is
no longer locked to a single port.

That makes the local docs workflow more practical for contributors without
changing the default behavior for anyone who is happy with `localhost:8000`.

## Validation

The change is intentionally small and localized: one Makefile knob plus docs
updates that explain how to use it.
