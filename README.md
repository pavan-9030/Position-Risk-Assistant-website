Position Risk Assistant

A prioritization tool for F&O traders — built as a product management case study

🔗 Live prototype: swift-risk-alert.lovable.app

Author: J. Pavankumar 

Overview

Position Risk Assistant is a lightweight web app that looks at a trader's open Futures & Options (F&O) positions and surfaces the top 2–3 risks that need attention right now — instead of showing every metric and leaving the trader to figure out what matters.

This repo documents the project the way a product manager would: the problem, why it's worth solving, the scope decisions, the alternatives considered and rejected, and how "good enough to ship" was defined and tested. The code itself is a prototype built on static sample data — the goal was to validate the risk-identification and prioritization logic, not to ship a production trading system.

The problem (as I framed it)

Open F&O positions change throughout the day — prices move, margin requirements shift, expiries get closer. Most trading apps respond to this by adding more dashboards, charts, and tables. That information is useful, but it still leaves the trader asking the same question:

"What needs my attention right now?"

I treated this as a prioritization problem, not a data-display problem. The tool shouldn't just show exposure, margin, and expiry data — it should decide which risk is important enough to look at first, and explain why in plain language, while leaving the actual trading decision to the user.

Why this is worth solving
Discovering a serious margin issue or an approaching expiry too late is a costly, frustrating experience for a retail trader.
The underlying data is usually already available — the real gap is triage, not visibility.
Surfacing the 2–3 issues that matter, with a plain-language reason, builds trust in the platform: it feels like an assistant, not just a data terminal.
The tool intentionally does not make trading decisions for the user — it provides evidence and context, and stops there.
Target user & scope

Persona: A retail F&O trader with ~3–8 open positions, checking them during the trading day.

In scope (v1) — three risk types:

Risk type	Trigger rule
Margin risk	marginUsed / marginAvailable > 0.9
Expiry risk	Expiry date within 2 days of today
Concentration risk	Any single symbol > 40% of total portfolio value

Output is intentionally short: the top 2–3 risks, ranked by severity, each with a plain-language explanation of what's happening and why it was flagged.

Explicitly out of scope for v1:

Options Greeks / implied volatility analysis
Multi-account or multi-broker aggregation
Historical backtesting or P&L analysis
Live market-price connections
Automatic trade execution or position closure
Buy/sell recommendations or price predictions

Keeping the scope tight was a deliberate call — the point was to prove the prioritization logic worked, not to compete with a full trading dashboard on day one.

Solution space — alternatives I considered
A full dashboard showing every metric — rejected. This just puts the interpretation burden back on the trader; they'd still have to search for what matters.
Show only the single biggest risk — rejected. Too simple; it can hide a genuine second problem (e.g., a trader can have a margin issue and an expiry issue at the same time).
Show the top 2–3 risks, ranked by priority — chosen. Balances simplicity with completeness: a small, ranked list without dashboard overload.
How it was built

The prototype was built through iterative prompting rather than manual coding, which mirrors how I'd work with an engineering team — starting broad, then refining based on gaps:

Initial build — compute all three risk rules against sample position data.
Refinement — filter the output to only positions that trigger a rule, and rank by severity (margin → expiry → concentration).
Rejected direction — an attempt to turn it into a "full dashboard showing all metrics for every position" was deliberately walked back, since it worked against the core prioritization goal.

See docs/PRD.md for the full requirements detail and docs/EVALS.md for how the output was tested and judged ready to ship.

What this project demonstrates (PM/Project Management lens)
Problem framing over feature listing — starting from "what decision is the user stuck on" rather than "what data can we show."
Deliberate scoping — a clear, written in/out-of-scope list to keep v1 achievable.
Trade-off analysis — three alternative solutions evaluated on paper before picking one, with reasons for rejecting the other two.
Definition of "done" — concrete pass/fail test cases tied back to the original problem statement, not just "does it run."
Backlog thinking — an explicit list of open questions for the next iteration (tie-breaking logic, combined risk types, drill-down detail, trust in suggested actions).
