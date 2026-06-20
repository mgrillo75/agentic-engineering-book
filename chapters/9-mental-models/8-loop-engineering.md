---
title: "Loop Engineering"
description: "The leverage ladder — as implementation automates, the engineer's authored artifact moves from prompt to loop, and verification becomes the binding constraint"
created: 2026-06-20
last_updated: 2026-06-20
tags:
  [
    mental-models,
    loop-engineering,
    leverage,
    harness,
    context-engineering,
    self-improvement,
    verification,
    long-horizon,
    autonomous-loops,
    osmani,
  ]
part: 3
part_title: Perspectives
chapter: 9
section: 8
order: 3.9.8
---

# Loop Engineering

Loop engineering is the practice of authoring the system that prompts the agent rather than authoring the prompts. The agentic loop is not new—ReAct, autonomous loops, and plan-build-review all iterate a model against tools until a goal is met. What changes is where the human stands: outside the per-turn cycle, designing the cycle itself. This reframing, surfacing in practitioner writing in mid-2026, is less a new technique than a name for one rung—the rung above the harness—of a leverage ladder that the rest of this book climbs.

---

## The Leverage Ladder

**As implementation automates, the engineer's authored artifact moves up a ladder, and each rung raises the unit of leverage.**

Every rung moves the human one step further from the keystroke. The lower rungs operate inside a single turn; the upper rungs operate across turns, sessions, and runs.

| Rung                    | Authored artifact                  | Where the human stands       |
| ----------------------- | ---------------------------------- | ---------------------------- |
| Prompt engineering      | A single prompt                    | Inside each turn             |
| Context engineering     | What the agent sees each turn      | Curating each turn's input   |
| Harness engineering     | The environment one agent runs in  | Around a single agent        |
| **Loop engineering**    | The system that prompts the agent  | Above the harness            |
| Factory                 | The system that builds software    | Running the organization     |

```text
            authored artifact                 human position
            ─────────────────                 ──────────────
prompt   →  one prompt                         inside each turn
context  →  what the agent sees                inside each turn
harness  →  the agent's environment            around the agent
loop     →  the system that prompts            ┌─ above the harness ─┐
                                               │ runs on a timer     │
                                               │ spawns helpers      │
                                               │ feeds itself        │
factory  →  the system that builds             └─ runs the org ──────┘
```

The ladder is the same progression the [Twelve Leverage Points](../1-foundations/1-twelve-leverage-points.md) describes: intervening further upstream changes more of the system's behavior per unit of effort. It also recasts the book's foundations—[Prompt](../2-prompt/_index.md), [Context](../4-context/_index.md), and [Harnesses](../6-harnesses/_index.md)—not as separate topics but as rungs of one structure. Loop engineering names the rung above the harness.

_[2026-06-20]_: Addy Osmani frames loop engineering as "replacing yourself as the person who prompts the agent—you design the system that does it instead," and positions the loop "one floor above the harness." The book's ladder generalizes this single step into a full progression. (Osmani, "Loop Engineering," addyosmani.com, 2026)

---

## What Loop Engineering Is

A **loop**, in this sense, is a recursive goal: a purpose is defined once, and the system iterates until the purpose is met. The distinguishing property is self-direction—the loop discovers work, assigns it, verifies completion, records results, and decides what to do next, without a human writing each prompt.

The contrast with prompt engineering is structural, not qualitative:

### Prompt Engineering

A human writes a prompt, reads the response, then writes the next prompt. The human holds the loop the entire time, one turn after another. Quality is bounded by prompt quality.

### Loop Engineering

A system discovers work, dispatches it, verifies it, and triggers the next iteration on its own. The human holds the loop's _architecture_, not its turns. Quality is bounded by loop architecture—what work the loop can find, how reliably it verifies, and what it remembers between runs.

