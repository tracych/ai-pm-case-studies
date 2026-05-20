# Prototype: Claude Code Fleet — Async Background Agents

An interactive UI prototype validating the **async background agent** opportunity for Claude Code.
Single-file HTML, no dependencies, no backend.

> Open `index.html` in any modern browser. Best viewed on a laptop in dark mode.

## Which opportunity this validates

This prototype validates **Opportunity B: Async Background Agent (Devin-competitor)** from
[`../03-opportunities.md`](../03-opportunities.md).

Today Claude Code is a synchronous IDE/terminal companion — one engineer, one Claude, one task at a time.
The async-agent opportunity flips the mental model: an engineer **dispatches a fleet** of Claude agents,
each working an independent task end-to-end (plan → code → test → PR), and the engineer reviews PRs
instead of writing code.

## The hypothesis

> **If Anthropic ships a managed, dashboard-based async-agent product, mid-market eng teams will pay
> $200–$500/seat/month to "10× their PR throughput" — turning Claude Code from a per-developer tool
> into a per-team capacity multiplier.**

The key risk to test: do users actually want a **fleet view** (managing many agents), or do they only
trust one agent at a time? This prototype's UX is the bet — the dashboard makes the fleet feel
manageable, observable, and controllable.

## What's mocked vs what's real

| Layer | Status |
|---|---|
| UI, layout, animations, interactions | **Real** — vanilla JS, no framework, no CDN |
| Agent progress over time (tick loop) | Real but scripted — `setInterval` advances state deterministically |
| Agent reasoning, tool calls, code diffs | **Mocked** — hand-authored to feel like real Claude output |
| Dispatching a new agent | UI works, no real agent — spawns a scripted placeholder card |
| Plan / Files / Terminal / PR tabs | Real UI bound to mocked data per agent |

This is a **UI-validation prototype, not a working agent platform**. The point is to put the
PM-designed surface in front of a stakeholder in 60 seconds.

## Key interactions the demo shows

1. **Fleet at a glance** — 5 agents on the board, each in a different lifecycle stage (planning,
   coding, testing, PR-open, blocked). Color-coded status with live progress bars and live narration.
2. **Click any card** — opens a 5-tab inspector: **Transcript** (Claude's reasoning + tool calls),
   **Plan** (checklist with active step highlighted), **Files** (add/mod/del with line counts),
   **Terminal** (real-looking shell output), **PR** (drafted PR body for completed agents).
3. **Live ticking** — progress bars advance every ~2s, agents auto-transition planning → coding →
   testing → PR-open. When an agent finishes, a toast announces the PR. Try the **1× / 2× / 4×**
   speed toggle in the top bar to fast-forward.
4. **Blocked state** — TASK-4121 (Stripe migration) is paused waiting on a missing webhook secret.
   The transcript shows Claude explicitly refusing to invent credentials — demonstrates the
   "good agent" mental model PMs need to internalize for trust.
5. **Dispatch a new agent** — top-right button opens a modal with 4 task templates. Dispatching
   spawns a new card that begins ticking through its lifecycle alongside the others.

## What success would look like if shipped

- **Activation:** ≥40% of existing Claude Code seats dispatch at least one async agent in week 1
- **Concurrency:** median active user runs 3+ agents in parallel at peak
- **PR quality:** ≥70% of agent PRs merged within 24h without human edits
- **Revenue:** $300+ in incremental ARR per converted seat
- **Trust signal:** users actually leave the dashboard open in a browser tab (vs. only checking once)

## Limitations + what real engineering work would be needed

- **Sandboxing & isolation:** each agent needs a real ephemeral container with repo clone, network
  policy, and resource caps. (Not modeled.)
- **State machine + persistence:** real lifecycle is durable across crashes, not an in-memory array.
- **Permissions & approval gates:** real product needs per-repo write scopes, PR-approval policy,
  and an audit log. The "Blocked" state hints at the human-in-the-loop pattern but doesn't
  implement an approval flow.
- **Cost control:** the `$/agent` field is cosmetic. Production needs token budgets, cost ceilings
  per task, and per-team billing rollups.
- **Failure modes not shown:** runaway loops, hallucinated files, broken builds. A real fleet
  dashboard would surface intervention prompts and one-click rollback.
- **No real model integration:** swapping mocked transcripts for live Claude calls is itself a
  multi-month engineering investment (agent harness, tool orchestration, sandbox plumbing).

## Why this prototype, not the others

Of the three opportunities in `../03-opportunities.md`, the async-agent fleet is the most
defensible to demo in an interview because:
- it shows **agentic PM thinking** (information density, status communication, control affordances)
- it visually telegraphs the **business model shift** (per-seat → per-capacity)
- it's the **most differentiated** from today's Claude Code, so the prototype carries the argument
