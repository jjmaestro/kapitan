# Combined PR for wip/2 and wip/2.1

## Scope

This document describes `wip/2-refactor-and-improve-testing` and
`wip/2.1-more-test-refactoring` as one logical PR.

Taken together, these branches do two related things:

- `wip/2` reshapes the test tree into a clearer, source-mirrored, better
  documented structure with stronger test lanes and full coverage
- `wip/2.1` follows through on that new structure by pushing fixtures closer to
  their consumers, making path handling more pytest-native, removing inherited
  helper abstractions, and splitting the `utils` source/tests cleanly

The combined result is the real intended test-suite refactor.

## High-Level Goal

The goal of the combined work is to turn the Kapitan test suite from a tree
that had grown over time into a suite with:

- clear structural boundaries
- a stronger source-to-test mapping
- explicit execution lanes
- stable randomized and parallel behavior
- more pytest-native fixtures and filesystem setup
- less hidden global state and cwd coupling
- a smaller, more intentional shared test framework

## Initial State

Before this combined work, the suite had several overlapping problems:

- top-level test responsibilities were less clearly separated
- unit, integration, fixture-project, and golden-data concerns were mixed more
  loosely
- source-to-test mirroring was incomplete
- helper code and fixture code had accumulated inherited abstractions
- the root `tests/conftest.py` had become a catch-all test framework
- compile-heavy tests depended too much on cwd and shared process state
- the suite mixed newer pytest-native path handling with older ad hoc temp and
  string-path patterns
- integration coverage around real workflows needed improvement
- deterministic random-order and high-coverage maintenance needed more explicit
  support

## What wip/2 Changes

`wip/2` provides the structural reset.

It:

- reorganizes the suite into clearer top-level areas:
  - `tests/unit/`
  - `tests/integration/`
  - `tests/resources/`
  - `tests/support/`
- mirrors the source tree more consistently under `tests/unit/kapitan/`
- consolidates non-Python test assets under `tests/resources/`
- introduces clearer shared path handling
- migrates legacy tests to pytest style
- improves integration coverage around `compile`, `refs`, and `lint`
- rewrites the Makefile test lanes to match the new suite structure
- makes markers and environment-coupled lanes more explicit
- hardens the suite for randomized and parallel execution
- raises and enforces the `100%` coverage contract

This branch is the big re-layout and stabilization pass.

## What wip/2.1 Changes

`wip/2.1` is the follow-through cleanup that makes the new layout feel
intentional instead of transitional.

It:

- distributes fixtures into subtree-local `conftest.py` files where possible
- moves shared cross-cutting fixtures into explicit support plugins
- shrinks the root `tests/conftest.py`
- replaces more cwd-heavy setup with explicit project-root and `tmp_path`
  patterns
- removes `CompileTestHelper`
- reduces `tests/support/helpers.py` to smaller pure helpers
- cleans up project-copy setup and temp-path handling
- removes the remaining `unittest`-style patch usage in tests
- splits `kapitan/utils.py` into the `kapitan/utils/` package
- mirrors that split in `tests/unit/kapitan/utils/`

This branch is the “make the new structure actually pleasant to work in” pass.

## Combined Final State

After both branches, the suite is in a materially better state:

- the top-level structure is clear
- the source-mirrored unit layout is real and much broader
- fixture projects and golden outputs live in a dedicated resources area
- test support code is more explicit and less ad hoc
- fixture ownership is closer to where fixtures are used
- root shared pytest machinery is smaller and easier to reason about
- compile-heavy tests are more explicit about project roots and output roots
- `tmp_path` / `Path` usage is much more consistent in the touched areas
- `CompileTestHelper` and `unittest` leftovers are gone
- `kapitan/utils.py` and its tests are no longer monoliths
- the suite keeps the `100%` coverage contract while being more maintainable

## Why The Combined PR Is Better Than Two Separate Stories

The two branches are understandable individually, but the most accurate story is
their combination:

- `wip/2` creates the new structure
- `wip/2.1` removes the biggest remaining structural debt inside that new
  structure

Without `wip/2.1`, `wip/2` still leaves too much shared machinery and inherited
helper behavior in place.

Without `wip/2`, `wip/2.1` does not make sense as a standalone refactor, since
it is clearly finishing and deepening the test-tree rewrite that `wip/2`
started.

Together they form one coherent test-suite modernization.

## Main Improvements

The combined work improves the suite along four axes:

### 1. Navigability

- easier to find relevant tests from production paths
- easier to find fixture projects, golden files, and helper modules
- clearer mental model for unit vs integration vs support vs resources

### 2. Execution clarity

- clearer Makefile test lanes
- clearer marker usage
- more explicit environment-coupled behavior
- stronger support for deterministic random-order and parallel runs

### 3. Test design quality

- more pytest-native setup
- less hidden cwd and global-state dependence
- fewer inherited helper abstractions
- cleaner source/test mirroring in one of the biggest previous hotspots

### 4. Maintenance

- smaller root shared machinery
- less duplicated setup logic
- better long-term base for later source-driven splits in other large modules

## Validation

This combined stack has been validated aggressively.

Notable checks:

- `wip/2`: `make test_python` across every commit in the rebased branch-local
  range
- `wip/2.1`: `make test_python` across every branch-local commit
- `wip/3`: `make test_python` across every branch-local commit
- `wip/4`: `make test_python` across every branch-local commit
- full numbered-stack commitlint check from `master..wip/4`

All of those checks pass on the current history.

## Still Intentionally Deferred

Even as a combined PR, this work intentionally leaves some follow-up items for
later:

- splitting larger core source modules beyond `kapitan/utils.py`
- splitting the largest remaining mirrored test modules only when those source
  modules are ready to split
- broader cleanup of formulaic test docstrings
- broader coverage-shape work where internal branch coverage can be replaced by
  stronger public-contract tests

Those are now narrower follow-up problems, not blockers to understanding or
maintaining the test tree.
