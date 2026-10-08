# Liquidity, execution quality and algorithmic experiments

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-20"></a>

## 20. Liquidity

**ESTABLISHED DOMAIN CONCEPT.** Liquidity is the ability to trade a desired size without a large price concession. Depth measures quantities across prices; a narrow Spread alone is insufficient.

For a funded Market Buy `2 BTC`, maximum `90,000`, in a zero-fee scenario:

| Ask book | Executed Quote value | Average price |
|----------|----------------------|---------------|
| Deep: `5 BTC @ 86,000` | `172,000 USDT` | `86,000` |
| Thin: `0.2 BTC @ 86,000`; `0.3 BTC @ 87,000`; `1.5 BTC @ 90,000` | `17,200 + 26,100 + 135,000 = 178,300 USDT` | `89,150` |

The same request costs `6,300 USDT` more in the thin book. With maximum `87,000`, the thin-book buy would acquire only `0.5 BTC` and terminate its `1.5 BTC` remainder. Funding does not guarantee counterparties. A snapshot can change before an action becomes effective.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-036](decisions.md#oq-036):** funded scenario accounts provide initial liquidity by ordinary orders; their available inventory is finite and is not automatically replenished.

<a id="source-section-21"></a>

## 21. Slippage

**ESTABLISHED DOMAIN CONCEPT.** Slippage compares actual execution against a named reference. It can reflect depth, other participants' actions, and timing; it is distinct from trading fees.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-041](decisions.md#oq-041):** use the opposing best quote actually observed at the participant's decision: Best Ask for BUY, Best Bid for SELL. Adverse slippage per Base unit is `average − reference` for BUY and `reference − average` for SELL. Positive means worse; negative means better. Percentage is that difference divided by reference, multiplied by 100. If reference or fills are absent, report unavailable.

BUY average `86,500` versus observed Ask `86,000` has adverse slippage `500 USDT/BTC`, approximately `0.5814%`; for `2 BTC`, the gross extra cost is `1,000 USDT`. SELL average `85,000` versus observed Bid `85,500` is also adverse by `500`.

**APPROVED — [OQ-008](decisions.md#oq-008):** this measurement does not replace the mandatory absolute Market protection price. A boundary can prevent a worse fill by ending the remainder; it cannot guarantee a full fill.

<a id="source-section-22"></a>

## 22. Market Impact

**ESTABLISHED DOMAIN CONCEPT.** Market Impact concerns market changes caused by trading. Assume Last Price `86,000`; Asks `0.5 BTC @ 86,000`, `0.5 @ 87,000`, `2 @ 88,000`. A Market Buy `2 BTC`, maximum `88,000`, executes across all three levels.

Average price is `(43,000 + 43,500 + 88,000) / 2 = 87,250`. The last execution sets Last Price to `88,000`; `1 BTC` remains at that Ask. With at least `2 BTC` originally offered at `86,000`, the buy would not have needed higher levels.

This illustrates immediate impact, not a permanent-price prediction. Slippage measures execution relative to a reference; impact concerns changes attributable to the order. A cancellation can move quotes without moving Last Price.

<a id="source-section-28"></a>

## 28. Algorithmic Trading

**ESTABLISHED DOMAIN CONCEPT.** Algorithmic Trading automates decisions to submit, cancel, or do nothing. Quantitative Trading uses quantitative evidence to guide decisions. Exchange Matching instead determines which submitted orders execute under the venue's rules. A strategy requesting `2 BTC` must evaluate actual fills, not assume it acquired `2 BTC`.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-040](decisions.md#oq-040):** V1 supports strategy participants using simulated market information and their own balances to submit/cancel ordinary V1 orders. Controlled synthetic experiments may be rerun as new experiments with declared initial funding, fee rates, action schedules, and timing assumptions. Reproducibility requires the same scenario and participant decisions; a changed strategy is a changed experiment input.

Historical-data backtesting, external venue/data integration, optimization suites, and strategy marketplaces are deferred beyond V1. Backtesting remains a useful domain concept: evaluating a strategy on past data requires explicit assumptions about fills, liquidity, fees, and timing. Synthetic scenario reruns are not claimed to prove historical-market performance.

<a id="source-section-29"></a>

## 29. HFT

**ESTABLISHED DOMAIN CONCEPT.** HFT involves frequent automated decisions, latency sensitivity, queue position, and execution quality; it is not simply “a bot that trades fast.” The SEC's [historical HFT research review](https://www.sec.gov/marketstructure/research/hft_lit_review_march_2014.pdf) provides background on these distinctions.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-041](decisions.md#oq-041):** V1 supports controlled experiments with an explicit nonnegative observation delay and action delay for each participant, fixed at setup for that experiment. Compare different delays in separate experiments; random timing jitter is deferred. All delays use the virtual experiment clock. Observation delay affects what market information a participant has seen; action delay affects when its order/cancellation reaches the platform's authoritative business ordering. Neither participant timestamps nor configured speed bypass existing priority.

Observe per-order fill ratio (`filled / original`), quantity-weighted execution price, observed-quote slippage, actual fees, fee-inclusive average buying cost/net selling proceeds per filled unit, time to first fill, time to full fill, and effective cancellation outcome. Unachieved first/full fills have unavailable elapsed metrics, not zero. Also observe own same-price queue order count/quantity ahead and resulting portfolio PnL. Times use experiment-clock intervals; displayed-time ties follow authoritative business order.

Example: A and B each rest `1 BTC @ 86,000`, A earlier. An incoming buy of `0.5 BTC` fills A while B receives nothing. Altering B's delay in another experiment may change acceptance order, but cannot change priority retrospectively.

This is an HFT simulation/research platform, not a production HFT venue. Real-network/hardware benchmarking and production latency targets are beyond V1 business scope. Synthetic delay and trading outcomes are reported as simulation results, not real-world profitability or infrastructure performance.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-algo-001"></a>BR-ALGO-001 | Include synthetic strategy/queue/timing experiments and execution-quality measures; defer historical backtesting. | Research value within a manageable V1. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-040](decisions.md#oq-040), [OQ-041](decisions.md#oq-041) |

## Phase B — behavioral analysis

### Purpose, actors and objects

Strategy Participant makes trading decisions from observed information and assigned capital; Exchange matching applies ordinary rules to its resulting Orders. The source supports synthetic queue/execution/delay studies as **proposed**, without designing strategy algorithms, historical backtests or physical latency benchmarks.

| Object / research fact | Relevant attributes | Lifecycle / boundaries |
|------------------------|---------------------|------------------------|
| Strategy Participant | Its account/capital, declared decisions, fixed observation and action delays | [OQ-040](decisions.md#oq-040)/[OQ-041](decisions.md#oq-041)/[OQ-043](decisions.md#oq-043); no STARTED/STOPPED strategy enum, algorithm/cadence or membership lifecycle specified |
| Observation latency | Delay affecting which market information has been seen | Nonnegative, fixed per run; exact precision/delivery scope [GAP-HFT-001](review/gap-register.md#gap-hft-001)/[GAP-HFT-002](review/gap-register.md#gap-hft-002) |
| Action latency | Delay before Order/cancel reaches authoritative business ordering | No priority bypass; late arrivals/end boundary [GAP-EXP-004](review/gap-register.md#gap-exp-004) |
| Queue observation | Own same-price earlier active order count and quantity | [OQ-037](decisions.md#oq-037); observation doesn't guarantee fill; applicability to never-resting Orders [GAP-HFT-004](review/gap-register.md#gap-hft-004) |
| Execution metrics | Filled/original, executed weighted average, fees/unit cost/net proceeds, observed-quote slippage, first/full timing and effective cancel outcome | [OQ-041](decisions.md#oq-041); origin of achieved elapsed metrics [GAP-HFT-003](review/gap-register.md#gap-hft-003); no-fill metrics unavailable except ratio zero |

### Preconditions, flow and postconditions

Use separate configured experiment inputs to compare delays; rates/delays remain fixed within each run. A changed decision/strategy is also a changed input, not an identical replay ([OQ-040](decisions.md#oq-040)/[OQ-041](decisions.md#oq-041)). Ordinary validation/funding/STP/priority remain applicable. Own capital/identity and scenario-hosted strategies require [GAP-ACT-001](review/gap-register.md#gap-act-001)/[GAP-ACT-002](review/gap-register.md#gap-act-002) review.

### Decision table — timing and execution quality

| Initial business situation | Trigger / condition | Outcome | State / financial / research consequences | Basis | Status |
|----------------------------|---------------------|---------|------------------------------------------|-------|--------|
| Declared delay pair | Zero / positive / negative delay | Zero/positive permitted; negative invalid; exact representability OPEN | Zero does not bypass priority or guarantee fill | [OQ-010](decisions.md#oq-010)/[OQ-041](decisions.md#oq-041); [GAP-HFT-001](review/gap-register.md#gap-hft-001) | PROVISIONAL / OPEN precision |
| Old observed quote | Action later reaches platform | Match actual then-eligible book within protection | Different fill/price/fee possible; use decision-observed reference for slippage | [OQ-008](decisions.md#oq-008)/[OQ-041](decisions.md#oq-041) | PROVISIONAL; delivery [GAP-HFT-002](review/gap-register.md#gap-hft-002) |
| Positive original, no fills | Report execution metrics | Fill ratio zero; average price/unit cost/slippage/first/full timing unavailable | No execution fee; no ownership from requested quantity | [OQ-041](decisions.md#oq-041) | PROVISIONAL |
| Partial fills | Report metrics | Actual ratio/weighted price/cost/fees; full-fill timing unavailable | Earlier fills count; unfilled units not owned | [OQ-015](decisions.md#oq-015)/[OQ-016](decisions.md#oq-016)/[OQ-041](decisions.md#oq-041)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Fills but absent observed opposing quote | Report slippage | Unavailable | Do not substitute later quote, midpoint or protection boundary | [OQ-041](decisions.md#oq-041) | PROVISIONAL |
| Known observed reference and fills | Average worse / better | Adverse slippage positive / negative by side formula in §21 | Not a fee and not permission to exceed protection | [OQ-008](decisions.md#oq-008)/[OQ-041](decisions.md#oq-041)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Accepted same-price Orders | Queue competition | Earlier sequence remains ahead; partial queue-ahead changes from actual events | Delay change in another run cannot retrospectively reorder | [OQ-010](decisions.md#oq-010)/[OQ-037](decisions.md#oq-037)/[OQ-041](decisions.md#oq-041) | PROVISIONAL complete research flow |
| Achieved first/full fill | Report elapsed time | UNDECIDED numeric origin — [GAP-HFT-003](review/gap-register.md#gap-hft-003) | No arbitrary decision/submission/arrival/acceptance origin | [OQ-041](decisions.md#oq-041) | OPEN |

### No action, time boundaries and comparison limits

“Do nothing” is a strategy decision with no submitted trading action; existing resting Orders can still fill from others. No participant action does not guarantee zero experiment Trades when scenario actions exist. Different delays may affect what is observed and acceptance order; faster configured delay need not yield better net results. Equal scheduled arrivals, clock progress, exact end/pending arrivals and observation coherence are unresolved independently of accepted-order priority.

[BS-HFT-001](scenarios/algo-hft.md#bs-hft-001)–[BS-HFT-008](scenarios/algo-hft.md#bs-hft-008) and [BS-MAT-004](scenarios/matching-stp.md#bs-mat-004) cover delayed observation/action, zero delay, queue, slippage/no-fill metrics, separate comparisons, strategy ownership and achieved metric origins. [DIA-LATENCY](diagrams/README.md#dia-latency), [DIA-OBS](diagrams/README.md#dia-obs) and [DIA-METRICS](diagrams/README.md#dia-metrics) show sourced distinctions with OPEN nodes. Review time/financial dependencies before comparing studies; no real-network/hardware performance is specified.
