# Product Requirements — Position Risk Assistant

## 1. Problem statement

Open F&O positions change throughout the trading day: prices move, margin requirements shift, and expiry dates approach. A trader holding several positions has to keep checking multiple numbers to know whether anything needs attention.

Most trading apps respond to this with more dashboards, charts, and tables. That's useful, but it doesn't answer the trader's real question:

> **"What needs my attention right now?"**

This is framed here as a **prioritization problem**, not a data-display problem. The product's job is to identify which risk is important enough to act on first, and explain why — not to dump more numbers on the trader.

**Goal:** Reduce the time and effort required for a trader to understand the most important risks in their current positions.

## 2. Why solve this

- Discovering a serious margin shortfall or an approaching expiry too late is a costly, frustrating experience — even when the underlying data was technically available.
- The trader shouldn't have to manually scan every position and compare numbers to find what's urgent.
- Surfacing the 2–3 most important issues, with a plain-language reason, increases trust in the platform — it becomes an assistant rather than just a data source.
- The product must **not** make trading decisions for the user. It surfaces evidence and context; the trader decides what to do.

## 3. Target user

A retail F&O trader with roughly **3–8 open positions**, who checks them periodically during the trading day. Not a professional desk trader managing dozens of positions across multiple accounts.

## 4. Scope

### In scope — v1 risk types

| # | Risk type | Definition | Trigger rule |
|---|---|---|---|
| 1 | Margin risk | Margin usage is getting high and may need attention | `marginUsed / marginAvailable > 0.9` |
| 2 | Expiry risk | Position is close to expiry and may need review | Expiry date within 2 days of today |
| 3 | Concentration risk | Too much exposure sits in one stock/sector | Any single symbol's position value > 40% of total portfolio value |

### Output behavior

- Show only the **top 2–3 risks**, ranked by severity (margin → expiry → concentration).
- **Filter out** any position that doesn't trigger a rule — do not pad the list to reach 3 if fewer real risks exist.
- Each flagged risk includes a plain-language explanation of what's happening and why it was surfaced.

### Out of scope — v1

- Options Greeks or implied volatility analysis
- Multi-account or multi-broker aggregation
- Historical backtesting or P&L analysis
- Live market-price connections
- Automatic trade execution or position closure
- Buy/sell recommendations
- Predictions about future market prices

The prototype uses **static sample data** to validate the risk-identification and prioritization logic, rather than building a live trading system in v1.

## 5. Solution space and decision

Three approaches were considered:

**Option A — Full dashboard, all metrics visible.**
Rejected: still puts the interpretation burden on the trader, who has to search through everything to find what matters.

**Option B — Show only the single biggest risk.**
Rejected: too simple. A trader could have a margin issue *and* an expiry issue simultaneously; showing only one hides the other.

**Option C — Show the top 2–3 risks in priority order.** ✅ **Chosen.**
Balances simplicity and completeness. The trader sees a short, ranked list of what matters without being overwhelmed by a full dashboard.

## 6. Sample data used

```json
[
  { "symbol": "NIFTY24500CE", "qty": 50, "entryPrice": 120, "currentPrice": 95, "expiryDate": "2026-09-12", "marginUsed": 45000, "marginAvailable": 48000 },
  { "symbol": "RELIANCE", "qty": 200, "entryPrice": 2850, "currentPrice": 2790, "expiryDate": null, "marginUsed": 0, "marginAvailable": 0 },
  { "symbol": "BANKNIFTY51000PE", "qty": 25, "entryPrice": 210, "currentPrice": 310, "expiryDate": "2026-09-11", "marginUsed": 30000, "marginAvailable": 31000 },
  { "symbol": "TCS", "qty": 150, "entryPrice": 3900, "currentPrice": 3850, "expiryDate": null, "marginUsed": 0, "marginAvailable": 0 },
  { "symbol": "RELIANCE24500CE", "qty": 75, "entryPrice": 40, "currentPrice": 55, "expiryDate": "2026-09-20", "marginUsed": 20000, "marginAvailable": 60000 }
]
```

## 7. Build iterations

1. **Initial build** — compute all three risk rules against the sample positions.
2. **Refinement** — restrict output to positions that actually trigger a rule; rank by severity.
3. **Rejected direction** — a request to turn this into a "full dashboard showing all metrics for every position" was walked back, since it directly undermines the core prioritization goal (see Option A above).

See [`EVALS.md`](EVALS.md) for how this was tested and judged ready to ship.
