---
title: From Factory to Institution
description: A forward-looking proposal for a sixth layer that feeds post-merge outcomes back into intent-formation
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-state, judgment, evaluation, speculative, autonomy]
part: 3
part_title: Perspectives
chapter: 12
section: 5
order: 3.12.5
---

# From Factory to Institution

> **This section is forward-looking and speculative.** Unlike the preceding sections, which describe state infrastructure that exists and runs, the ideas here are proposals and open questions about what a _sixth_ layer of agent state might look like. They are unproven, framed as hypotheses to test rather than established practice, and the "## Open Questions" section at the end is the honest summary of how much remains undecided.

The five-layer model describes a system that executes work reliably across a long horizon. The [Software Factories](../9-mental-models/7-software-factories.md) mental model names what such a system becomes at scale: a factory that turns intent into merged work through sandboxed agents, with quality gates checking correctness at every step. This section asks what comes _after_ the factory—and argues the answer is a missing layer.

---

## The Argument: A Factory Is Open-Loop on Value

A factory optimizes throughput of a known-good artifact. In an agent factory, the artifact is the merged change, and every quality gate—review, tests, the merge checkpoint—verifies **correctness**. The composition loop of the previous sections ends at "the change merges, the next agent inherits the memory." Removing humans from the execution floor makes the factory faster, but it does not change what the factory checks.

The gap is that **no layer checks whether the work was worth doing**. Nothing observes whether a merged change shipped value, was reverted a week later, moved a metric, or whether subsequent work built on it versus routed around it. A factory in this state can execute the wrong roadmap flawlessly and indefinitely. It is **closed-loop on correctness and open-loop on value**.

That gap names the next jump. A factory executes the order book; an **institution decides the order book and learns from the market**. Bridging the two requires a layer the five-layer model does not have:

> **Layer 6 — Judgment / Evaluation (proposed):** outcomes feed back into intent-formation. The system observes what happened _after_ a change merged, forms a sense of what is worth doing, and that sense shapes the next plan.

If the proposal holds, the headline metric flips. A factory is measured by **cost per merged change**; an institution would be measured by the **rate of improvement in its own judgment**. Stated compactly: the factory amortizes human _labor_ across many runs; the institution would amortize human _taste_ across many runs. Taste, in this framing, is captured cheaply as small curation acts, accumulated durably in outcome-weighted memory, and re-applied automatically when forming the next intent.

---

## Proposal: A Three-Tier Value Signal

For Layer 6 to close the loop, "value" needs a signal the system can read. The proposal is three tiers, each with a different cost and reliability profile:

| Tier                           | Signal                                                                                 | Cost                             | Risk                                                                    |
| ------------------------------ | -------------------------------------------------------------------------------------- | -------------------------------- | ----------------------------------------------------------------------- |
| 1. Telemetry                   | Change merged, reverted within N days, downstream work built on it or routed around it | Free, from git and the event log | Noisy and gameable—optimizing it rewards small, safe, low-value changes |
| 2. Human ratification          | A human marks the outcome of a unit of work as success, partial, or failure            | One human act per unit           | Ground truth, but rate-limited by human attention                       |
| 3. Executable success_criteria | Some criteria become re-runnable predicates the system checks after merge              | Authoring cost up front          | The real closed loop—but only as good as the predicate                  |

The positions that follow from this structure are specific. **Tier 1 is bait**: optimized alone, it drives agents toward trivially safe changes. **Tier 3 is the real control loop**: a coverage ratchet, a latency budget, or a readiness check is already a re-runnable predicate, and a unit of work with no measurable criterion is one the system cannot learn from. **Tier 2 anchors tier 1** against gaming by supplying a ground-truth label that telemetry alone cannot.

---

## Proposal: Autonomy Gated by Blast Radius × Reversibility × Novelty

If outcomes can be measured, some work can run with less human involvement. The proposed governing rule is that autonomy is gated not by abstract importance but by **blast radius × reversibility × novelty**:

