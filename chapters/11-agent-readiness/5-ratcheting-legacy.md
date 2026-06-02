---
title: Ratcheting and Legacy
description: How brownfield repositories become agent-ready by improving monotonically rather than by rewriting
created: 2026-05-29
last_updated: 2026-06-01
tags: [agent-readiness, ratcheting, legacy, brownfield, characterization-tests]
part: 3
part_title: Perspectives
chapter: 11
section: 5
order: 3.11.5
---

# Ratcheting and Legacy

Most readiness advice assumes a greenfield repository where "full coverage" or "no file over 500 lines" is achievable. Most software is not greenfield. A fifteen-year codebase with twelve thousand files will never reach those absolutes, and waiting for a cleanup project that never gets authorized leaves the repository permanently un-ready. The principles do not change for legacy work, but three patterns dominate everything else.

The core shift is from rewriting to ratcheting: instead of verifying the whole world is clean, verify that this change did not make it worse. That single reframe makes every readiness principle achievable in a brownfield context.

---

## Ratcheting Beats Rewriting

A ratchet snapshots the repository's current state into a baseline file and fails continuous integration only on _regressions_ against that baseline. The repository then improves monotonically, getting only cleaner, without anyone authorizing a dedicated cleanup effort.

|               | Rewriting                                      | Ratcheting                                 |
| ------------- | ---------------------------------------------- | ------------------------------------------ |
| Target        | Absolute ideal (full coverage, no large files) | No regression against the current baseline |
| Authorization | Requires a funded cleanup project              | None; rides existing PRs                   |
| Risk          | Large, all-at-once, easy to stall              | Incremental, monotonic, hard to stall      |
| Legacy fit    | Effectively impossible at scale                | Works at any size                          |

The pattern applies to coverage floors, file-size caps, complexity thresholds, dead code, dependency counts, and even lint-rule adoption: allowlist the existing violations, block any new one. Ratcheting is the legacy equivalent of "verifiable in seconds": the check does not prove the whole codebase is clean, it proves this pull request did not degrade it.

A baseline ratchet is the practical form. The check derives its thresholds from the repository's current worst state rather than from an ideal, so it never manufactures failures on day one; it only catches the next regression.

---

## Slices, Not the Whole Repo

Verifiability in sixty seconds is impossible if the full test suite takes forty-five minutes. The reframe is that agents work on _slices_ that can be verified in sixty seconds, not on the whole repository at once. Platform work becomes "what is the smallest unit of this system that can be built, linted, and tested in isolation?"

Build-module boundaries and monorepo build tools exist largely to enable this slicing. For a polyglot codebase, the slices layer by language: each language gets its own self-description, its own verify command, and its own ratchet file. Unifying verification across languages is usually not worth the abstraction tax.

A repository's shape determines which signals even apply. A markdown-only repository with no runtime, for example, can still be made agent-ready by explicitly excluding the runtime signals that do not apply to it (tracing, metrics, alerting), so a readiness check does not penalize the absence of telemetry there is nothing to emit. Marking non-applicable signals as out of scope, with a stated rationale, prevents metric-gaming and keeps the readiness picture honest.

---

## Characterization Tests Before Refactor

The highest-leverage early use of agents in a legacy codebase is writing tests for code that has no tests, so that future edits become safe. A characterization test captures the _current_ behavior of code, correct or not, so that any change altering that behavior is caught. The technique predates agents by two decades, but it suits them well: an agent will patiently write two hundred boring test cases that a human would resent.

This turns a frightening refactor into a routine pull request. In a legacy context the test suite is not a quality gate layered on top; it _is_ the agent's memory of how the system is supposed to behave. An agent cannot hold a twelve-thousand-file system in context, so the tests stand in for the understanding the agent cannot load. Get the characterization suite working and most other readiness gains follow, because the verification surface finally exists.

**Sequence:**

1. Write characterization tests that pin current behavior.
2. Confirm they pass against the unmodified code.
3. Refactor with the tests as a regression net.
4. Update the tests only where behavior was deliberately changed.

---

## The Repo Describes Itself

Self-description carries extra weight in legacy work because the tribal knowledge it replaces is both deep and undocumented. A self-description file that states the setup command, the verify command, and the architecture lets an agent reach a running test suite without interviewing a senior engineer. The standards that file references should be mechanized as a check that fails the build, rather than aspirational prose, because mechanized standards are the ones agents reliably follow.

The self-description must be validated, or it decays into fiction. A check that confirms every path and command the file references still resolves keeps it honest; an unverified onboarding doc in a fast-moving repository is a liability, not an asset. This validation is itself a small ratchet: it does not demand the file be complete, only that what it claims remains true.

A legacy-specific honesty applies to the gains. The realistic twelve-month outcome for a long-lived monolith is not agents shipping features end to end; it is agents making engineers substantially faster on well-scoped work, with higher-quality pull requests because the verify loop is finally real. Most "AI readiness" gains in legacy are "engineering readiness" gains (pre-commit hooks, branch protection, ratcheted coverage, structured logging) that were good ideas long before agents. The agent angle is mostly a forcing function for overdue platform work.

---

## Anti-Pattern: Aggressive Thresholds on Day One

**What it looks like:** Adopting an ideal threshold (full coverage, a strict file-size cap) on a legacy repository and watching CI light up red across hundreds of pre-existing violations.

**Why it fails:** The build is now red for reasons unrelated to any current change, so the signal is useless and the team disables the check. The readiness gain is lost to noise.

**Better approach:** Baseline from the repository's current worst state and ratchet from there. The check stays green until something regresses, which keeps the signal meaningful and the check alive.

---

## Open Questions

- What is the brownfield ceiling on the environment dimension? Legacy constraints appear to cap effective agent autonomy below the level greenfield repositories reach, but the limiting factor is not precisely characterized.
- How should ratchet baselines be re-baselined? A monotonic ratchet improves slowly; when and how to tighten the baseline toward the ideal without manufacturing failures is unsettled.

---

## Connections

- **[Dimensions of Readiness](4-maturity-ladders.md):** Ratcheting is how a legacy repository raises its environment readiness without a rewrite; it is the mechanism behind monotonic improvement toward a self-improving state.
- **[Readiness Principles](3-readiness-principles.md):** Slicing implements "verifiability in seconds" for large repos, and self-description plus mechanized standards are principles 2 and 3 applied to brownfield work.
- **[Software Factories](../9-mental-models/7-software-factories.md):** The factory critique notes that the dramatic productivity claims are greenfield; ratcheting is the brownfield discipline that the factory model's headline numbers tend to omit.
- **[Context at Codebase Scale](../4-context/6-context-at-codebase-scale.md):** When an agent cannot hold a legacy system in context, the characterization test suite substitutes for the understanding that cannot be loaded.
