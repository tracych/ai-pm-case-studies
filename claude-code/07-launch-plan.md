# Claude Code — Launch & Growth Plan

*Section 7 of the Claude Code AI-PM case study. Audience: hiring managers evaluating PM shipping experience.*

---

## 1. The bet & the sequence

Ship **Async Background Agent in Q1** as a high-margin add-on sold to the existing Claude Code CLI installed base, then ship **Claude Code Web in Q2** as the mainstream wedge, with Web's free tier subsidized by Async's premium ARPU. The sequence is non-obvious and deliberate. Async ships first because (a) the buyer is a known, instrumented user — fastest learning loop on agent reliability, which is the bottleneck for both products; (b) it monetizes immediately at $50–$200/seat, funding the GPU bill for Web's free tier; (c) it lets us pressure-test long-horizon agent infra (sandboxing, PR generation, retry semantics) on forgiving users before exposing it to a cold-traffic browser audience. Web ships second because the mainstream developer's first impression of Claude Code must be a working PR, not a half-baked agent. Web without Async-grade reliability is a launch we'd regret; Async without Web is a quarter of revenue we'd still happily take.

---

## 2. 90-day launch timeline

Timeline assumes T0 = Async Background Agent alpha kickoff. Web track runs in parallel starting Week 5.

| Phase | Week | Workstream | Milestone | Owner | Success criterion |
|---|---|---|---|---|---|
| Pre-launch | 1–2 | Eval | Hold-out task suite v1 (Async) built | ML eval lead | 100 tasks across 8 categories; baseline ≥60% completion, ≥40% merge-rate in sandboxed repos |
| Pre-launch | 1–2 | Infra | Sandboxed exec environment + 15-min task budget shipped | Infra eng lead | p95 cold-start <8s, 99.5% sandbox isolation pass rate on red-team suite |
| Pre-launch | 2 | Safety | Prompt-injection + RCE red-team v1 | Security PM | Zero P0 escapes in 500-prompt suite; 3 mitigations landed |
| Alpha | 3 | Distribution | 25 internal Anthropic engineers running Async on real repos | DevRel lead | ≥15 DAU in week 1, ≥10 PRs merged into production code |
| Alpha | 3–4 | Product | In-CLI `claude async` subcommand GA in nightly | Product eng lead | <2s handoff latency from CLI to async task; ≥80% of alpha users invoke it unprompted |
| Closed beta | 4 | Distribution | 50 invited design partners onboarded (Async) | DevRel | 30+ active in week 1, 10+ public testimonial commitments |
| Closed beta | 5 | Web track | Web prototype deployable behind feature flag | Web eng lead | 5-min p95 task completion; 200 internal sessions logged |
| Closed beta | 5–6 | Pricing | Pricing research: 40 interviews + WTP survey (n=800) | Product + Bizops | 3 candidate price points with ≥30% stated-intent at top tier |
| Closed beta | 6 | Eval | Async hold-out suite v2 (200 tasks, 12 categories) | ML eval lead | ≥70% completion, ≥55% merge-rate, regression-flag harness wired to CI |
| Closed beta | 6 | Comms | 3 customer case studies drafted (startup / mid-market / OSS) | Comms | All 3 quotes signed; ≥1 with named logo |
| Public preview | 7 | Distribution | Async public preview waitlist opens | Growth PM | 10K signups in 7 days; ≤20% bot-rate after filter |
| Public preview | 8 | Pricing | Pricing page live with usage-based + tier-based variants | Product + Bizops | A/B test 3 price points across 5K users; ≥10% trial→paid conversion at winner |
| Public preview | 8 | Web track | Web closed beta opens (100 invited) | Web PM | ≥40% D7 retention; 25+ session length minutes median |
| Public preview | 9 | Integrations | GitHub App for Async (PR attribution + comments) | Integrations eng | Installed on 200+ repos in week 1; <5% uninstall rate |
| Public preview | 10 | Community | Plugin marketplace v1 (Async hooks) live | Ecosystem PM | 15 third-party plugins published in 2 weeks; ≥5 with >50 installs |
| Public preview | 10 | Safety | External red-team contract (Trail of Bits or equivalent) report delivered | Security PM | Zero unmitigated P0/P1 at GA gate |
| GA | 11 | Comms | Launch keynote + Anthropic CEO blog + engineering infra post | Comms + Marketing | 50K homepage visits in 48h; top-3 HN slot for ≥6h |
| GA | 11 | Distribution | Async GA + Web public preview opens same day | Growth PM | 25K Web signups week 1; 5K paid Async seats week 1 |
| GA | 11 | Sales | Enterprise pricing sheet + 5 named-account briefings | Enterprise sales | 3 enterprise pilots signed in 30 days post-GA |
| GA | 12 | Comms | 5 customer case studies live + dev-Twitter activation | Comms + DevRel | ≥20 high-credibility dev voices post organically in 72h |
| GA+ | 12 | Telemetry | Post-launch metrics dashboard shipped | Data PM | Daily exec view: DAU, merge-rate, NPS, gross margin per task |
| GA+ | 13 | Reliability | First post-launch eval regression review | ML eval lead | Merge-rate held ≥65% on live traffic; rollback plan tested |

