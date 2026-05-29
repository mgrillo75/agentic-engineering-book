---
title: The Five-Layer Model
description: Decomposing long-horizon agent state into execution, memory, intent, coordination, and orchestration
created: 2026-05-29
last_updated: 2026-05-29
tags: [agent-state, architecture, memory, intent, coordination]
part: 3
part_title: Perspectives
chapter: 12
section: 1
order: 3.12.1
---

# The Five-Layer Model

Long-horizon agent state is not a single feature. It decomposes into five distinct layers, each solving a different problem, each useless without the others. A system that needs agents to sustain work across days and across handoffs needs all five. Naming them separately makes it possible to diagnose which layer is missing when a long-running deployment breaks.

The layers stack from the most concrete (a single run's history) to the most abstract (sequencing many runs into a coherent program of work). Higher layers build on the substrate the lower ones provide.

---

## The Stack

```text
┌─────────────────────────────────────────────┐
│  5. Orchestration                           │
│     Sequential multi-run execution with     │
│     human-gated checkpoints                 │
├─────────────────────────────────────────────┤
│  4. Coordination                            │
│     Shared event log where humans and       │
│     agents act as peers                     │
├─────────────────────────────────────────────┤
│  3. Intent                                  │
│     Structured work decomposition with      │
│     dependencies and lifecycle              │
├─────────────────────────────────────────────┤
│  2. Memory                                  │
│     Typed expertise that survives sessions  │
│     and decays gracefully                   │
├─────────────────────────────────────────────┤
│  1. Execution                               │
│     Isolated runs with event streams and    │
│     durable history                         │
└─────────────────────────────────────────────┘
```

---

## Layer 1: Execution (Durable Run History)

**What it stores.** Every agent run produces a full, persisted event stream—tool calls, reasoning steps, outputs, errors, and state transitions. Runs execute in isolated sandboxes, but their history is permanent. On restart, the system resumes the stream from the last persisted sequence number; failed runs leave inspectable artifacts; runs orphaned by a crash are reconciled at boot.

**Why it matters.** This layer is the raw substrate every higher layer reads. It provides audit trails, cost attribution, and replay. Without a durable record of what an agent actually did, none of the higher layers have ground truth to build on.

**What breaks without it.** Runs become black boxes that vanish when they finish. A failure cannot be diagnosed because the evidence is gone. Cost cannot be attributed. There is no way to answer "what did this agent do, and why did it stop"—the most basic question in any production incident.

---

## Layer 2: Memory (Typed Expertise)

**What it stores.** A typed memory system where agents record conventions, patterns, failures, decisions, and references—keyed by domain, linked to evidence such as commits or files, and scoped to relevance on retrieval. Records carry classification tiers: foundational knowledge is permanent, tactical knowledge decays over months, observations decay over weeks. An agent primes itself from this store at the start of a session and records new learnings before it finishes.

**Why it matters.** Memory is what lets an agent get _better_ at a codebase over time and lets one agent's learnings transfer to the next. Graceful decay keeps the store from accumulating stale advice that misleads future runs.

**What breaks without it.** Agents rediscover the same facts every session. A hard-won insight—this build flag is required, this API silently rate-limits—dies with the context window that learned it. The system never compounds; every run starts from zero.

---

## Layer 3: Intent (Work Decomposition)

**What it stores.** A structured representation of work: goals decomposed into plans with ordered steps, blocking dependencies, and lifecycle states (open, in progress, closed). Agents read the work queue to learn what is ready; plans give the full picture of what is done, what is blocked, and what comes next. A git-native issue tracker is a typical implementation—agents query for unblocked work and walk plan children in dependency order.

**Why it matters.** Intent turns a one-shot task into a multi-step goal that survives across sessions. An agent can pick up exactly where the last one stopped, and a human can see progress on agent work without reading every run log.

**What breaks without it.** Agents work on isolated tasks with no awareness of the broader goal. Nothing tracks what remains, what is blocked, or what order work must happen in. Multi-step goals cannot survive a single run because there is no durable representation of the steps.

---

## Layer 4: Coordination (Shared Human-Agent Event Log)

**What it stores.** A coordination object that binds intent, attachments, and an append-only event log around a unit of work. Humans and agents are peer actors on the log. Humans set intent—goals, non-goals, constraints, success criteria—and answer questions. Agents record decisions, produce artifacts, and pose questions. A write-ACL enforces the boundary: agents cannot edit intent, they must ask. Questions pause the work until a human answers.

**Why it matters.** This layer is where humans and agents hand off without working in silos. It captures _why_ a piece of work is being done and _what happened_ along the way, as institutional memory rather than scattered chat logs. It lets agents ask for help instead of guessing, and lets work pause gracefully on a blocker.

**What breaks without it.** Human-agent collaboration degrades to "dispatch and pray." There is no shared record of decisions or steering, no way for the two to take turns, and no mechanism for an agent to surface a blocker rather than silently producing the wrong thing.

---

## Layer 5: Orchestration (Multi-Run Execution)

**What it stores.** A coordinator that walks a plan's steps one at a time, dispatching a run per step and waiting for the previous step's change to merge before dispatching the next. Trivial steps auto-advance; a failed step halts the plan; a resumed plan skips already-completed steps. When tied to a coordination object, the orchestrator transitions the work unit to done once the final step lands.

**Why it matters.** Real work is rarely one change. Orchestration produces multi-change features where each unit is independently reviewable, and it makes human review the natural checkpoint between steps—the agent cannot proceed until a human merges. Plans survive interruption and resume from where they stopped.

**What breaks without it.** Sequencing every step falls back to a human dispatching each run by hand. Long programs of work cannot run unattended, interruptions lose all progress, and there is no automatic gate that holds the next step until the prior one is reviewed.

---

## The Layers at a Glance

| Layer                | Stores                                                     | Enables                                          | Breaks without it                                   |
| -------------------- | ---------------------------------------------------------- | ------------------------------------------------ | --------------------------------------------------- |
| **1. Execution**     | Durable per-run event streams in isolated sandboxes        | Audit, cost attribution, replay, resume          | Runs are black boxes; failures undiagnosable        |
| **2. Memory**        | Typed expertise records, keyed by domain, decaying by tier | Agents improve over time; knowledge transfers    | Constant rediscovery; learnings die with context    |
| **3. Intent**        | Plans, ordered steps, dependencies, lifecycle              | Multi-step goals that survive sessions           | No awareness of the broader goal or remaining work  |
| **4. Coordination**  | Shared append-only human-agent event log + intent          | Graceful handoff, blockers, institutional memory | "Dispatch and pray"; agents guess instead of asking |
| **5. Orchestration** | Multi-run coordinator gated on merge checkpoints           | Reviewable multi-change work; resumable plans    | A human must sequence every run by hand             |

No layer is sufficient alone. Memory without intent is trivia with nowhere to apply itself. Intent without coordination is fire-and-forget. Coordination without orchestration still requires a human to sequence every step. The value comes from composing them—the subject of the next section.

---

## Connections

- **[The Composition Loop](2-composition-loop.md):** The five layers are not independent features; they close into a feedback loop. The next section traces that loop and the principles for building it.
- **[Memory and Intent](3-memory-and-intent.md):** Layers 2 and 3 receive deeper treatment, including the contrast between typed memory and context stuffing.
- **[Coordination and Orchestration](4-coordination-and-orchestration.md):** Layers 4 and 5 receive deeper treatment, including the human-agent boundary and multi-run execution.
- **[Context Management Architectures](../4-context/5-context-management-architectures.md):** Layer 1 is the durable counterpart to in-window context management—history that persists past the run rather than being compacted away within it.
