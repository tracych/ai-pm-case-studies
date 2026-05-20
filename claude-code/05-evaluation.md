# 05 — Evaluation Framework

> Scope: we are shipping **Claude Code Web** (browser-native, mainstream-dev TAM) as the primary revenue bet, with **Async Background Agent** as the secondary bet covered at lighter touch. The framework below is built so the same scaffolding works for both — only the north-star and a few inputs swap.

---

## 1. North-Star Metric

**Web NSM: Weekly Active Coders who complete ≥1 task (WAC-1)**
A "completed task" = a user-initiated session that ends in either (a) accepted code suggestion shipped to a branch/PR, or (b) explicit thumbs-up + session-end. Not a turn. Not a message. A *unit of work the user kept*.

Why not the obvious alternatives:

- **Signups** — vanity. Pricing PLG funnels show 60%+ of devtool signups never return after day 1.
- **WAU / sessions** — measures curiosity, not value. A user who opens the app, asks one question, and bounces still counts. We've seen this fail at Replit and Cursor in their early instrumentation.
- **Retention (D7/D28)** — a downstream consequence of WAC-1, not a leading driver. If WAC-1 moves, D28 will follow with a 3–4 week lag.
- **Revenue / seats** — a lagging financial signal. Useful for the board deck, useless for the weekly product review.

WAC-1 forces us to instrument the *job-to-be-done* boundary, not the surface engagement. It is also the metric most tightly coupled to willingness-to-pay in our pricing research.

**Async Background Agent NSM: PRs merged from agent-authored work / week (per active user).** Same logic — the only thing that matters is *merged*, because async agents that produce un-merged PRs are net-negative (they create review burden without shipping value).

---

## 2. Metric Tree

```
[L0] NORTH STAR: Weekly Active Coders w/ ≥1 completed task (WAC-1)
│
├── [L1] Activation Rate — % new signups reaching first completed task in 7 days
│    ├── [L2] Time-to-first-task (median minutes from signup → first accepted suggestion) — telemetry
│    ├── [L2] First-session task-success % — telemetry + accept/reject events
│    ├── [L2] Onboarding completion rate (repo connect → first prompt) — telemetry funnel
│    └── [L2] First-week NPS (post-activation survey, n≥300/wk) — Delighted/Sprig survey
│
├── [L1] Task Throughput — completed tasks / WAC / week
│    ├── [L2] Median turns-per-completed-task — telemetry (lower = better, floor ~2)
│    ├── [L2] Tool-call success rate (Edit/Bash/Read no-error %) — telemetry
│    └── [L2] Context-load success rate (repo + file retrieval p95 latency <2s) — telemetry
│
├── [L1] Quality / Trust — does the work survive contact with reality
│    ├── [L2] Suggestion acceptance rate (accepted / shown) — telemetry
│    ├── [L2] Post-accept revert rate within 24h — git telemetry
│    ├── [L2] Eval-suite pass rate (nightly hold-out, 250 tasks) — synthetic eval
│    └── [L2] Hallucination rate on codebase claims — sampled human grading (n=100/wk)
│
└── [L1] Retention — WAC-1 cohort retention W4 / W12
     ├── [L2] Repeat task rate (% users with ≥3 completed tasks/wk) — telemetry
     └── [L2] Multi-repo usage (% users on ≥2 repos in 4 weeks) — telemetry
```

---

## 3. AI-Specific Quality Evals

The three-layer split is the actual PM craft here. Capability says the model *can*; calibration says the model *knows what it can*; product says the user *gets value*. Conflating these is the #1 reason AI products ship broken.

### Layer 1 — Capability Evals (does the agent get the task right?)

| Item | Spec |
|---|---|
| **Test set** | 250 real coding tasks sampled from anonymized user sessions + 50 curated edge cases (concurrency, large refactors, ambiguous specs). Refreshed quarterly. Held private. |
| **Metrics** | Task-completion % (binary), unit-test pass rate of generated code, linter score, turns-to-completion, tokens-to-completion |
| **Cadence** | Nightly on `main` model + every candidate model checkpoint |
| **Gate** | Model promotion blocked if pass-rate regresses >2pp or turns-to-completion regresses >15% vs prod baseline |
| **Owner** | Eval engineer + PM weekly review of regression diffs |

### Layer 2 — Calibration Evals (does the agent know when it's wrong?)

This is the layer most teams skip. It's also the one that determines whether async agents are usable at all.

| Item | Spec |
|---|---|
| **Confidence calibration** | When the model emits "this should work" / "tests pass" / "fixed", how often is it true? Target: ≥85% precision. Measured via 200-task weekly human-graded sample. |
| **Hallucination rate** | % of model claims about the codebase ("the `foo` function lives in `bar.py`") that are factually wrong. Target: <3%. Auto-graded by AST + filesystem ground truth. |
| **Surrender quality** | When the agent gets stuck (>5 turns no progress), does it ask a useful clarifying question vs. spin / fabricate? Rubric-graded 0–3 on a 50-task weekly sample. |
| **Refusal appropriateness** | False-refusal rate on safe coding tasks; over-compliance rate on risky ones (rm -rf, prod creds). |