---

## 3. Distribution strategy

**Primary channel — installed-base upsell (Async):** The Claude Code CLI already has hundreds of thousands of authenticated, instrumented users. Async ships as a `claude async` subcommand inside the existing binary — zero new install friction, zero new auth. The first 10K paid seats come from a targeted in-CLI nudge to users who have run ≥20 long-form sessions in the last 30 days. CAC is effectively zero; the only cost is one well-designed in-product moment and one email. This channel alone should clear the Q1 revenue plan; everything else compounds on top.

**Mainstream channel — developer content + GitHub (Web):** Web's audience is the developer who has heard of Claude Code but never installed a CLI. We meet them where they read: a top-of-HN technical post on the infra (not the product), three serialized YouTube deep-dives with high-trust dev creators on flat fee (not rev-share — see below), and an active dev-Twitter presence from the engineering team, not marketing. The discovery surface is **GitHub**: a "Try Claude Code on this PR" button injected by the GitHub App becomes our top-of-funnel for free signups. Every PR comment is a billboard.

**Community channel — plugin marketplace:** Third-party plugins are the flywheel. We seed it by hand-recruiting 15 plugin authors during closed beta (paid bounty of $5K each plus marketplace placement), then go open. Every plugin install is both a retention event for the installer and an acquisition event for the plugin author's audience. Target: 100 plugins with >100 installs each by end of Q2.

**Enterprise channel — top-down through existing API relationships:** Anthropic already has signed master agreements with most of the Fortune 500. The Async + Web bundle gets sold into those accounts by named-account reps with security review pre-cleared. The pitch isn't "buy a new product" — it's "your devs are already pasting code into Claude; here is the governed, audited version." Land via SSO + audit log, expand via seats.

**What we explicitly do not do:**
- **No paid social (Meta, TikTok, X ads).** Developer audience density is too low; the CPM-to-qualified-signup ratio is roughly 50x worse than HN front page or a single well-placed YouTube deep-dive.
- **No paid influencer dev promo (rev-share or sponsorships disclosed as such).** This audience punishes manufactured enthusiasm. Pay creators flat for honest reviews and let them say it's mid if it's mid; we'd rather a 7/10 honest review than a 10/10 sponsored one. The launch survives skepticism; it does not survive a credibility hit.

---

## 4. Pricing

**Async Background Agent — usage-based with a seat floor.** $50/seat/month includes 100 task-hours; additional task-hours metered at $0.40/hr. Defense: async tasks have wildly variable cost (a 30-second lint fix vs a 12-minute multi-file refactor), so pure seat pricing leaves margin on the floor for power users and scares off light users. Pure usage pricing makes the bill unpredictable, which finance teams hate. Hybrid splits the difference and lets us upsell heavy teams onto enterprise contracts.

**Claude Code Web — freemium with a quality gate, not a feature gate.** Free: 20 tasks/month, single repo, public repos only, watermarked PRs. Paid: $25/seat/month, unlimited tasks, private repos, no watermark, priority queue, Async included. We gate on *what the user can do at production scale*, not on which buttons they see — feature gates make the free product feel crippled and breed resentment.

**WTP anchors:** Cursor sits at $20 individual / $40 business. Copilot at $19 / $39. Devin at $500/mo with a 12-month commit. Claude Code Web at $25 slots above Copilot (justified by agent capability), well below Devin (which is sold as autonomous, not assistive). Async at $50/seat sits in a category Cursor and Copilot do not occupy — async PR generation — and is priced to a per-PR ROI of ~$8/PR at average team velocity, an easy sell to any eng manager.

**Anti-cannibalization for existing API customers:** Async usage that runs through a customer's own API key bills against their existing API contract at a 15% discount vs the seat price. This is a margin hit on paper but defends the API relationship — without this rule, every API customer's procurement team starts asking why they're paying twice.

