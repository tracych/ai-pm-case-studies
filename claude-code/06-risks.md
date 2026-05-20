# Risk Analysis: Claude Code Web + Async Background Agent

> Scope: risks to the combined H1–H2 bet on (a) Claude Code Web (browser-native, mainstream-dev TAM) and (b) Async Background Agent (Devin-class long-horizon agent). Both ship on overlapping infra, are sold into overlapping ICPs, and share the same brand surface — so most risks compound rather than offset.

---

## 1. Risk Register

Severity 1–5 (5 = bet-killing). Likelihood 1–5 (5 = >70% within 12 months). Score = S × L. Sorted descending.

| Rank | Risk | Category | Sev | Lik | Score | Trigger signal |
|---|---|---|---|---|---|---|
| 1 | Async agent reliability stalls below "trust threshold" (~85% success on multi-step tasks) | Product / Technical | 5 | 4 | **20** | Internal SWE-bench-Verified or Terminal-bench score plateaus across two model generations; beta NPS for async <20 |
| 2 | Per-task compute cost > willingness-to-pay; foundation-model price war squeezes margin further | Cost economics | 5 | 4 | **20** | Median async task cost >$8 against $50 ARPU; OpenAI/Google undercut Claude API by >30% |
| 3 | Competitor (Cursor, Copilot, Cognition, Cloudflare) preempts the web/async wedge before GA | Market | 5 | 4 | **20** | Cursor ships hosted async tier at lower price; Copilot Workspace exits preview with GitHub-native distribution |
| 4 | High-profile agent failure goes viral (deleted repo, leaked secret, prod outage) | Reputational / Trust & safety | 5 | 3 | **15** | First "Claude Code wiped my repo" tweet >10K impressions; one paying customer files incident |
| 5 | Web UX fails to onboard mainstream devs — feels like a toy vs. an IDE | Product | 4 | 4 | **16** | D7 retention <15% on Web free; >40% of web sessions end with "open in CLI" |
| 6 | Parallel bets dilute focus; neither ships at the quality bar | Org / Execution | 4 | 3 | **12** | Slipped milestone on either track by >4 weeks; same eng leads owning both surfaces |
| 7 | Agent commits secrets, runs destructive git ops, or executes malicious code in sandbox | Trust & safety | 5 | 2 | **10** | First confirmed `.env` exfiltration; sandbox escape CVE filed |
| 8 | EU AI Act classifies autonomous coding agents as high-risk; data residency blocks enterprise | Regulatory | 4 | 2 | **8** | EU AI Office issues guidance naming autonomous SWE agents; >3 enterprise deals stall on residency |
| 9 | Distribution channel closes — GitHub/Microsoft de-prioritize third-party Code apps in marketplace | Market | 4 | 2 | **8** | App Store ranking changes; Copilot bundling deepens with VS Code defaults |
| 10 | Hiring gaps in cloud/eval engineering delay async infra | Org / Execution | 3 | 3 | **9** | >2 quarters of unfilled SRE/eval-eng req; on-call burnout signal in async pod |

---

## 2. Top 5 Deep Dive

### Risk #1 — Async agent reliability stalls below the trust threshold

**Risk statement.** Long-horizon async agents need to clear ~85% task success on real-world tickets before users will leave them unattended; if Claude 4.7/5.0 generations plateau in the 70–80% band, the entire async SKU collapses into "expensive synchronous chat."

**Why it matters.** The async bet's whole pricing power is "fire-and-forget for an hour, come back to a PR." At <85% success, users still have to babysit, which destroys the value prop versus our own sync Claude Code at a third the price. Devin's 2024 launch is the cautionary tale.

**Early-warning signals.**
- Internal evals: SWE-bench-Verified, Terminal-bench, and our own private LiveSWE harness. Threshold to act: two consecutive minor model versions where pass@1 moves <2pp.
- Beta telemetry: % of async runs where user discards the PR without merging; % requiring >1 retry; median human-edit-lines-per-merged-PR.
- Qualitative: beta NPS <20 with verbatim theme "I had to redo it."

