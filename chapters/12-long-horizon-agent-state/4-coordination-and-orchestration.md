---
title: Coordination and Orchestration
description: The shared human-agent event log and multi-run execution across many agents
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-state, coordination, orchestration, multi-agent, event-log]
part: 3
part_title: Perspectives
chapter: 12
section: 4
order: 3.12.4
---

# Coordination and Orchestration

Layers 4 and 5—coordination and orchestration—are the layers that turn individual runs into collaborative, sequenced work. Coordination is the boundary where humans and agents hand off to each other; orchestration is the mechanism that chains many runs into a coherent program. Memory and intent give an agent what it needs to act; coordination and orchestration govern _how that action is steered and sequenced_ across a long horizon.

This section examines each layer in depth: coordination as a shared, append-only event log with humans and agents as peers, and orchestration as merge-gated multi-run execution.

---

## Coordination as a Shared Event Log

The coordination layer binds three things around a unit of work: the **intent** (goal, non-goals, constraints, success criteria), the **attachments** (the issues, changes, files, and memory records the work touches), and an **append-only event log** that records everything that happened. A coordination object built on these three parts is one concrete implementation of the layer.

The defining property is that humans and agents are **peer actors** on the same log. Each can append events; each sees the full history. The log accumulates over days or weeks and becomes the single source of truth for why the work is being done and what happened along the way—institutional memory of how a decision was actually made, not a scattered trail across chat threads and commit messages.

### The Human-Agent Boundary

The two kinds of actor have different rights, enforced by the write-ACL introduced in the composition loop:

| Action                                            | Human | Agent |
| ------------------------------------------------- | ----- | ----- |
| Set or edit intent (goal, non-goals, constraints) | Yes   | No    |
| Change work status                                | Yes   | No    |
| Answer a question                                 | Yes   | No    |
| Record a decision                                 | Yes   | Yes   |
| Produce an artifact                               | Yes   | Yes   |
| Pose a question                                   | Yes   | Yes   |

The asymmetry is the point. Humans own intent; agents propose, act, and ask. When an agent reaches a decision it cannot make within its authority—an ambiguous requirement, a tradeoff that changes the goal—it appends a **question event**, which pauses the work until a human answers. This converts the most dangerous autonomous failure, an agent quietly redefining its objective, into a visible pause-and-ask.

### What Coordination Enables

The shared log is what makes human-agent collaboration something other than "dispatch and pray." It lets work pause gracefully on a blocker instead of guessing, preserves the rationale behind decisions for whoever picks the work up next, and gives an interactive agent the full context of the unit of work on every turn. The event log is also where higher layers read state: an orchestrator watches it for question events to know when to pause, and for merge events to know when to advance.

---

## Orchestration as Multi-Run Execution

A single run rarely completes a real feature. Orchestration is the coordinator that walks a plan's steps one at a time: it dispatches a run for the current step, waits for that step's change to be reviewed and merged, then dispatches the next step with the accumulated context of everything before it.

The orchestrator is a small state machine per step—pending, dispatched, running, change-open, merged—emitting an event at every transition so the coordination log has a complete record. Its behavior across a plan follows a few rules:

- **Trivial steps auto-advance.** A step with no change to review does not wait for a merge.
- **Failed steps halt the plan.** A failure stops dispatching rather than cascading errors forward.
- **Resumed plans skip completed steps.** An interrupted plan picks up at the first unfinished step rather than restarting.
- **The work unit closes on the final merge.** When the last step lands, the orchestrator transitions the coordination object to done.

### Why Merge Is the Checkpoint

The orchestrator gates on **review and merge**, not on a timer or a webhook. This makes human review the natural checkpoint between steps and reuses the existing code-review workflow rather than inventing a new approval step. Each step produces an independently reviewable unit of change, so a long feature arrives as a sequence of clean, reviewed increments rather than one unreadable bulk change. Because the gate is the merge, a plan survives interruption: whatever has merged is done, and the coordinator resumes from there.

### Coordination and Orchestration Together

The two layers interlock. Orchestration reads the coordination log to know when to pause (a question event) and when to advance (a merge event); coordination records every orchestration transition as an event so the human sees the program's progress in one place. Orchestration without coordination can sequence runs but cannot pause for human judgment mid-plan. Coordination without orchestration captures the conversation but still requires a human to dispatch each step by hand. Combined, they produce unattended multi-step work that still stops and asks at exactly the right moments.

---

## Connections

- **[The Five-Layer Model](1-five-layer-model.md):** This section is the deep treatment of layers 4 and 5 introduced there.
- **[Orchestrator Pattern](../7-patterns/3-orchestrator-pattern.md):** The orchestration layer is the durable-state implementation of the orchestrator pattern—the state a coordinator reads and writes to sequence sub-agent work.
- **[Autonomous Loops](../7-patterns/4-autonomous-loops.md):** Merge-gated step advancement is how an autonomous loop stays bounded—each iteration is checkpointed by review rather than running unobserved.
- **[Production Multi-Agent Systems](../7-patterns/11-production-multi-agent-systems.md):** The coordination log and merge-gated orchestration are the operational substrate the production patterns there assume—attribution, batch tracking, and merge integration.
- **[Operating Agent Swarms](../8-practices/7-operating-agent-swarms.md):** Orchestration across many runs is what swarm operation coordinates; the coordination log is where its blockers and decisions surface for human attention.