- **Low blast radius, reversible, recurring** — for example, fifteen runs hit the same memory gap, so the fix is to add the missing record. Telemetry detects the pattern, an agent fixes it, and a predicate confirms the signal went away. A human need not be in the loop.
- **High blast radius, irreversible, novel** — architectural shifts, large refactors, anything that changes the fundamental shape of the system. A human ratifies before it ships.

A subtle inversion sits inside this rule. At high event volume, human-only review is already blind—failure modes accumulate faster than any human can inspect them. Under that condition, **telemetry-gated autonomy for the low-stakes tier is a safety instrument, not a risk**, because it puts a sensor on work no human was watching anyway. The license for an agent to run unattended is an executable predicate that closes the loop; unmeasurable work routes back to human attention. Measurable work thereby earns the right to run with less supervision, and each human ratification labels a bucket as safe-or-not, training the boundary to move outward per domain over time.

---

## Proposal: Canvas, Not Form

Today's intent capture asks a human to fill structured fields—goal, non-goals, constraints, criteria. That extracts structuring labor _from_ the human. The proposed inversion flips which side produces the structure:

```text
Today (form):
  Human fills structured fields  →  system executes

Proposed (canvas):
  Human brain-dumps freely
        │
        ▼
  An agent distills structure       (goal, constraints, candidate predicates)
        │                           as a derived, editable projection
        ▼
  Human corrects the distillation   ← the correction is the signal
        │
        ▼
  System executes
```

The schema does not disappear; _producing_ it becomes the system's job. The valuable part is the correction step. When a human fixes the agent's distillation of their intent, that correction captures the gap between what was said and what was meant—a top-tier taste signal that a blank form cannot collect. The canvas is, in this view, a better sensor for taste than a form, because it records the human's judgment rather than just their input.

---

## Open Questions

This entire section is a set of hypotheses. The genuine uncertainties:

- **Is value checkable without a human at all?** The working position is that human ratification stays permanently load-bearing for high-stakes work, and telemetry stands alone only for the low-stakes, recurring tier. Whether that boundary is stable or merely a current limitation is unresolved.
- **Does forcing intent toward measurable predicates cage the most valuable work?** The most valuable work is often the least measurable. The tentative answer—predicates _license_ autonomy while unmeasurable work routes to human attention—may prove too rigid in practice.
- **Is the durable unit the work item or the intent-shape?** If intent is the atom, the learnable thing may be the reusable, outcome-weighted intent-shape, with each unit of work as an instance. This is unsettled.
- **Can outcome-weighted memory avoid Goodhart effects?** Weighting memory by past outcomes risks reinforcing whatever the telemetry happened to reward. Whether tier-2 ratification is enough of an anchor is an open question.
- **Does git-native storage survive the judgment layer?** Outcome data and frequently-edited intent may strain the "everything lives in git" principle—reopening a unit of work to add one sentence should not require a commit. A database that _syncs_ with git, treating git as the eventual durable home rather than the only home, is a proposed but undecided departure.
- **Where does cross-project judgment live?** Extending the loop above a single repository—so judgment formed in one project informs intent in another—needs a coordination substrate above the per-project one. Whether that is a shared umbrella repository or an external index is unresolved.

---

## Connections

- **[Software Factories](../9-mental-models/7-software-factories.md):** This section is the "what comes next" extension of that mental model. The factory executes the order book; the proposed Layer 6 is the attempt to also decide and learn from it—closing the value loop the factory leaves open.
- **[The Composition Loop](2-composition-loop.md):** Layer 6 would extend the loop past "merge → next agent inherits memory" to "merge → observe outcome → reshape intent."
- **[Memory and Intent](3-memory-and-intent.md):** Outcome-weighting attaches to the memory and intent-shape constructs defined there; the judgment layer is what would supply the weights.
- **[Design as Bottleneck](../9-mental-models/6-design-as-bottleneck.md):** If implementation is free and the new constraint is deciding _what_ to build, a judgment layer that improves intent-formation targets exactly that bottleneck.

---

## Sources

- The five-layer model and composition loop draw on current long-horizon agent-state practice in git-native agent infrastructure.
- The factory-to-institution argument and the proposed Layer 6 are forward-looking synthesis, presented here as hypotheses rather than documented results.