### Layer 3 — Product / UX Evals (does the developer actually succeed?)

| Item | Spec |
|---|---|
| **End-to-end task success** | Real users, real tasks, telemetry-derived. Definition above. |
| **Time-to-first-merge** | Signup → first agent-authored PR merged. Headline activation metric. |
| **Trust score** | Composite: 60% behavioral (% suggestions accepted without expand/inspect) + 40% survey ("I trust Claude Code to ship to prod" 1–7). |
| **Rage-quit rate** | Sessions ending in cancel after >3 turns. Watch the slope, not the level. |
| **Cost-to-value** | $ compute cost per completed task, by task type. Critical for async agent ROI math. |

---

## 4. Online vs Offline Evals

| | Offline | Online |
|---|---|---|
| **Purpose** | Gate model/feature *promotion to prod* | Gate model/feature *exposure to 100% of users* |
| **Speed** | Hours | Days to weeks |
| **Signal** | Capability, calibration, regression | Real task success, trust, cost, retention |
| **Risk** | Overfitting to eval set | Hurting real users |
| **Rule** | Must pass L1 + L2 evals to enter A/B | Must beat control on NSM + not regress guardrails to ramp to 100% |

The offline suite is the *promotion gate*. The online experiment is the *launch gate*. Skipping either gets you SWE-bench heroes that ship broken products, or beautiful products that quietly regressed on a model swap.

---

## 5. A/B / Experimentation Design

- **Unit of randomization**: **user** for Web (independent decisions); **workspace** for Async Background Agent (shared repo state means user-level randomization leaks).
- **Sample size**: NSM baseline ~22% activation, MDE = 2pp absolute lift, α=0.05, power=0.8 → ~6,500 users per arm. At current signup rate of ~4k/week, that's a 2-week experiment per arm. For Async, workspace counts are lower (~800/wk) → 4-week experiments, so we prioritize fewer/bigger bets.
- **Guardrail metrics** (any regression auto-pauses):
  - p95 turn latency (Web: <8s; Async: <90s)
  - $ cost per completed task (no >20% regression)
  - Crash / tool-error rate (<0.5%)
  - Abuse signals: prompt-injection success rate, prod-credential exfil attempts
  - Rage-quit rate (<5%)
- **Stopping rules**: (1) auto-stop if any guardrail regresses >2σ for 48h; (2) sequential testing with Bonferroni on the 4 driver metrics so we don't false-positive ourselves into a launch.

---

## 6. Eval Pitfalls to Avoid

1. **Benchmark hacking.** SWE-bench Verified scores stopped predicting real-world success around mid-2024 once labs started training on adjacent data. *Avoid:* maintain a private hold-out set refreshed quarterly from real user sessions; never publish it; never include it in pretraining filters' allowlist.
2. **Survivorship bias on completed sessions.** Measuring acceptance rate only on sessions that submitted misses the user who tried twice, got bad output, and never came back. We saw this in early Copilot data — acceptance looked great, retention was terrible. *Avoid:* always include a *session-abandon* bucket in the funnel; rage-quit rate is a guardrail.
3. **Cost-blind eval.** A new model can boost task success 5pp while regressing $/task 4×. That's a P&L disaster dressed as a win. *Avoid:* every capability eval reports cost in the same dashboard row as quality; no promotion without cost-adjusted scorecard.
4. **Eval set drift from product reality.** As we add features (web search, multi-file edit, async), the eval set gets stale. *Avoid:* quarterly eval-refresh sprint where 20% of tasks are rotated in from the last quarter's user sessions, weighted toward newly supported capabilities.

---

## 7. The 90-Day Learning Agenda

| Week | Question | Eval method | Decision it informs |
|---|---|---|---|
| 1–2 | What is real baseline WAC-1 and activation funnel for Web beta? | Telemetry on closed beta (n≈500) | GA readiness gate |
| 3–4 | Where do new users drop off in first session? | Funnel analysis + 20 user interviews | Onboarding redesign scope |
| 5–6 | Does the async agent's PR-merge rate justify its cost? | Cohort analysis on async beta + $/merged-PR | Continue/kill async investment level |
| 7–8 | What's the calibration gap between "model says done" and "user merges"? | L2 calibration eval on 500 sampled sessions | Trust-UI investment (confidence badges, diff previews) |
| 9–10 | Which task types drive WAC-1 vs. which are vanity? | Task-type segmentation on NSM | Where to invest model-capability roadmap |
| 11–12 | Is acceptance-without-inspect rising or falling as users mature? | Behavioral trust score over cohort age | Auto-merge / auto-apply policy |
| 13 | Does Web cannibalize CLI or expand TAM? | Cross-product cohort: CLI usage of Web signups | Pricing + bundling decision for Q3 |

The agenda is deliberately front-loaded with funnel and trust questions, because those determine whether the *product* works — model questions come later because we already know the model is good enough; what we don't know is whether mainstream developers will trust it.