---

## 5. Growth loops

**Loop 1 — PR-merged flywheel (Async, primary loop)**

> User runs Async → agent opens a PR → PR description includes a small "Generated with Claude Code · [view trace]" footer → reviewer clicks trace, sees the reasoning, gets curious → reviewer installs CLI → reviewer's team standardizes on Claude Code → more PRs.

**Why this works:** every merged PR is a high-trust referral from a teammate, which is the highest-converting acquisition surface that exists for developer tools. The trace link is the *value-add disguised as attribution* — reviewers click it because it helps them review, not because it's marketing.

**What breaks it:** if the PR quality drops below the team's bar, the attribution becomes a negative brand signal — every bad PR is now a billboard saying "Claude Code wrote this slop." Eval gate: attribution does not ship until live merge-rate is ≥65% for 14 consecutive days in beta. If merge-rate drops below 55% post-launch, attribution auto-disables.

**Loop 2 — Plugin marketplace flywheel (community, secondary loop)**

> Plugin author publishes a plugin → users install it → installs drive plugin author's reputation → plugin author writes about Claude Code on their own channels → their audience signs up → some of them become plugin authors.

**Why this works:** the marketplace turns retention into acquisition by giving power users a status game (install counts, reviews) that they play in public. We're not paying for the marketing; the status incentive does it. The flywheel kicks in once the marketplace has roughly 50 active plugin authors and 10K monthly plugin installs.

**What breaks it:** quality drift and security incidents. One malicious plugin tanks the whole channel. Mitigation: every plugin code-reviewed by Anthropic for the first 6 months; sandboxed execution model so plugins cannot exfiltrate code or credentials by construction.

**Loop 3 — Enterprise land-and-expand (paid loop)**

> One team in a Fortune 500 buys 10 seats → IT sees governed usage → IT issues an org-wide policy → seats expand to 200 → expansion triggers an enterprise contract → enterprise contract includes Async, which spreads adoption further inside the org.

**Why this works:** governance is the wedge that enterprise IT actually pays for. Once IT has a story for "Claude Code is the sanctioned way," shadow-IT alternatives lose. Land target: 30 seats average. Expand target: 5x within 12 months.

---

## 6. Comms / launch sequence

**T-7 days:** Brief 15 high-credibility dev voices under embargo — mix of OSS maintainers (Sindre Sorhus, Mitchell Hashimoto-tier), eng leaders (Charity Majors, Kelsey Hightower-tier), and dev creators (ThePrimeagen, Theo, Fireship). Goal: not endorsements, just informed opinions on day one.

**T-1 day:** Engineering blog post goes live on the infra story — sandboxing model, eval methodology, how we hit the merge-rate number. This is the credibility post; senior engineers will share it.

**T-0 morning (Tue, 9am PT):** CEO blog + announcement video. Coordinated HN post by an Anthropic engineer (not marketing) with the engineering blog as the link, not the product page — keeps the thread technical.

**T-0 + 4h:** Five case studies go live on the website: one Series A startup (Vercel-shape), one mid-market (5000-eng-org), one OSS maintainer, one enterprise pilot, one solo dev. Each ≤500 words, each with a concrete merge-rate or hours-saved number.

**T+1 to T+7:** Dev-Twitter activation rolls out as scheduled organic posts from the 15 briefed voices. No paid amplification. AMA on Friday with the eng lead, not the CEO.

**Killswitch:** if HN sentiment turns negative in the first 4 hours (defined as <40% upvote ratio in top thread), we kill paid promotion, the eng lead posts a substantive response in-thread, and we publish a follow-up technical post within 48h. We do not delete, do not astroturf, do not get defensive.

---

## 7. What I would say "no" to in the launch

1. **No celebrity dev partnership for launch.** A flashy "X uses Claude Code" splash post is manufactured social proof that this audience smells immediately. It does not survive the first time the product underperforms in a video review. Trade real testimonials with numbers for celebrity quotes every time.

2. **No "Devin-killer" comparative messaging.** Naming a competitor anchors the product against them and sets up an unflattering future comparison the first time our reliability dips below theirs on any axis. Also: Devin has a credibility problem we don't need to inherit. We compete on our own substrate: developer trust, eval transparency, and the existing Claude Code installed base.

3. **No enterprise-only at launch.** The category is won bottoms-up by individual developers who bring the tool into their team. Going enterprise-first would optimize for one quarter of revenue and lose the next four quarters of category position. Enterprise is the *expand* motion, not the *land* motion — Q1 is for landing.