**Mitigation options.**
1. **Narrow the supported task surface** (e.g., bug fixes + small features only; no greenfield, no infra). Cost: PM/eng ~4 weeks of scoping + UX guardrails. Buys down: ~60% of reliability risk by removing the long tail. Side-effect: caps TAM and gives Devin/Cognition the "we do everything" narrative.
2. **Verifier loop + test-time compute scaling** — agent generates N candidate PRs, executes tests, picks winner. Cost: 6–8 eng-months, +30–60% compute per task. Buys down: ~15–20pp on pass@1. Side-effect: blows up cost economics (see Risk #2).
3. **Human-in-the-loop checkpoints** at planning + pre-merge. Cost: ~4 eng-weeks. Buys down: trust risk specifically, not raw reliability. Side-effect: re-introduces babysitting, undermining the async value prop.

**Recommended.** **Option 1 + Option 2 combined.** Ship async GA only on a narrow, verifier-gated task surface. Owner: Async PM + Eval lead. Decision gate: if at GA-readiness review pass@1 on internal LiveSWE <82% even with verifier loop, delay GA and re-position as "supervised async" rather than autonomous.

---

### Risk #2 — Per-task compute economics don't work

**Risk statement.** A non-trivial async task consumes $5–20 in compute (extended thinking + multi-turn tool use + verifier loop). At a $50/mo Pro tier, the SKU is upside-down after 3–10 tasks; the new-entrant price war on foundation models makes this worse, not better.

**Why it matters.** Either we cap usage (and heavy users churn to Cursor/Copilot), eat the loss (and Finance kills the SKU), or move to metered pricing (and conversion collapses). All three are bad. This is the single biggest threat to the async SKU surviving year 2.

**Early-warning signals.**
- Median tokens-per-completed-task in beta (target: <500K input, <100K output).
- p90 cost-per-task (target: <$6).
- Correlation between user-rated task value and compute cost — if r<0.3, we're spending on tasks users don't value.
- Competitor API pricing: any >30% drop from OpenAI/Google on a frontier-tier model.

**Mitigation options.**
1. **Prompt caching + plan reuse + smaller model on routing/sub-tasks.** Cost: 3 eng-months. Buys down: ~30–40% avg cost. Side-effect: ceiling-limited; doesn't help worst-case heavy users.
2. **Metered overage tier** ("Pro: 20 tasks/mo included, then $1.50/task"). Cost: 2 quarters of billing eng. Buys down: removes the unbounded-loss tail. Side-effect: billing complexity, hostile to power users, churn risk at the overage cliff.
3. **Cap task complexity at lower tiers** (free/Pro can't run agents >30min wall-clock or >100 tool calls). Cost: 4 eng-weeks. Buys down: tail risk. Side-effect: creates an obvious feature gap competitors will weaponize in head-to-head reviews.

**Recommended.** **(1) for all users + (2) as opt-in for the top decile of usage**, gated behind clear in-product cost meter. Defer (3) unless post-launch cost data forces it. Owner: Async PM + Pricing/Finance. Action threshold: if p90 cost-per-task >$8 at end of beta, hold GA until (1) ships.

---

### Risk #3 — Competitor preempts the web/async wedge

**Risk statement.** Cursor, GitHub Copilot Workspace, Cognition (Devin), and Cloudflare are all chasing the same web + async wedge; if any one of them ships with credible quality and lower price before our GA, our launch window closes.

**Why it matters.** First-mover in dev tools is sticky — Copilot's lead persisted for two years despite better competing models. We do not get a second chance to claim "the async agent that actually works."

**Early-warning signals.**
- Cursor's release cadence on hosted/async features (currently rumored Q3).
- Copilot Workspace exit-from-preview date.
- Pricing moves: any competitor announcing async at <$40/mo.
- GitHub marketplace search-rank position for "AI coding agent."

**Mitigation options.**
1. **Compress timeline — ship Web GA in Q2, async beta in Q3 (vs. current Q3/Q4 plan).** Cost: ~$2M in extra eng + accepted feature scope cuts. Buys down: ~50% of preemption risk. Side-effect: increases reliability and reputational risk (#1, #4).
2. **Lock in distribution moats now** — exclusive integrations with Vercel, Sentry, Linear; default-on in Anthropic Console. Cost: BD-led, 1 quarter. Buys down: ~20% (channel-side only). Side-effect: distracts BD from enterprise pipeline.
3. **Differentiate on safety / verifiability** rather than racing on features. Cost: marketing + product narrative reframe. Buys down: protects against being out-featured. Side-effect: cedes "fastest / most capable" narrative.

**Recommended.** **(2) + (3) immediately; (1) only if Cursor or Copilot announces an async tier in the next 60 days.** Owner: GM Claude Code. Action threshold: any competitor public ship of hosted async = trigger emergency replan within 1 week.

---

### Risk #4 — Web UX fails to onboard mainstream devs

**Risk statement.** The Claude Code brand is currently shaped by terminal-fluent power users; a browser-native product targeted at the next 5M devs may feel like a stripped-down toy to existing users and an underpowered IDE to newcomers.

**Why it matters.** Web is the entire mainstream-dev TAM expansion thesis. If D7 retention is <15% (vs. CLI's ~35%), the SKU doesn't earn its build cost, and we've burned brand equity in the process.

**Early-warning signals.**
- D1 → D7 → D28 retention on Web free.
- % of web sessions that end with "open in CLI" CTA click (high = web is a funnel, not a product).
- Task-completion rate in onboarding flow.
- "What were you trying to do?" exit survey themes.

**Mitigation options.**
1. **Closed beta with 500 explicit non-CLI users** (Replit/Vercel/student channels) for 8 weeks before GA. Cost: 1 quarter delay. Buys down: ~50% of UX risk via real telemetry. Side-effect: hands timing advantage to competitors (#3).
2. **Ship Web as a "companion" to CLI, not a replacement** — explicit positioning, no PMF claim. Cost: marketing reframe. Buys down: reputational risk. Side-effect: doesn't unlock mainstream TAM, which was the point.
3. **Bring in an outside UX/design lead** with consumer-product chops (ex-Figma, ex-Notion, ex-Linear). Cost: 1 senior hire, ~1 quarter to ramp. Buys down: ~30%. Side-effect: org friction with existing design.

**Recommended.** **(1) is non-negotiable; (3) in parallel if hiring bar is met.** Owner: Web PM + Head of Design. Action threshold: if closed-beta D7 retention <20%, do not GA — iterate or kill.

---

### Risk #5 — Viral agent failure (reputational / trust)

**Risk statement.** One screenshot of Claude Code deleting a user's repo, force-pushing to main, or leaking a `.env` to a public gist could undo a year of "safety-first" brand positioning that is core to Anthropic's enterprise wedge.

**Why it matters.** Anthropic's whole differentiation versus OpenAI is trust. The async surface multiplies failure modes (unattended, long-horizon, broad tool access). One incident hurts; a pattern is existential.

**Early-warning signals.**
- Sandbox escape attempts in red-team evals (any).
- User-reported destructive-action incidents in beta (target: zero merged-PR force-pushes; zero secret commits).
- Social listening: mentions of "Claude Code" + (deleted | wiped | broke | leaked).

**Mitigation options.**
1. **Hard sandbox + irreversible-op allowlist** (no `rm -rf`, no `git push --force`, no writes outside workspace, no network egress without user confirm). Cost: 6 eng-weeks. Buys down: ~70%. Side-effect: caps agent capability on legitimate ops.
2. **Pre-commit secret scanner + automated revert window** (30-min undo on any agent commit). Cost: 4 eng-weeks. Buys down: ~50% of secret/commit risk. Side-effect: minimal.
3. **Public incident-response playbook + bug bounty** for agent-safety issues. Cost: 1 quarter to stand up. Buys down: reputational tail (turns incidents into trust moments). Side-effect: invites more probing.

**Recommended.** **All three — they're cheap relative to the downside.** Owner: Safety lead + Async eng. Hard gate: no async GA without (1) and (2) shipped and audited.

---

## 3. Cross-cutting Watch List

These are individually mid-tier but combine into killers:

1. **"Copilot ships async + cuts price simultaneously."** Risks #2 + #3 fuse. The combined effect is not additive; it removes both our differentiation and our pricing room. Watch: GitHub Universe and Microsoft Build keynote agendas; any competitor pricing announcement triggers a 48h pricing-room review.
2. **"Reliability misses *and* a viral incident hits in the same quarter."** Risks #1 + #4 fuse. We lose both the rational and emotional case for trust. Watch: incident count × NPS delta — if both move negative same quarter, escalate to CEO review.
3. **"EU AI Act guidance lands *while* we're closing enterprise deals."** Risks #8 + cost. Forces residency + audit retrofits during the exact quarter we need clean revenue. Watch: EU AI Office docket monthly; pre-build EU region in Q2 even if no signed customer yet.
4. **"Org dilution shows up as quality slippage on both tracks."** Risk #6 metastasizes — same tech leads owning both web and async means a slip on one becomes a slip on both. Watch: shared-dependency count between web and async eng plans; if >3, split orgs.

---

## 4. Kill Criteria

We do not get to ride sunk cost. We pre-commit, in writing, to walking away at these thresholds.

**Claude Code Web — kill if, at end of Q1 post-GA:**
- Free→paid conversion <5% **AND** DAU <50K, **OR**
- D28 retention <10% on free tier, **OR**
- >40% of web sessions end with a "switch to CLI" action.
- Action: sunset the standalone Web SKU; fold the best surfaces into the CLI/Console as a thin web companion. Reallocate ~15 eng to async. Owner of kill decision: GM Claude Code + CPO.

**Async Background Agent — kill if, at end of Q2 post-beta:**
- Internal pass@1 on LiveSWE <80% **with** verifier loop on, **OR**
- p90 cost-per-task >$10 with no path to <$6 in 2 quarters, **OR**
- Beta NPS <10 with "had to redo it" >30% of verbatims, **OR**
- A second viral failure incident inside any rolling 60-day window.
- Action: reposition as "supervised async" inside the existing sync product; do not launch a standalone SKU. Owner: GM Claude Code + Head of Safety.

**Combined-bet kill criterion:** if both surfaces miss their Q2 gates, we stop the parallel-bet posture entirely, pick one, and concentrate. We do not let "almost working on both" become a slow-bleed strategy.

The signal we are sending internally: **we will kill our own babies on data, not vibes, and the thresholds are written down before the data arrives.**
