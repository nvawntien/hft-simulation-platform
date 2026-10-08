# Order book and market data

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-9"></a>

## 9. Order Book

**ESTABLISHED DOMAIN CONCEPT.** The book contains active resting interest, not Trade history. V1 resting interest consists of Limit Orders.

| Side | Price (USDT/BTC) | Active quantity (BTC) |
|------|------------------|-----------------------|
| Ask | `88,000` | `3` |
| Ask | `87,000` | `2` |
| Ask — best | `86,000` | `1` |
| Bid — best | `85,500` | `1.5` |
| Bid | `85,000` | `2` |
| Bid | `84,000` | `4` |

Best Bid is the highest bid, `85,500`; Best Ask is the lowest ask, `86,000`; Spread is `500 USDT/BTC`. The `1 BTC` Ask could aggregate separate orders of `0.4` and `0.6 BTC`; aggregation does not merge their ownership or priority.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-037](decisions.md#oq-037), [OQ-044](decisions.md#oq-044):** expose full aggregated price-level depth, best prices, and each participant's own orders and queue observations. Do not expose other accounts' identities or individual order histories. An absent side has an unavailable best price; if either side is absent, Spread is unavailable, not zero. Cancellation can change quotes without changing Last Price.

<a id="source-section-19"></a>

## 19. Market Price Concepts

**ESTABLISHED DOMAIN CONCEPT.** Last Price is the latest Trade price; Best Bid/Ask describe current resting interest; Average Execution Price is total executed notional divided by executed Base quantity, excluding fees.

Example: an incoming buy executes `1 BTC @ 86,000`, then `2 BTC @ 87,000`. Its average is `86,666.66666667` when displayed to 8 decimals. Suppose afterward Best Bid is `85,500`, Best Ask `88,000`. Last Price is `87,000`: all four values differ.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-034](decisions.md#oq-034), [OQ-044](decisions.md#oq-044), [OQ-046](decisions.md#oq-046):** Last Price is unavailable before the first Trade. No fill means Average Execution Price is unavailable, not zero. A quote is limited in quantity and is not guaranteed liquidation value. Derived price displays round half-up to 8 decimals without changing actual Trade prices or funding amounts.

<a id="source-section-23"></a>

## 23. Market Data

**ESTABLISHED DOMAIN CONCEPT.** Market data describes standing interest and completed activity: order-book depth, best quotes, Trades, Last Price, volume, and OHLCV. Orders submitted, funding grants, and cancellations are not traded volume.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-037](decisions.md#oq-037), [OQ-038](decisions.md#oq-038), [OQ-044](decisions.md#oq-044):** provide full aggregated depth, Best Bid/Ask/Spread, anonymous Trade history, Last Price, and both Base and USDT gross traded volume. Count each actual Trade once per symbol; report each one-minute interval and cumulative experiment totals. Fees are separate and do not inflate Quote volume. No rolling 24-hour statistic in V1.

Participants also see their own active orders, acceptance priority, same-price order count and quantity ahead, fills, fees, balances, and termination reasons. Queue observations include all active earlier same-price orders; they are observations, not guaranteed future fills. Other accounts' identities/order histories remain private.

**APPROVED — [OQ-003](decisions.md#oq-003), [OQ-011](decisions.md#oq-011):** Simulation Funding and prevented self-matches contribute no Trade/volume/Last Price. STP may change depth because it cancels resting interest. External market-data integration is beyond V1 (**[OQ-002](decisions.md#oq-002)**).

<a id="source-section-24"></a>

## 24. Candlesticks / OHLCV

**ESTABLISHED DOMAIN CONCEPT.** Open/High/Low/Close are the first/highest/lowest/last Trade prices within an interval. Volume sums actual executions, counting each Trade once.

| Experiment time | Trade price (USDT/BTC) | Quantity (BTC) | Notional (USDT) |
|-----------------|------------------------|----------------|-----------------|
| 10:00:05 | `86,000` | `1` | `86,000` |
| 10:00:20 | `88,000` | `0.5` | `44,000` |
| 10:00:35 | `85,000` | `2` | `170,000` |
| 10:00:50 | `87,000` | `0.5` | `43,500` |

For that minute: Open `86,000`, High `88,000`, Low `85,000`, Close `87,000`, Base Volume `4 BTC`, Quote Volume `343,500 USDT`. The chart follows Trades; it does not determine prices. Close is not the average.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-035](decisions.md#oq-035), [OQ-039](decisions.md#oq-039):** V1 has one-minute candles only, on the platform's virtual experiment clock, starting at `00:00:00 UTC` on the declared scenario date. Intervals include their start and exclude their end: a Trade at `10:01:00` belongs to the next minute. Same-time Trades follow authoritative business execution order.

For an empty interval, show unavailable OHLC and zero volume; do not carry forward a fake candle price. Last Price may separately retain the last actual Trade. At experiment end, retain and label any partial final interval. Time pauses when the experiment clock is stopped; local client time never groups Trades or decides priority.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-mkt-001"></a>BR-MKT-001 | Last Price and Trade summaries use actual simulated Trades; no seeded/external price substitution. | Distinguish inputs from executions. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-038](decisions.md#oq-038), [OQ-039](decisions.md#oq-039), [OQ-044](decisions.md#oq-044) |
| <a id="br-mkt-002"></a>BR-MKT-002 | Provide full aggregated depth and own-account queue observations, without other-account identity/history. | Support understandable queue research. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-037](decisions.md#oq-037) |
| <a id="br-mkt-004"></a>BR-MKT-004 | Publish one-minute and experiment-cumulative Base/USDT volume, one-minute candles, and empty OHLC as unavailable. | Consistent market summaries. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-038](decisions.md#oq-038), [OQ-039](decisions.md#oq-039) |

## Phase B — behavioral analysis

### Purpose, actors and object attributes

Participants observe interest and actual executions. Public aggregate facts are distinct from private own-account facts. An observation may be older than the effective book when the participant acts; it cannot guarantee execution or create trading rights.

| Object | Attributes / source meaning | Beginning, changes and end |
|--------|-----------------------------|----------------------------|
| Order Book | Symbol, active aggregated price levels, best quotes; individual ownership/sequence retained underneath | Setup funding alone empty; rests/fills/cancels/STP/end alter quantities ([OQ-036](decisions.md#oq-036)); no formal EMPTY/ONE_SIDED states |
| Market Data Observation | Observed depth/quotes/anonymous Trades/Last Price, own queue/fill/balance details as permitted | [OQ-037](decisions.md#oq-037) proposed content; precise observation instant/coherence/delivery lifecycle [GAP-MKT-001](review/gap-register.md#gap-mkt-001)/[GAP-HFT-002](review/gap-register.md#gap-hft-002) |
| Candle Interval | One-minute virtual UTC interval, actual Trades in execution order, OHLC, Base/gross Quote volume, final partial label | Start-inclusive/end-exclusive; first/last Trade determine Open/Close; empty has no OHLC; effective run end retains partial interval ([OQ-039](decisions.md#oq-039)) |
| Last Price fact | Latest actual Trade price and virtual occurrence time used for age | Unavailable before first Trade ([OQ-044](decisions.md#oq-044)); quote/cancel/STP/funding cannot set it; retain with stale flag in Portfolio |

Market observations and Candle summaries are derived facts, not a matching input that sets execution prices. No formal observation CREATED/DELIVERED lifecycle is adopted; delivery semantics need decisions. Same-time accepted order priority is separate from same-time Trade execution ordering used for candle Open/Close.

### Preconditions, flow and postconditions

Canonical observation policy is §23/[OQ-037](decisions.md#oq-037)/[OQ-038](decisions.md#oq-038)/[OQ-044](decisions.md#oq-044); interval grouping is §24/[OQ-035](decisions.md#oq-035)/[OQ-039](decisions.md#oq-039). Anonymous public Trades do not disclose counterparties; own Orders retain detailed outcomes. Aggregation adds active quantities without merging individual priority. Count each Trade once per symbol, although both participants have a fill.

### Decision table — book / Trade / candle conditions

| Initial business facts | Event / condition | Observation outcome | State / financial effect | Basis | Status |
|------------------------|-------------------|---------------------|--------------------------|-------|--------|
| No Trade yet | Funding or Orders exist | Last Price unavailable, quotes depend on actual book | No execution/volume/price from funding | [OQ-003](decisions.md#oq-003)/[OQ-037](decisions.md#oq-037)/[OQ-044](decisions.md#oq-044) | PROVISIONAL complete view |
| Bid absent / Ask absent | Observe best quotes | Missing side unavailable; Spread unavailable if either absent | No fabricated zero quote | [OQ-037](decisions.md#oq-037)/[OQ-044](decisions.md#oq-044) | PROVISIONAL |
| Active level | Cancel/STP without Trade | Reduce depth; Last Price stays as it was; zero volume increment | Unused reserve release follows Balance; no Trade fee for prevention | [OQ-011](decisions.md#oq-011)/[OQ-023](decisions.md#oq-023)/[OQ-037](decisions.md#oq-037)/[OQ-038](decisions.md#oq-038)/[OQ-044](decisions.md#oq-044) | PROVISIONAL view; approved STP core |
| Interval has Trades | Summarize | Actual first/high/low/last; sum Base and gross USDT | Fee/ownership unaffected by reporting | [OQ-038](decisions.md#oq-038)/[OQ-039](decisions.md#oq-039); [MKT-INV-002](invariants.md#mkt-inv-002) | PROVISIONAL |
| Interval has no Trades | Interval completes on virtual clock | OHLC unavailable; both volumes zero | Last Price can retain old actual price separately | [OQ-039](decisions.md#oq-039)/[OQ-044](decisions.md#oq-044) | PROVISIONAL; idle progression [GAP-EXP-003](review/gap-register.md#gap-exp-003) |
| Trade at interval start/end | Assign interval | Start included; end excluded, belongs to next interval | Actual Trade preserved once | [OQ-035](decisions.md#oq-035)/[OQ-039](decisions.md#oq-039) | PROVISIONAL |
| Same-time Trades | Summarize Open/Close | Use authoritative execution order | Display time equality does not merge/reorder Trades | [OQ-039](decisions.md#oq-039) | PROVISIONAL; initial action order [GAP-MAT-001](review/gap-register.md#gap-mat-001) |
| Effective end inside minute | Retain final summary | Label incomplete final interval; no carry-forward OHLC | End doesn't reverse Trades | [OQ-035](decisions.md#oq-035)/[OQ-039](decisions.md#oq-039) | PROVISIONAL; triggering end [GAP-EXP-001](review/gap-register.md#gap-exp-001)/[GAP-EXP-004](review/gap-register.md#gap-exp-004) |

### No action and boundaries

No Trade implies no volume increase or Last Price update even if quotes change. A genuinely empty interval is zero volume, not absence of known Last Price. If clock is stopped, interval time does not progress (§24); whether idle running time advances is [GAP-EXP-003](review/gap-register.md#gap-exp-003). A Trade exactly at intended experiment end cannot be assigned a final execution outcome until [GAP-EXP-004](review/gap-register.md#gap-exp-004) resolves eligibility; half-open candle grouping alone does not permit that Trade.

[BS-MKT-001](scenarios/market-data.md#bs-mkt-001)–[BS-MKT-008](scenarios/market-data.md#bs-mkt-008) cover empty/one-sided books, STP-only changes, worked OHLCV, empty/boundary/partial candles and observation coherence. [DIA-DATA](diagrams/README.md#dia-data), [DIA-CANDLE](diagrams/README.md#dia-candle) and [DIA-OBS](diagrams/README.md#dia-obs) apply source behavior with explicit OPEN nodes. Cross-read Matching/Trade, Experiment, Portfolio and Algo/HFT. Observation frequency/transport/UI rendering are not silently selected.
