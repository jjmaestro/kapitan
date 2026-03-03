# PR: simplify GitHub Actions runner configuration

## Goal

Make the Ubuntu runner choice in the main CI workflow easier to understand and
change without leaving misleading duplicated configuration behind.

## Initial state

Before this branch, the workflow had an `UBUNTU_VERSION` variable, but it was
not the real single source of truth:

- `setup.runs-on` was still hard-coded
- the platform matrix still carried hard-coded runner labels
- the surrounding comment suggested a one-knob update path that did not really
  exist

That made future runner-version bumps more error-prone than the file implied.

## Changes

This branch:

- replaces the old `UBUNTU_VERSION` plus hard-coded platform blob with
  `DEFAULT_RUNNER`
- derives the build-matrix runner labels from that default in the setup step
- keeps the setup job `runs-on` explicit, with a comment explaining the GitHub
  Actions limitation around `env` in job-level `runs-on`
- updates the workflow README to reflect the new runner setup

## Final state

After this branch, the workflow is still constrained by GitHub Actions syntax,
but the file now reflects that constraint honestly:

- one default runner value drives the platform matrix
- the one remaining hard-coded `runs-on` is documented as a platform
  limitation, not hidden duplication

## Validation

The workflow change was kept small enough to stay easy to reason about and was
validated with the workflow linter while amending the branch.
