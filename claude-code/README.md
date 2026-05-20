# Claude Code — AI PM Case Study

**Thesis:** Anthropic can 5–10× Claude Code revenue in 18 months by anchoring on **Enterprise + GitHub-native distribution** before chasing Web/Async — counter to the more "viral" Web + Async bets that initial intuition suggested.

> **🎯 The 60-second view:** open [`walkthrough.html`](./walkthrough.html) — a single self-contained HTML page that walks through the entire strategy with charts, ICE rankings, prototype preview, and launch timeline. Designed to be the interview-room artifact.

## Structure

| # | Section | What's in it |
|---|---|---|
| 1 | [Market analysis](./01-market-analysis.md) | TAM/SAM math, 5-segment competitive map (IDE / CLI / Async / Cloud-native / Code-review), Claude Code's differentiated wedge, 12-month forecasts |
| 2 | [SWOT](./02-swot.md) | 4-quadrant analysis grounded in public evidence + cross-linked synthesis |
| 3 | [Revenue-growth opportunities](./03-opportunities.md) | 9 opportunities scored by ICE, ranked top-5 table, strategic recommendation + what to deprioritize |
| 4 | [Prototype](./04-prototype/) | Interactive HTML mockup of the async-agent fleet dashboard (5 concurrent agents, lifecycle stages, transcripts, PR drafts) |
| 5 | [Evaluation framework](./05-evaluation.md) | North-star metric defense, metric tree, 3-layer eval framework (Capability / Calibration / Product), A/B design, pitfalls |
| 6 | [Risks](./06-risks.md) | 10-row risk register with severity × likelihood, top-5 deep-dive with mitigation options, kill criteria with numeric thresholds |
| 7 | [Launch plan](./07-launch-plan.md) | 22-row 90-day timeline, distribution strategy, pricing math, 3 growth loops, comms sequence, what to say "no" to |

## How the analysis updated the priors

The case study deliberately captures a real PM moment: **the initial hypothesis (Claude Code Web + Async Background Agent) gave way to a different recommendation (Enterprise tier + Native GitHub PR App) once ICE-scored against revenue ceiling, demand evidence, and competitive timing.** The walkthrough makes this transparent — interview signal is "I update my priors based on data", not "I picked a winner and worked backward".

The eval / risks / launch sections were drafted against the original prior. The methodology transfers cleanly to the final picks; a real shipping team would re-run the launch table against the final SKUs.

## How this case study was built

Eight parallel Claude Code agents — one per section (market / SWOT / opportunities / prototype / eval / risks / launch) plus a synthesis agent for the walkthrough HTML. Total wall-clock: ~7 minutes for content + ~5 minutes for the walkthrough. The case study itself is a demonstration of AI-native PM work — leveraging fleet-of-agents to compress what would historically be a 2-week deep dive into an afternoon.

## Author

Tracy Chen — chenxi.tracy@gmail.com