The leverage point moves from the wording of a prompt to the structure of the loop. This is the same shift [Design as Bottleneck](6-design-as-bottleneck.md) describes one level down: when the cheap thing (a turn) is automated, the scarce thing (the loop's design) becomes the constraint.

---

## Anatomy of a Loop

A self-directing loop is assembled from components the rest of the book treats separately. Naming them together is what makes "loop engineering" a useful frame rather than a relabeling.

| Component        | Role in the loop                                       | Role / where covered                                                   |
| ---------------- | ------------------------------------------------------ | ---------------------------------------------------------------------- |
| **Triggers (automations)** | Start the loop on a timer, not a human prompt | Cron, CI hooks, scheduled runs                                         |
| **Worktrees**    | Isolate parallel work so agents do not collide         | [Operating Agent Swarms](../8-practices/7-operating-agent-swarms.md)   |
| **Skills**       | Supply codified knowledge so context is not re-fed     | [Skills and Meta-Tools](../5-tool-use/5-skills-and-meta-tools.md)      |
| **Connectors**   | Reach real tools and systems (often via MCP)           | [Tool Design](../5-tool-use/1-tool-design.md)                          |
| **Sub-agents**   | Verify work independently, avoiding self-grading bias  | [Multi-Agent Collaboration](../7-patterns/9-multi-agent-collaboration.md) |
| **State**        | Persist memory across runs the model would otherwise lose | [Long-Horizon Agent State](../12-long-horizon-agent-state/_index.md)   |

The state component is load-bearing. A model forgets everything between runs, so a loop without persistent state cannot improve—it rediscovers the same work and repeats the same mistakes. State is what turns a sequence of isolated runs into a single long-horizon process. This is the same insight behind [Persistent Identity, Ephemeral Execution](6-design-as-bottleneck.md#model-3-persistent-identity-ephemeral-execution): sessions are disposable, but what the loop knows must persist.

### Example: A Self-Feeding Triage Loop

```text
06:00  Trigger fires (cron)
  │
  ▼
Discover ──► scan issue tracker, logs, failing tests
  │
  ▼
Assign ────► spawn isolated worktree per promising item
  │
  ▼
Act ───────► drafting sub-agent proposes a fix
  │
  ▼
Verify ────► separate sub-agent + test harness check it
  │
  ▼
Record ────► write findings + decisions to a state file
  │
  ▼
Decide ────► open PRs for passing work; queue the rest for tomorrow
```

No human writes a prompt at any step. The human authored the loop's shape—what counts as work, how it is verified, what persists—once.

---

## Verification as the Binding Constraint

When the human leaves the per-turn loop, something must still decide whether each iteration's output is correct. That role no longer fits human review: a loop that runs on a timer and spawns helpers produces work faster than any human can read it. Verification becomes the binding constraint on how far the loop can run unattended.

The **harness-first** response inverts the usual order: build the verification mechanism _before_ the implementation, defining explicit invariants the agent must satisfy on every iteration. Correctness then lives in the harness, not in a reviewer's attention.

```text
Review-bounded loop:                Verification-bounded loop:

agent → human reads every diff      agent → harness checks invariants
          │                                   │  (seconds, high confidence)
          ▼                                   ▼
   bounded by reading speed          telemetry validates in production
                                              │
                                              ▼
                                   feedback updates harness → agent retries
```

Two consequences follow once correctness is locked into the harness:

- **Optimization becomes controlled hill-climbing.** The agent proposes a change, the verification suite runs, and the change is accepted only if the invariants still hold. The loop can improve itself—including rewriting parts of its own harness—because every proposal is gated by an objective check rather than human judgment.
- **The economics of rigor invert.** When agents can generate specifications and checks automatically, investment shifts from checking outputs to designing the mechanisms that check them. Formal methods and exhaustive simulation become cheaper than line-by-line review, because the expensive human step (reading) is removed from the inner loop.

_[2026-06-20]_: Datadog's "harness-first agents" describes this loop directly: "the feedback updates the harness and the agent tries again," with deterministic simulation testing as the verification workhorse and performance work reduced to "controlled hill-climbing" once correctness is locked in. The human role narrows to defining invariants, strengthening harnesses, and approving architecture. (Datadog, "Harness-First Agents," datadoghq.com, 2026)

This is the loop-engineering form of the constraint [Design as Bottleneck](6-design-as-bottleneck.md) identifies: the scarce resource is not implementation but the upstream design—here, the design of verification.

---

## When to Use

- **Recurring, discoverable work.** Triage, dependency upgrades, flaky-test repair, doc drift—work that can be found programmatically and verified mechanically.
- **Tasks with objective acceptance checks.** A loop is only as trustworthy as its verifier. Strong checks (tests, type systems, simulation, telemetry) make autonomy safe.
- **Long-horizon goals.** Objectives that span many runs and benefit from accumulated state rather than a single session.

## When Not to Use

- **Tasks without a reliable verifier.** If correctness can only be judged by a human, the loop cannot close, and autonomy adds risk rather than leverage.
- **One-off or exploratory work.** The cost of authoring a loop is justified by repetition. A single ambiguous task is better served by a prompt or a supervised session.
- **Domains where the cost of a wrong unattended action is high and irreversible.** Keep a human in the loop (see [Human-in-the-Loop](../7-patterns/6-human-in-the-loop.md)) until verification is trusted.

---

## Anti-Patterns

### Open Loop

**The mistake:** Building a self-directing loop without a verifier strong enough to gate its output—relying on the agent to grade its own work.

**Why it fails:** Self-grading bias compounds. Each unverified iteration becomes the input to the next, and errors accumulate silently until the loop has produced a large body of confidently wrong work. The faster the loop runs, the more damage before anyone notices.

**The fix:** Close the loop with independent verification—a separate sub-agent, a test harness, or production telemetry. No iteration advances until an invariant confirms it.

### Knowledge Rot

**The mistake:** Letting the loop's codified knowledge (skills, state files, documented decisions) drift out of sync with the system it operates on.

**Why it fails:** A loop acts on what it remembers. Stale skills and outdated state cause the loop to confidently apply obsolete patterns, and because no human is in each turn, the drift is invisible until outputs degrade.

**The fix:** Treat the loop's knowledge as code with an owner and a refresh path. See [Knowledge Evolution](../8-practices/6-knowledge-evolution.md) and [Context as Code](4-context-as-code.md).

### Cognitive Surrender

**The mistake:** Ceding so much judgment to the loop that the humans operating it lose the understanding required to design, debug, or correct it.

**Why it fails:** A loop encodes its designers' understanding of the problem. When that understanding atrophies, the loop cannot be improved when the problem shifts, and failures become unintelligible. The leverage that the loop provides depends on a human who still understands what it is doing and why.

**The fix:** Keep humans authoring the loop's architecture and invariants, not just consuming its output. Periodically inspect what the loop is doing, not only whether it is running.

---

## A Tension to Hold

Loop engineering is new enough that its status is contested. Two readings coexist:

- **A genuine new discipline.** Authoring self-directing loops is a distinct skill from authoring prompts, with its own failure modes (open loops, knowledge rot) and its own leverage. On this reading, the term marks a real shift in where engineering effort goes.
- **A rebrand of existing agentic loops.** Skeptics note that ReAct, autonomous loops, and orchestration already describe iterating an agent against tools until done. On this reading, "loop engineering" repackages established patterns under a fresh label during a period of rapid terminology churn.

Both readings carry weight. The mechanics loop engineering names are real and already documented across this book; whether the _name_ earns its keep depends on whether the leverage-ladder framing changes design decisions. The value of the term is not that the loop is novel—it is that placing the human _above_ the harness clarifies where the constraint moves (to verification) and what the engineer should author (the loop, not the turn). That clarification is useful independent of whether the label survives.

---

## Open Questions

- **How far up the ladder does leverage keep paying off?** Each rung has so far raised leverage. Does authoring the loop generalize to authoring the system that authors loops, or does meta-design hit diminishing returns?
- **What makes a verifier trustworthy enough to close a loop unattended?** The autonomy a loop can safely take is bounded by verifier quality, but there is no settled measure of when a verifier is "good enough" to remove the human.
- **Where is the boundary between a loop and a factory?** Osmani places the loop one floor below the factory. Whether these are distinct levels or a continuum is unresolved—see [Software Factories](7-software-factories.md).
- **Does the term survive?** If "loop engineering" is absorbed back into "agentic orchestration" or "harness engineering," which of its distinctions—if any—persist as named concepts?

---

## Connections

- **[Twelve Leverage Points](../1-foundations/1-twelve-leverage-points.md):** The leverage ladder is the same upstream-intervention principle—authoring the loop is a higher leverage point than authoring the prompt.
- **[Autonomous Loops](../7-patterns/4-autonomous-loops.md):** The pattern loop engineering generalizes. Autonomous loops describe the mechanism; loop engineering describes the engineer's relationship to it.
- **[ReAct Pattern](../7-patterns/5-react-pattern.md):** The Thought/Action/Observation cycle is the substrate a loop runs on—the inner turn the loop engineer no longer authors by hand.
- **[Self-Improving Experts](../7-patterns/2-self-improving-experts.md):** Hill-climbing within a verified harness is self-improvement made safe by an objective gate.
- **[Harnesses](../6-harnesses/_index.md):** Loop engineering sits one rung above harness engineering—the harness is what the loop wraps and, when self-improving, rewrites.
- **[Design as Bottleneck](6-design-as-bottleneck.md):** Loop engineering is design-as-bottleneck applied one level up: when turns are automated, the loop's design (and its verification) is the constraint.
- **[Long-Horizon Agent State](../12-long-horizon-agent-state/_index.md):** Persistent state is what lets a loop accumulate across runs rather than restart each time—the five-layer model is the loop's memory.
- **[Knowledge Evolution](../8-practices/6-knowledge-evolution.md):** The countermeasure to knowledge rot—keeping a loop's codified knowledge current.
- **[Human-in-the-Loop](../7-patterns/6-human-in-the-loop.md):** The complement to autonomy—where verification is not yet trustworthy, the human stays in the loop by design.

---

## Sources

- Addy Osmani, "Loop Engineering" (addyosmani.com, 2026) — primary practitioner definition: "replacing yourself as the person who prompts the agent"; the loop "one floor above the harness"; the five mechanics (automations, worktrees, skills, connectors, sub-agents) and state tracking; the three tensions (verification burden, knowledge rot, cognitive surrender). https://addyosmani.com/blog/loop-engineering/
- Datadog, "Harness-First Agents" (datadoghq.com, 2026) — verification as the binding constraint; building checks before implementation; deterministic simulation testing; "controlled hill-climbing"; the inversion that makes formal methods cheaper than review. https://www.datadoghq.com/blog/ai/harness-first-agents/
- Skeptical reading — practitioner critiques framing loop engineering as a rebrand of existing agentic-loop patterns amid terminology churn (mid-2026). Treated here as an open tension rather than a settled position.
