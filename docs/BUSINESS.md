# HFT Simulation Platform — Business Domain Specification

## 1. Purpose of This Document

**DRAFT — REQUIRES FINAL OWNER REVIEW**

This business/domain source of truth helps the six-person CS team understand and review one coherent V1 trading model. It explains trading without requiring prior financial expertise. Architecture, technology choices, and implementation are outside scope.

[Vietnamese edition](BUSINESS.vi.md) expresses the same decisions, identifiers, examples, and approval boundaries. A translation is not a separate approval. Both editions must change together when decisions change.

| Label | Meaning |
|-------|---------|
| ESTABLISHED DOMAIN CONCEPT | Educational knowledge about trading; not every venue uses identical policies. |
| PROJECT V1 RULE — APPROVED | Explicit owner decisions OQ-001 through OQ-014. These are binding for V1. |
| PROJECT V1 RULE — PROPOSED — OWNER REVIEW REQUIRED | Concrete Codex recommendations OQ-015 through OQ-046. The narrative uses them to describe the recommended V1, but they are not owner-approved. |

Section 32 is the decision register. Historical OQ identifiers are retained for traceability even though each now has a selected decision. Section 31 distinguishes approved business rules from recommendations. There are no unresolved multiple-choice decisions in this revision.

Numbers use commas for thousands and periods for decimals: `90,000 USDT`, `0.4 BTC`. Prices are Quote units per Base unit. Examples assume adequate funding and different accounts unless stated otherwise. Examples marked **zero-fee scenario** use configured zero rates; V1 still supports fees. Amounts are simulated, not real-market quotations.

## 2. Product Domain Overview

**ESTABLISHED DOMAIN CONCEPT.** A trading platform connects buyers and sellers under defined rules:

**Trader → Order → Order Book → Matching → Trade → Settlement → Market Data / Portfolio**

This is a business flow, not a software architecture. An Order expresses intention; a Trade records actual execution. Rejection, cancellation, and lack of eligible counterparties can result in no Trade. A Market Order consumes resting liquidity and never becomes a resting order in V1.

Example: Bob's resting SELL offers `1 BTC @ 86,000`. Alice's incoming BUY LIMIT accepts up to `90,000`. They trade `1 BTC` for `86,000 USDT` before fees. Settlement transfers the assets; market history reflects the execution.

**APPROVED — OQ-001–OQ-014:** V1 is a funded simulated spot exchange with three markets, protected Market Orders, GTC Limit Orders, deterministic matching, self-trade prevention, and configurable per-Trade Maker/Taker fees.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-040, OQ-041:** V1 includes strategy participation and controlled queue-position, execution-quality, and simulated timing experiments. Historical-data backtesting is deferred.

## 3. Product Boundary

**PROJECT V1 RULE — APPROVED — OQ-001, OQ-002, OQ-004, OQ-005, OQ-006, OQ-012.** V1 supports only BTC/USDT, ETH/USDT, SOL/USDT; simulated BTC, ETH, SOL, USDT; funded spot trading; Limit and Market Orders; GTC for Limit; immediate-only Market execution.

Outside V1: real money, real cryptocurrency custody/ownership/redemption, bank transfers, blockchain deposits/withdrawals/settlement, real brokerage connectivity, external market-data integration, and real regulatory-compliance implementation. No production HFT claim is made.

Borrowing, leverage, margin, short selling, derivatives, perpetuals, futures, options, additional Quote Assets, cross pairs, Quote-budget Market Buy, explicit IOC/FOK, and advanced orders are deferred beyond V1. Advanced orders include Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged, and other conditional orders.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-028, OQ-029, OQ-030, OQ-040, OQ-045:** amendments, combined cancel-and-replace, expiry, historical backtesting, and Trade corrections are also deferred. Separate cancellation followed by a new order remains available. Future extensibility does not authorize those features now.

## 4. Business Actors

These are business responsibilities, not technical access-control roles.

| Actor | Objective and activities | Decision basis |
|-------|--------------------------|----------------|
| Human Trader | Observe the market, place funded orders, cancel own active remainders, inspect outcomes. | Approved trading rules; recommended observations in OQ-033, OQ-037. |
| Algorithmic Strategy | Make automated trading decisions using market information and assigned capital. | Proposed OQ-040, OQ-043. |
| Exchange / Trading Platform | Apply matching rules, establish Trades, account for settlement, and describe market activity. | Approved matching rules; proposed settlement/data details. |
| Administrator | Configure experiments and allocate simulated funds; never manufacture Trades to satisfy orders. | Approved OQ-003; proposed experiment controls OQ-042. |
| Liquidity / Scenario Account | Supply funded trading interest through ordinary orders. | Approved OQ-003; proposed participation model OQ-036, OQ-043. |

**APPROVED — OQ-003, OQ-005, OQ-010, OQ-011:** funded accounts receive no priority, funding, or self-trade exception because an administrator created them.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-043:** each manual participant, strategy, and liquidity scenario uses a separate trading account and balance pool within an experiment. No shared strategy capital or transfers between accounts in V1. The account-level self-trade rule does not group distinct accounts by human owner.

## 5. Assets and Trading Symbols

**ESTABLISHED DOMAIN CONCEPT.** In a Trading Pair, the **Base Asset** is bought/sold and the **Quote Asset** pays for it. **Notional** is Base quantity multiplied by price, before fees.

| Approved symbol | Base | Quote | Example before fees |
|-----------------|------|-------|---------------------|
| BTC/USDT | BTC | USDT | BUY `0.25 BTC @ 80,000` spends `20,000 USDT`. |
| ETH/USDT | ETH | USDT | BUY `2 ETH @ 3,000` spends `6,000 USDT`. |
| SOL/USDT | SOL | USDT | SELL `10 SOL @ 150` receives `1,500 USDT`. |

**APPROVED — OQ-001, OQ-002:** these are the exact V1 markets and four simulated assets. Familiar symbols imply no real cryptocurrency rights. USDT is the common Quote; cross pairs and external venues/data are beyond V1.

**APPROVED — OQ-003:** normal accounts receive configurable initial USDT and may initially have zero BTC/ETH/SOL. Administrators can allocate approved simulated assets to dedicated liquidity/scenario accounts. This is **Simulation Funding**, not a Trade, volume, profit, or execution. Liquidity appears only when funded accounts submit ordinary orders.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-042:** funding occurs only during experiment setup; normal accounts start with the configured USDT and zero Base assets. Dedicated scenario accounts may receive Base assets. Configuration states the amounts explicitly; no universal starting balance is assumed. Section 25 explains the non-profit cost basis of seeded assets.

## 6. Orders

**ESTABLISHED DOMAIN CONCEPT.** An Order can produce zero, one, or several Trades. BUY spends Quote and acquires Base; SELL gives Base and receives Quote.

| Quantity | Meaning |
|----------|---------|
| Original Quantity | Base amount requested, unchanged throughout this V1 order. |
| Filled Quantity | Sum of this order's actual Trade quantities. |
| Unexecuted Quantity | Original minus Filled, including any portion that has ended. |
| Active Remaining Quantity | Unexecuted quantity still eligible to trade; zero after termination. |

**APPROVED — OQ-004, OQ-006, OQ-008:** all V1 orders specify Base quantity. Limit Orders include a limit; Market Buy includes a maximum price and Market Sell a minimum price. “Spend up to `100,000 USDT`” is not a V1 Market Buy instruction. Price limits/protection constrain unit execution price; fees are funded separately.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-017, OQ-018, OQ-019, OQ-020, OQ-046:** use these explicit teaching-market bounds, not real-exchange specifications:

| Symbol | Price tick (USDT) | Inclusive price range (USDT) | Quantity step = minimum order | Maximum order quantity |
|--------|-------------------|------------------------------|-------------------------------|------------------------|
| BTC/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.000001 BTC` | `100 BTC` |
| ETH/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.00001 ETH` | `1,000 ETH` |
| SOL/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.001 SOL` | `100,000 SOL` |

Prices include Limit and Market protection prices. Quantities must be positive step multiples within the bounds. There is no additional minimum or maximum notional. Invalid values are rejected before trading, never silently rounded or resized. Fills and active remainders remain step multiples; no separate minimum applies to them. A positive remainder cannot fall below one step.

## 7. Limit Order Business Behavior

**ESTABLISHED DOMAIN CONCEPT.** BUY LIMIT sets the maximum price; SELL LIMIT sets the minimum. A limit is a boundary, not an exact execution-price request. See [Investor.gov — Types of Orders](https://www.investor.gov/introduction-investing/investing-basics/how-stock-markets-work/types-orders).

| Incoming order | Opposing resting order | V1 outcome before fees |
|----------------|------------------------|------------------------|
| BUY LIMIT `1 BTC @ 90,000` | SELL `1 BTC @ 86,000` | Trade at `86,000`; buyer improves by `4,000 USDT`. |
| BUY LIMIT `1 BTC @ 90,000` | SELL `1 BTC @ 91,000` | No match at this price. |
| SELL LIMIT `1 BTC @ 85,000` | BUY `1 BTC @ 87,000` | Trade at `87,000`; seller improves by `2,000 USDT`. |
| SELL LIMIT `1 BTC @ 85,000` | BUY `1 BTC @ 84,000` | No match at this price. |

**APPROVED — OQ-009, OQ-012:** an incoming Limit Order first consumes eligible resting interest at resting prices; its unfilled remainder rests as GTC. A Limit Order can therefore be immediately aggressive. It need not fill at all, and its resting remainder has no guaranteed future fill.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-022, OQ-023:** reserve enough Quote for a BUY's limit price and the larger possible fee rate; release excess after each Trade. Amendment is deferred (**OQ-028, OQ-029**).

## 8. Market Order Business Behavior

**APPROVED — OQ-006, OQ-007, OQ-008, OQ-012:** Market Buy and Sell request a Base quantity, execute immediately within a mandatory absolute price boundary, and end any unfilled portion. A buy consumes Asks from lowest price; a sell consumes Bids from highest price. The boundary is a maximum for BUY and a minimum for SELL, never a percentage-based slippage instruction.

Example: Market Buy `2 BTC`, maximum `90,000`. Resting Asks are `1 BTC @ 86,000` and `2 BTC @ 87,000`. It buys `1 BTC` at each price, spending `173,000 USDT` before fees. Average Execution Price is `173,000 / 2 = 86,500`.

If that maximum were `86,000`, it would buy only `1 BTC`; the other `1 BTC` ends immediately. If there is zero eligible liquidity, it creates zero Trades and ends without execution. Completed partial fills stand. No Market remainder rests, and the trader does not supply IOC.

Market Sell `2 BTC`, minimum `80,000`, can sell to bids of `82,000` and `80,000`, but not `79,000`. Protection limits execution prices; it does not guarantee quantity or execution at the boundary.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-021, OQ-022, OQ-027:** validate full protected funding before accepting the order, even if the book looks thin. Invalid/unfunded requests are REJECTED. A valid Market Order ends FILLED if complete or CANCELLED with reason MARKET_REMAINDER otherwise, including zero fills. Section 13 defines this terminology.

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

**PROPOSED — OWNER REVIEW REQUIRED — OQ-037, OQ-044:** expose full aggregated price-level depth, best prices, and each participant's own orders and queue observations. Do not expose other accounts' identities or individual order histories. An absent side has an unavailable best price; if either side is absent, Spread is unavailable, not zero. Cancellation can change quotes without changing Last Price.

## 10. Matching Rules

**ESTABLISHED DOMAIN CONCEPT.** Matching selects compatible same-symbol, opposite-side interest. Price-Time is one common method; other markets use other allocations, as illustrated by [CME's matching explanation](https://www.cmegroup.com/education/articles-and-reports/overview-what-makes-ags-markets-work).

**PROJECT V1 RULE — APPROVED — OQ-009, OQ-010:**

1. Better price first: lowest Ask for a buy, highest Bid for a sell.
2. At equal prices, earlier **platform acceptance sequence** first.
3. Larger quantity never overtakes an earlier same-price order.
4. Each Trade executes at the matched resting order's price.

The platform establishes one authoritative deterministic acceptance sequence for all accepted orders. A, B, C might receive `#1001`, `#1002`, `#1003` despite identical displayed times. Client-provided timestamps never determine priority. A resting remainder keeps its order's original acceptance sequence. How this sequence is generated is outside this document.

Example A: earlier Ask A offers `1 BTC @ 86,000`; later Ask B offers `1 BTC @ 85,000`. Incoming BUY LIMIT `1 BTC @ 86,000` trades with B at `85,000` first.

Example B: Ask A `0.4 BTC @ 86,000`, sequence `#1001`; Ask B `2 BTC @ 86,000`, sequence `#1002`. Incoming BUY LIMIT `1 BTC @ 86,000` fills A's `0.4`, then B's `0.6`, leaving B with `1.4 BTC`. Bid priority is the reverse price direction: `85,500` precedes `85,000` for an incoming sell.

### Self-Trade Prevention

**APPROVED — OQ-011:** check trading-account identity when the next eligible resting order would match. If it belongs to the incoming order's account, **cancel that resting order's active remainder**, record SELF_TRADE_PREVENTION distinctly from user cancellation, create no Trade, and continue the incoming order against other eligible counterparties. Do not merely skip the self-owned order and leave it active.

Example: account A's incoming BUY encounters A's resting SELL `0.4 BTC @ 86,000`, then B's SELL `0.6 BTC @ 86,000`. A's resting remainder is cancelled; only the `0.6 BTC` exchange with B becomes a Trade. The prevented match creates **no Trade, volume, Last Price update, or fee**. Previous fills of the cancelled order remain valid. Cross-account ownership grouping is deferred beyond V1.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-031:** for each eligible order pair, execute the smaller active remaining Base quantity as one Trade, then continue against the next eligible resting order if needed. This makes Trade boundaries, and therefore per-Trade fees, deterministic. The full immediate matching outcome of an incoming order, including prevention and fills, precedes the next effective order/cancel business action. This defines observable precedence, not a technical execution mechanism.

## 11. Partial Fills

**ESTABLISHED DOMAIN CONCEPT.** Filled Quantity accumulates across Trades. For a zero-fee incoming BUY LIMIT `10 BTC @ 90,000` consuming already resting Asks:

| Trade | Quantity | Resting price | Cumulative filled | Unexecuted |
|-------|----------|---------------|-------------------|------------|
| 1 | `4 BTC` | `86,000` | `4 BTC` | `6 BTC` |
| 2 | `3 BTC` | `87,000` | `7 BTC` | `3 BTC` |
| 3 | `3 BTC` | `88,000` | `10 BTC` | `0 BTC` |

Total value is `344,000 + 261,000 + 264,000 = 869,000 USDT`; the average is `86,900`. All three prices are existing resting prices, not prices later imposed on an already resting buy.

**APPROVED — OQ-007, OQ-012:** if only Trade 1 is immediately possible, a GTC Limit remainder of `6 BTC` rests at `90,000`. The equivalent protected Market Buy ends that remainder instead. A later sell against the resting buy executes at `90,000`, not at the new seller's lower limit.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-027:** after cancelling that Limit remainder, filled is `4`, unexecuted is `6`, active remaining is `0 BTC`. A cancelled order may contain valid Trade history.

## 12. Balance and Asset Reservation

**ESTABLISHED DOMAIN CONCEPT.** Available Balance can fund new commitments. Locked/Reserved Balance is still owned but committed. Reservation alone does not transfer ownership. With the proposed immediate settlement model, each asset's total is Available plus Locked.

**APPROVED — OQ-005, OQ-013:** trading is fully funded and fees must be included. **Assets reserved for one active order cannot simultaneously fund another active order**, including across all three markets sharing USDT.

### Recommended reservation policy

**PROPOSED — OWNER REVIEW REQUIRED — OQ-015, OQ-016, OQ-021, OQ-022, OQ-023, OQ-024, OQ-046.** Fees are in USDT. Buyers pay notional plus fee; seller fees come from sale proceeds. Let `q` be the active Base quantity, `b` the buy boundary, and `rM`/`rT` the experiment's maker/taker rates as fractions. Round a required USDT reservation **up to 8 decimal places**:

| Order | Required reservation |
|-------|----------------------|
| BUY LIMIT | `q × limit price × (1 + max(rM, rT))`, rounded up. |
| MARKET BUY | `q × maximum execution price × (1 + rT)`, rounded up. |
| SELL LIMIT or MARKET SELL | `q` Base units; no extra USDT reservation because the fee is deducted from proceeds. |

Rates are nonnegative, at most `1%`, and fixed during an experiment (section 26). A Limit Buy therefore remains funded if it first takes and later makes, even when maker fees exceed taker fees. Full reservation is required before any trading or self-trade-prevention effects; insufficient available funds cause rejection, never automatic downsizing. A self-cancellation expected later cannot pre-fund the incoming order.

After every fill, pay its actual cost, recompute the requirement for active remaining quantity, and release surplus immediately. When an order ends, release all its unused reservation. Section 26's downward per-Trade fee rounding prevents extra fees from fragmenting an order beyond its conservative reservation.

### BUY examples

**Zero-fee scenario:** `100,000 USDT` initially; BUY LIMIT `1 BTC @ 80,000` reserves `80,000`, leaving `20,000` available. An immediate `0.4 BTC` fill against a resting Ask at `75,000` spends `30,000`. Keep `48,000` for the remaining `0.6 BTC`; release `2,000`. Now available is `22,000`, locked is `48,000`, total is `70,000 USDT`; BTC received is `0.4`. Cancelling the remainder makes all `70,000 USDT` available. Released surplus is unused funding, not profit.

**Fee-enabled example:** maker `0.1%`, taker `0.2%` are illustrative scenario settings. The same buy reserves `80,160`, leaving `19,840`. Its immediate `30,000 USDT` Trade costs an additional `60` taker fee. The remaining reserve is `48,096`; surplus release is `2,004`. Available becomes `21,844`, locked `48,096`, total `69,940 USDT`. These rates are not mandated defaults.

A Market Buy `2 BTC`, maximum `90,000`, taker `0.2%`, requires `180,360 USDT` even when visible Asks would cost less. The maximum is a unit-price boundary; the extra `360` funds fees, not permission to buy above `90,000`.

### SELL example

With `3 BTC`, SELL LIMIT `2 BTC @ 86,000` reserves `2`, leaving `1 BTC` available. A subsequent sale of `0.5 BTC` at the resting sell's `86,000` price leaves `1` available and `1.5 BTC` locked. At a maker rate of `0.1%`, gross proceeds `43,000` minus fee `43` give `42,957 USDT`. Cancelling the remainder leaves `2.5 BTC` available; neither the sold `0.5 BTC` nor its fee is restored.

## 13. Order Lifecycle

**PROJECT V1 RULE — PROPOSED — OWNER REVIEW REQUIRED — OQ-025, OQ-026, OQ-027.** Use five observable states. NEW is not an observable V1 state; acceptance is a business event. CANCEL_PENDING is not an order state. A cancellation request alone does not establish a successful cancellation.

| State | Meaning | Terminal? |
|-------|---------|-----------|
| OPEN | Accepted GTC Limit Order resting with no fills. | No |
| PARTIALLY_FILLED | GTC Limit Order with fills and an active resting remainder. | No |
| FILLED | Filled Quantity equals Original Quantity. | Yes |
| CANCELLED | Unfilled portion ended; Filled Quantity may be zero or positive. | Yes |
| REJECTED | Request failed validation/funding before acceptance, Trades, or STP effects. | Yes |

Initial outcomes: accepted Limit → OPEN, PARTIALLY_FILLED, or FILLED; accepted Market → FILLED or CANCELLED. Invalid requests → REJECTED. A Market's partial fills remain in history, but PARTIALLY_FILLED is not its final state.

For resting Limits: OPEN → PARTIALLY_FILLED/FILLED/CANCELLED; PARTIALLY_FILLED → PARTIALLY_FILLED/FILLED/CANCELLED. Terminal orders never reactivate. Active Remaining equals Unexecuted only for active orders; it is zero for every terminal order. Filled Quantity remains cumulative.

Cancellation reasons are USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, and EXPERIMENT_ENDED. They explain the event without adding lifecycle states. MARKET_REMAINDER covers insufficient liquidity or the protection boundary, including zero fills. EXPIRED and amendment transitions are deferred (**OQ-028, OQ-030**). The end-of-experiment rule is proposed under **OQ-035, OQ-042**.

## 14. Order Cancellation

**ESTABLISHED DOMAIN CONCEPT.** **Cancel Order != Reverse Trade.** Only an active unfilled remainder can be cancelled. Already exchanged assets and fees are not returned by cancelling that remainder.

Example: a zero-fee buy of `1 BTC` fills `0.4 BTC` for `30,000 USDT`; cancelling the remaining `0.6 BTC` preserves that purchase and releases only unused reservation. A FILLED order has nothing left to stop.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-031:** order acceptance, cancellation decisions, and experiment end share authoritative business ordering. Client timestamps do not override it. An incoming order's entire immediate matching outcome completes before the next order/cancel action becomes effective. This is a business precedence rule, not a threading or sequencing implementation.

If the effective fill comes first, it stands and a later cancellation stops only the then-active remainder. If cancellation comes first, that remainder cannot trade later. After full fill, return “too late/already filled”; after previous cancellation, return “already ended”; neither changes history or releases funds twice. A nonexistent or rejected request has no active order to cancel. Participant cancellation applies only to its own account's orders.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-028, OQ-029:** changing intent means successfully cancelling the old remainder, then independently submitting a new order. There is no combined amend/cancel-and-replace operation. The new order must pass all checks and receives new acceptance priority; rejection does not restore the old order.

## 15. Time In Force

**ESTABLISHED DOMAIN CONCEPT.** Time In Force describes persistence/completeness separately from Order Type. [Investor.gov's order guidance](https://www.investor.gov/introduction-investing/general-resources/news-alerts/alerts-bulletins/investor-bulletins-14) explains these common concepts:

| Concept | General meaning | V1 status |
|---------|-----------------|-----------|
| GTC — Good Till Cancelled | Remain eligible until filled or cancelled, subject to venue rules. | APPROVED for every Limit Order. |
| IOC — Immediate Or Cancel | Execute eligible immediate quantity and cancel the rest. | Domain concept — not supported as an explicit V1 instruction. |
| FOK — Fill Or Kill | Execute the full quantity immediately or execute none. | Domain concept — not supported in V1. |

For `5 BTC` requested with `3 BTC` immediately eligible: V1 GTC Limit may fill `3` and rest `2`; V1 Market may fill `3` and end `2`. Industry FOK would execute zero, not execute `3` then reverse it.

**APPROVED — OQ-007, OQ-012:** V1 Market is intrinsically immediate-only and never requires the trader to specify IOC. GTC persists until fill, explicit cancellation, or another defined V1 ending rule.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-030, OQ-035:** no automatic expiry or maximum GTC lifetime within an experiment. Ending an experiment cancels its resting remainders with EXPERIMENT_ENDED; this is not EXPIRED.

## 16. Trade

**ESTABLISHED DOMAIN CONCEPT.** A Trade is an executed exchange, not an order submission, reservation, funding grant, or prevented self-match. One BUY for `2 BTC` can produce two Trades against sells of `0.5` and `1.5 BTC`.

Its business meaning includes buyer, seller, symbol, Base quantity, execution price, occurrence time/order, participating orders, maker/taker roles, and each side's fee. These are conceptual facts, not a storage or interface definition. Public market observations omit counterparty identities under **proposed OQ-037**.

**APPROVED — OQ-009, OQ-014:** every Trade uses its resting order's price and independently identifies the resting side as Maker and incoming side as Taker. Both sides agree on quantity and price, although their fees may differ.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-045:** confirmed Trades cannot be edited, deleted, reversed, or exceptionally corrected in V1. If an experiment is invalid, preserve and label its history invalid for research, end it, and start a separate experiment. Administrative funding cannot disguise a Trade correction.

## 17. Execution Price

**PROJECT V1 RULE — APPROVED — OQ-009.** **Execution Price = price of the matched resting order.** The incoming Limit Price or Market protection price only determines eligibility.

| Resting interest | Incoming interest | Execution price |
|------------------|-------------------|-----------------|
| 10:00 BUY LIMIT `1 BTC @ 90,000` | 10:01 SELL LIMIT `1 BTC @ 86,000` | `90,000` — the resting buy's price. |
| 10:00 SELL LIMIT `1 BTC @ 86,000` | 10:01 BUY LIMIT `1 BTC @ 90,000` | `86,000` — the resting sell's price. |

The times illustrate arrival order; authoritative acceptance sequence determines actual priority. Compatible limits overlap between `86,000` and `90,000`, but V1 does not choose a midpoint or the incoming limit. The incoming participant may obtain price improvement. Multiple resting price levels produce separate executions at their respective prices.

A resting BUY at `90,000` can execute against a Market Sell with minimum `80,000`, at `90,000`. A resting SELL at `91,000` cannot execute against a Market Buy with maximum `90,000`. Fees are separate from the unit-price boundary.

## 18. Trade Settlement

**ESTABLISHED DOMAIN CONCEPT.** Settlement fulfills each Trade's ownership exchange. **PROPOSED — OWNER REVIEW REQUIRED — OQ-015, OQ-023, OQ-024, OQ-046:** settle immediately per Trade. Received Base and net USDT proceeds become available immediately; there is no pending-settlement balance category.

The buyer gives notional plus buyer fee in USDT and receives the full Base quantity. The seller gives that Base quantity and receives notional minus seller fee. Both fees go to a dedicated, non-trading simulated **Fee Treasury** owned by the platform within the experiment; it starts at zero and is not strategy profit.

Complete example: Alice has `200,000 USDT`, Bob `3 BTC`, treasury `0`. Bob first rests two sells, `0.5 BTC` and `1.5 BTC`, both at `86,000`. Alice submits BUY LIMIT `2 BTC @ 90,000`. Example rates: Maker `0.1%`, Taker `0.2%`.

| Stage | Account | Available USDT | Locked USDT | Available BTC | Locked BTC |
|-------|---------|----------------|-------------|---------------|------------|
| Before orders | Alice | `200,000` | `0` | `0` | `0` |
| Before orders | Bob | `0` | `0` | `3` | `0` |
| After reservations | Alice | `19,640` | `180,360` | `0` | `0` |
| After reservations | Bob | `0` | `0` | `1` | `2` |
| After first Trade: `0.5 BTC` | Alice | `21,644` | `135,270` | `0.5` | `0` |
| After first Trade | Bob | `42,957` | `0` | `1` | `1.5` |
| After first Trade | Fee Treasury | `129` | `0` | `0` | `0` |
| After second Trade: `1.5 BTC` | Alice | `27,656` | `0` | `2` | `0` |
| After second Trade | Bob | `171,828` | `0` | `1` | `0` |
| After second Trade | Fee Treasury | `516` | `0` | `0` | `0` |

Trade 1 has notional `43,000`, buyer fee `86`, seller fee `43`. Trade 2 has notional `129,000`, buyer fee `258`, seller fee `129`. Final USDT conservation: `27,656 + 171,828 + 516 = 200,000`; BTC conservation: `2 + 1 = 3`. After only Trade 1, USDT also reconciles: `21,644 + 135,270 + 42,957 + 129 = 200,000`.

Partial execution settles only executed quantity. Reservation release is neither a transfer to the counterparty nor income. Each Trade settles once. Conserving asset units does not mean a portfolio's market valuation cannot change. Simulation Funding is separately identified when explaining experiment-wide totals.

## 19. Market Price Concepts

**ESTABLISHED DOMAIN CONCEPT.** Last Price is the latest Trade price; Best Bid/Ask describe current resting interest; Average Execution Price is total executed notional divided by executed Base quantity, excluding fees.

Example: an incoming buy executes `1 BTC @ 86,000`, then `2 BTC @ 87,000`. Its average is `86,666.66666667` when displayed to 8 decimals. Suppose afterward Best Bid is `85,500`, Best Ask `88,000`. Last Price is `87,000`: all four values differ.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-034, OQ-044, OQ-046:** Last Price is unavailable before the first Trade. No fill means Average Execution Price is unavailable, not zero. A quote is limited in quantity and is not guaranteed liquidation value. Derived price displays round half-up to 8 decimals without changing actual Trade prices or funding amounts.

## 20. Liquidity

**ESTABLISHED DOMAIN CONCEPT.** Liquidity is the ability to trade a desired size without a large price concession. Depth measures quantities across prices; a narrow Spread alone is insufficient.

For a funded Market Buy `2 BTC`, maximum `90,000`, in a zero-fee scenario:

| Ask book | Executed Quote value | Average price |
|----------|----------------------|---------------|
| Deep: `5 BTC @ 86,000` | `172,000 USDT` | `86,000` |
| Thin: `0.2 BTC @ 86,000`; `0.3 BTC @ 87,000`; `1.5 BTC @ 90,000` | `17,200 + 26,100 + 135,000 = 178,300 USDT` | `89,150` |

The same request costs `6,300 USDT` more in the thin book. With maximum `87,000`, the thin-book buy would acquire only `0.5 BTC` and terminate its `1.5 BTC` remainder. Funding does not guarantee counterparties. A snapshot can change before an action becomes effective.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-036:** funded scenario accounts provide initial liquidity by ordinary orders; their available inventory is finite and is not automatically replenished.

## 21. Slippage

**ESTABLISHED DOMAIN CONCEPT.** Slippage compares actual execution against a named reference. It can reflect depth, other participants' actions, and timing; it is distinct from trading fees.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-041:** use the opposing best quote actually observed at the participant's decision: Best Ask for BUY, Best Bid for SELL. Adverse slippage per Base unit is `average − reference` for BUY and `reference − average` for SELL. Positive means worse; negative means better. Percentage is that difference divided by reference, multiplied by 100. If reference or fills are absent, report unavailable.

BUY average `86,500` versus observed Ask `86,000` has adverse slippage `500 USDT/BTC`, approximately `0.5814%`; for `2 BTC`, the gross extra cost is `1,000 USDT`. SELL average `85,000` versus observed Bid `85,500` is also adverse by `500`.

**APPROVED — OQ-008:** this measurement does not replace the mandatory absolute Market protection price. A boundary can prevent a worse fill by ending the remainder; it cannot guarantee a full fill.

## 22. Market Impact

**ESTABLISHED DOMAIN CONCEPT.** Market Impact concerns market changes caused by trading. Assume Last Price `86,000`; Asks `0.5 BTC @ 86,000`, `0.5 @ 87,000`, `2 @ 88,000`. A Market Buy `2 BTC`, maximum `88,000`, executes across all three levels.

Average price is `(43,000 + 43,500 + 88,000) / 2 = 87,250`. The last execution sets Last Price to `88,000`; `1 BTC` remains at that Ask. With at least `2 BTC` originally offered at `86,000`, the buy would not have needed higher levels.

This illustrates immediate impact, not a permanent-price prediction. Slippage measures execution relative to a reference; impact concerns changes attributable to the order. A cancellation can move quotes without moving Last Price.

## 23. Market Data

**ESTABLISHED DOMAIN CONCEPT.** Market data describes standing interest and completed activity: order-book depth, best quotes, Trades, Last Price, volume, and OHLCV. Orders submitted, funding grants, and cancellations are not traded volume.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-037, OQ-038, OQ-044:** provide full aggregated depth, Best Bid/Ask/Spread, anonymous Trade history, Last Price, and both Base and USDT gross traded volume. Count each actual Trade once per symbol; report each one-minute interval and cumulative experiment totals. Fees are separate and do not inflate Quote volume. No rolling 24-hour statistic in V1.

Participants also see their own active orders, acceptance priority, same-price order count and quantity ahead, fills, fees, balances, and termination reasons. Queue observations include all active earlier same-price orders; they are observations, not guaranteed future fills. Other accounts' identities/order histories remain private.

**APPROVED — OQ-003, OQ-011:** Simulation Funding and prevented self-matches contribute no Trade/volume/Last Price. STP may change depth because it cancels resting interest. External market-data integration is beyond V1 (**OQ-002**).

## 24. Candlesticks / OHLCV

**ESTABLISHED DOMAIN CONCEPT.** Open/High/Low/Close are the first/highest/lowest/last Trade prices within an interval. Volume sums actual executions, counting each Trade once.

| Experiment time | Trade price (USDT/BTC) | Quantity (BTC) | Notional (USDT) |
|-----------------|------------------------|----------------|-----------------|
| 10:00:05 | `86,000` | `1` | `86,000` |
| 10:00:20 | `88,000` | `0.5` | `44,000` |
| 10:00:35 | `85,000` | `2` | `170,000` |
| 10:00:50 | `87,000` | `0.5` | `43,500` |

For that minute: Open `86,000`, High `88,000`, Low `85,000`, Close `87,000`, Base Volume `4 BTC`, Quote Volume `343,500 USDT`. The chart follows Trades; it does not determine prices. Close is not the average.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-035, OQ-039:** V1 has one-minute candles only, on the platform's virtual experiment clock, starting at `00:00:00 UTC` on the declared scenario date. Intervals include their start and exclude their end: a Trade at `10:01:00` belongs to the next minute. Same-time Trades follow authoritative business execution order.

For an empty interval, show unavailable OHLC and zero volume; do not carry forward a fake candle price. Last Price may separately retain the last actual Trade. At experiment end, retain and label any partial final interval. Time pauses when the experiment clock is stopped; local client time never groups Trades or decides priority.

## 25. Portfolio

**ESTABLISHED DOMAIN CONCEPT.** A Portfolio describes holdings; a Position is the quantity held; cost basis assigns acquisition cost; Realized PnL concerns sold quantity; Unrealized PnL values quantity still held. Locked assets remain part of holdings.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-033, OQ-034, OQ-046:** V1 reports available/locked/total balances, spot positions, weighted-average cost, realized and unrealized PnL per Base asset in USDT, fees, and total USDT equity when all nonzero holdings can be valued. Use **weighted-average cost only**; tax-lot/FIFO accounting is beyond V1.

On BUY, add notional plus buyer fee to the position's total cost basis. On SELL, remove the proportional basis `basis before sale × sold quantity / quantity before sale`, rounded down to 8 USDT decimals; a full sale removes all remaining basis. Realized PnL is net sale proceeds minus removed basis. This retains rounding residuals in the remaining position instead of losing them. Average cost is basis divided by quantity; it is unavailable for zero holdings.

In a zero-fee scenario, buy `1 BTC @ 80,000` and `1 @ 90,000`: total basis `170,000`, average `85,000`. Sell `0.5 @ 88,000`: realized PnL `1,500`; remaining basis `127,500`. At Last Price `87,000`, unrealized PnL is `130,500 − 127,500 = 3,000 USDT`.

If all three executions instead incur a taker fee of `0.2%`, basis becomes `170,340`, average `85,170`; selling `0.5` nets `43,912`, removes basis `42,585`, and realizes `1,327`. Remaining basis `127,755` yields unrealized PnL `2,745` at the same Last Price. Fee rates are scenario choices, not defaults.

Seeded Base assets receive an explicitly declared scenario acquisition-reference price, valid on the symbol's price grid; their initial basis is quantity × that price. Seeding changes funded capital, not trading PnL or Last Price. USDT allocation likewise is not profit. Portfolio performance separates funded capital, realized PnL, unrealized PnL, and fees; fees already included in cost/proceeds must not be subtracted twice.

Value holdings at the experiment's Last Price, without substituting a setup reference. Before a first Trade, valuation/unrealized PnL of a nonzero Base holding and total equity are unavailable; realized PnL and known balances remain available. A Last Price older than `60` experiment seconds is flagged stale but retained, with its age. These are indicative valuations, not guaranteed liquidation proceeds; no future selling fee is deducted from unrealized PnL.

## 26. Fees

**PROJECT V1 RULE — APPROVED — OQ-013, OQ-014.** V1 supports configurable Maker/Taker fees, including zero. Classification is **per Trade**: resting order = Maker; incoming order = Taker. Rates may be equal or different. Fees affect funding, reservations, settlement, balances, and reported performance.

Example: resting SELL `1 BTC @ 86,000`; incoming BUY LIMIT `2 BTC @ 90,000`. Trade 1 is `1 BTC @ 86,000`: BUY Taker, SELL Maker. The BUY's remaining `1 BTC` rests at `90,000`. A later incoming SELL executes against it at `90,000`: that same BUY is now Maker. A Limit Order is not inherently a maker for all its fills.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-015, OQ-016:** both fees are in USDT and credited to the Fee Treasury. For each side of each Trade, fee = notional × its applicable rate, **rounded down to 8 USDT decimal places**. No minimum fee, rebates, discounts, separate fee token, or per-order fee aggregation. A tiny fee may round to zero; there is no hidden rounding charge. Each side loses exactly what the treasury receives.

Require explicit maker and taker rates at experiment setup, each from `0%` through `1%`, in increments of `0.01` percentage point (`0.0001` as a fraction). Rates remain fixed through the experiment, protecting existing reservations. Zero rates are allowed; there is no implicit fee default.

For notional `86,000`, maker `0.1%` gives fee `86`; taker `0.2%` gives `172 USDT`. If seller is Maker and buyer Taker, buyer pays `86,172`, seller receives `85,914`, treasury receives `258`. With Quote fees capped at `1%`, seller fees can always be paid from proceeds. Exact decimal conventions and reserve rounding are in sections 6, 12, and OQ-046.

## 27. Market Maker

**ESTABLISHED DOMAIN CONCEPT.** A Market Maker provides buying and selling interest. A bid `85,500` and ask `86,000` offer liquidity, but their `500` spread is not guaranteed profit: one side may fill, inventory may accumulate, and prices/fees affect results.

**APPROVED — OQ-003:** administrators seed assets into dedicated accounts; those accounts must trade through the book. Administrative direct Trades, forced fills, and direct Last Price changes are prohibited.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-036, OQ-043:** V1 seeds liquidity through predefined, funded scenario-account Limit Orders submitted in declared scenario action order once the experiment is running. Setup funding itself leaves the book empty. Those accounts may also host experimental automated strategies under the same ordinary rules. V1 supplies no guaranteed always-on Market Maker, automatic inventory refill, or special priority. Designing an adaptive market-making strategy is beyond this specification. A market can legitimately be empty or one-sided.

## 28. Algorithmic Trading

**ESTABLISHED DOMAIN CONCEPT.** Algorithmic Trading automates decisions to submit, cancel, or do nothing. Quantitative Trading uses quantitative evidence to guide decisions. Exchange Matching instead determines which submitted orders execute under the venue's rules. A strategy requesting `2 BTC` must evaluate actual fills, not assume it acquired `2 BTC`.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-040:** V1 supports strategy participants using simulated market information and their own balances to submit/cancel ordinary V1 orders. Controlled synthetic experiments may be rerun as new experiments with declared initial funding, fee rates, action schedules, and timing assumptions. Reproducibility requires the same scenario and participant decisions; a changed strategy is a changed experiment input.

Historical-data backtesting, external venue/data integration, optimization suites, and strategy marketplaces are deferred beyond V1. Backtesting remains a useful domain concept: evaluating a strategy on past data requires explicit assumptions about fills, liquidity, fees, and timing. Synthetic scenario reruns are not claimed to prove historical-market performance.

## 29. HFT

**ESTABLISHED DOMAIN CONCEPT.** HFT involves frequent automated decisions, latency sensitivity, queue position, and execution quality; it is not simply “a bot that trades fast.” The SEC's [historical HFT research review](https://www.sec.gov/marketstructure/research/hft_lit_review_march_2014.pdf) provides background on these distinctions.

**PROPOSED — OWNER REVIEW REQUIRED — OQ-041:** V1 supports controlled experiments with an explicit nonnegative observation delay and action delay for each participant, fixed at setup for that experiment. Compare different delays in separate experiments; random timing jitter is deferred. All delays use the virtual experiment clock. Observation delay affects what market information a participant has seen; action delay affects when its order/cancellation reaches the platform's authoritative business ordering. Neither participant timestamps nor configured speed bypass existing priority.

Observe per-order fill ratio (`filled / original`), quantity-weighted execution price, observed-quote slippage, actual fees, fee-inclusive average buying cost/net selling proceeds per filled unit, time to first fill, time to full fill, and effective cancellation outcome. Unachieved first/full fills have unavailable elapsed metrics, not zero. Also observe own same-price queue order count/quantity ahead and resulting portfolio PnL. Times use experiment-clock intervals; displayed-time ties follow authoritative business order.

Example: A and B each rest `1 BTC @ 86,000`, A earlier. An incoming buy of `0.5 BTC` fills A while B receives nothing. Altering B's delay in another experiment may change acceptance order, but cannot change priority retrospectively.

This is an HFT simulation/research platform, not a production HFT venue. Real-network/hardware benchmarking and production latency targets are beyond V1 business scope. Synthetic delay and trading outcomes are reported as simulation results, not real-world profitability or infrastructure performance.

## 30. Business Invariants

These distinguish general consistency from V1-specific policy. Proposed details retain their status even when expressed as requirements of the recommended model.

| ID | Invariant | Basis |
|----|-----------|-------|
| ORD-INV-001 | `0 ≤ filled ≤ original` for every order; original quantity never changes in V1. | Domain concept; no-amendment detail proposed OQ-028. |
| ORD-INV-002 | `original = filled + unexecuted`; active remaining is zero after termination. | Domain concept; terminology proposed OQ-027. |
| ORD-INV-003 | Every execution respects both applicable boundaries, including Market protection. | APPROVED OQ-008, OQ-009. |
| ORD-INV-004 | Effective cancellation prevents future execution of that remainder; prior fills stand. | Domain concept; precedence proposed OQ-031. |
| BAL-INV-001 | Reserved assets cannot fund a second order, including across symbols. | Domain concept; funded model APPROVED OQ-005. |
| BAL-INV-002 | Reservation/release alone changes availability, not total ownership. | Domain concept. |
| BAL-INV-003 | Releasing an unused reservation cannot refund assets already exchanged or fees already paid. | Domain concept. |
| TRD-INV-001 | A confirmed Trade remains immutable historical fact. | Domain concept; no corrections proposed OQ-045. |
| TRD-INV-002 | Both sides agree on symbol, Base quantity, and resting-order execution price. | Domain concept; price APPROVED OQ-009. |
| TRD-INV-003 | Each Trade contributes its quantity once to each participating order's filled total. | Domain concept. |
| STL-INV-001 | Settlement gives consistent ownership to all parties, including the fee recipient. | Domain concept; USDT treasury proposed OQ-015, OQ-024. |
| STL-INV-002 | Trading conserves each asset across participants plus Fee Treasury; Simulation Funding is accounted for separately. | Domain concept; proposed fee destination. |
| STL-INV-003 | Each Trade's asset/fee transfers apply once, never again because it is reported again. | Domain concept. |
| MKT-INV-001 | Last Price reflects only the latest actual Trade in authoritative execution order. | Domain concept; initialization proposed OQ-044. |
| MKT-INV-002 | Market volume counts each Trade once, not once per counterparty. | Domain concept; windows proposed OQ-038. |
| ORD-INV-005 | Equal-price priority follows platform acceptance sequence, never client time or larger quantity. | APPROVED OQ-010. |
| TRD-INV-004 | No Trade occurs between the same trading account's orders; STP cancels the resting remainder and creates no fee/volume/Last Price. | APPROVED OQ-011. |
| BAL-INV-004 | Accepted commitments cannot require unavailable assets; available and locked balances remain nonnegative. | APPROVED funded scope; reserve calculation proposed OQ-021, OQ-022. |
| SIM-INV-001 | Funding is not execution, volume, or trading profit and cannot manufacture a market price. | APPROVED OQ-003. |

## 31. Business Rules and Approval Status

Existing BR identifiers are preserved. A row containing a newly chosen policy remains **PROPOSED — OWNER REVIEW REQUIRED**, even when it also applies an approved principle. Approved rows cite only owner-approved decisions.

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| BR-ORD-001 | Better price, then earlier platform acceptance sequence; never client timestamps or quantity-based overtaking. | Deterministic priority. | APPROVED | OQ-010 |
| BR-ORD-002 | Limit uses GTC; Market is immediate-only and its remainder never rests. Explicit IOC/FOK are deferred. | Separate type from persistence. | APPROVED | OQ-007, OQ-012 |
| BR-ORD-003 | Use OPEN, PARTIALLY_FILLED, FILLED, CANCELLED, REJECTED and the transitions/reasons in section 13. | Represent outcomes without unnecessary states. | PROPOSED — OWNER REVIEW REQUIRED | OQ-025, OQ-026, OQ-027 |
| BR-BAL-001 | Trade only fully funded simulated spot assets; no credit, leverage, shorting, or derivatives. | Keep ownership obligations funded. | APPROVED | OQ-002, OQ-005 |
| BR-BAL-002 | Apply section 12's fee-inclusive buy reservations and Base-only sell reservations. | Fund protected quantity and maker/taker changes. | PROPOSED — OWNER REVIEW REQUIRED | OQ-015, OQ-016, OQ-022, OQ-046 |
| BR-BAL-003 | Recompute reservation and release surplus after every Trade; release unused funds at termination. | Keep available funds aligned with obligations. | PROPOSED — OWNER REVIEW REQUIRED | OQ-023, OQ-024 |
| BR-BAL-004 | Reject insufficient full funding before acceptance or STP; never silently resize. | Prevent unpayable commitments. | PROPOSED — OWNER REVIEW REQUIRED | OQ-021 |
| BR-TRD-001 | Preserve Trades without editing, deletion, reversal, or correction in V1. | Preserve explainable experiment history. | PROPOSED — OWNER REVIEW REQUIRED | OQ-045 |
| BR-STL-001 | Settle each Trade immediately once, including fees; unfilled quantities create no transfer. | Consistent usable ownership. | PROPOSED — OWNER REVIEW REQUIRED | OQ-024, OQ-046 |
| BR-MKT-001 | Last Price and Trade summaries use actual simulated Trades; no seeded/external price substitution. | Distinguish inputs from executions. | PROPOSED — OWNER REVIEW REQUIRED | OQ-038, OQ-039, OQ-044 |
| BR-MKT-002 | Provide full aggregated depth and own-account queue observations, without other-account identity/history. | Support understandable queue research. | PROPOSED — OWNER REVIEW REQUIRED | OQ-037 |
| BR-PF-001 | Report balances, positions, fee-inclusive weighted-average cost, and realized/unrealized PnL. | Useful performance analysis with one cost method. | PROPOSED — OWNER REVIEW REQUIRED | OQ-033, OQ-034 |
| BR-ACT-001 | Administratively funded accounts use the ordinary funding, matching, and prevention rules. | No privileged forced counterparties. | APPROVED | OQ-003, OQ-005, OQ-010, OQ-011 |
| BR-ORD-004 | Support Limit and protected Market only; both BUY and SELL use Base quantity. | Explicit trading intent and bounds. | APPROVED | OQ-004, OQ-006, OQ-008 |
| BR-ORD-005 | Execute at the matched resting order's price. | Explain price improvement consistently. | APPROVED | OQ-009 |
| BR-ORD-006 | Cancel the self-owned resting remainder, create no self-Trade, and continue the incoming order. | Account-level STP with distinct history. | APPROVED | OQ-011 |
| BR-FEE-001 | Configure Maker/Taker rates, including zero; classify resting/incoming per Trade. | An order can change roles between Trades. | APPROVED | OQ-013, OQ-014 |
| BR-FEE-002 | Charge each side in USDT to Fee Treasury; floor each fee to 8 decimals, no minimum/rebate; fix bounded rates per experiment. | Exact, fundable fee obligations. | PROPOSED — OWNER REVIEW REQUIRED | OQ-015, OQ-016 |
| BR-MKT-003 | Support exactly BTC/USDT, ETH/USDT, SOL/USDT using simulated BTC/ETH/SOL/USDT only. | Explicit market boundary. | APPROVED | OQ-001, OQ-002 |
| BR-SIM-001 | Initial USDT is configurable; seed approved assets into scenario accounts through Simulation Funding, never direct Trades. | Establish liquidity without fabricated execution. | APPROVED | OQ-003 |
| BR-PREC-001 | Enforce section 6's price/quantity bounds; all assets have 8-decimal precision; apply section 32's rounding rules. | Deterministic amounts without unexplained loss. | PROPOSED — OWNER REVIEW REQUIRED | OQ-017, OQ-018, OQ-019, OQ-020, OQ-046 |
| BR-ORD-007 | Use separate cancel then new order with new priority; no amendment, combined replace, or expiry. | Keep order commitments stable. | PROPOSED — OWNER REVIEW REQUIRED | OQ-028, OQ-029, OQ-030, OQ-031, OQ-032 |
| BR-SIM-002 | Run bounded experiments; setup-only funding/configuration; end by cancelling remainders and preserve history. | Reset by starting a separate experiment. | PROPOSED — OWNER REVIEW REQUIRED | OQ-035, OQ-042, OQ-045 |
| BR-ACT-002 | Give each participant a separate funded account; seed initial depth with ordinary scenario Limit Orders. | Finite, accountable liquidity and capital. | PROPOSED — OWNER REVIEW REQUIRED | OQ-036, OQ-043 |
| BR-MKT-004 | Publish one-minute and experiment-cumulative Base/USDT volume, one-minute candles, and empty OHLC as unavailable. | Consistent market summaries. | PROPOSED — OWNER REVIEW REQUIRED | OQ-038, OQ-039 |
| BR-ALGO-001 | Include synthetic strategy/queue/timing experiments and execution-quality measures; defer historical backtesting. | Research value within a manageable V1. | PROPOSED — OWNER REVIEW REQUIRED | OQ-040, OQ-041 |

## 32. Business Decision Register

OQ-001 through OQ-014 are owner-approved. OQ-015 through OQ-046 each contain one concrete recommendation requiring final owner review. A proposed row that restates an approved dependency, such as Stop Order deferral, does not reopen that approved decision. Configurable scenario values are deliberate parameters, not unanswered policy questions.

| ID | Decision | Rationale | Status |
|----|----------|-----------|--------|
| OQ-001 | Exactly BTC/USDT, ETH/USDT, SOL/USDT; USDT common Quote. Other quotes and cross pairs deferred. | Fixed V1 market scope. | APPROVED |
| OQ-002 | BTC, ETH, SOL, USDT are simulated units only, with no real custody, ownership, deposits, withdrawals, redemption, or blockchain settlement. External data/venue integration is outside V1. | Keep a fully simulated exchange. | APPROVED |
| OQ-003 | Configurable initial USDT for normal accounts; Base balances may initially be zero. Admin may seed approved assets into dedicated liquidity/scenario accounts. Simulation Funding is not Trade, volume, profit, or execution; funded accounts obey normal rules and no forced fills bypass the book. | Fund experiments without inventing trading activity. | APPROVED |
| OQ-004 | Support Limit and Market Orders only; defer Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged, and other advanced/conditional orders. TIF is separate. | Explicit order-type scope. | APPROVED |
| OQ-005 | Fully funded simulated spot only: buy with available Quote, sell available owned Base. No borrowing, leverage, margin, shorting, derivatives, perpetuals, futures, or options. | No unfunded obligations. | APPROVED |
| OQ-006 | Market Buy and Market Sell specify Base quantity. Market Buy requests up to that quantity; Quote-budget buying is deferred. Funding/protection/completion follow their own rules. | Unambiguous quantity intention. | APPROVED |
| OQ-007 | Market consumes immediately eligible liquidity; preserve partial fills and end every unfilled remainder without resting. Zero eligible liquidity produces zero Trades and no execution. GTC Limit remainders may remain active. | Never reverse valid fills for insufficient liquidity. | APPROVED |
| OQ-008 | Every Market Buy specifies an absolute maximum price; every Market Sell an absolute minimum. Execute only within it and end any remainder. No percentage substitute. | Explicit execution-price protection. | APPROVED |
| OQ-009 | Each Trade uses its matched resting order's price; incoming limits/protection are boundaries. Multiple resting levels can yield different Trade prices. | Deterministic execution and improvement. | APPROVED |
| OQ-010 | Price-Time Priority: better price, then earlier unique authoritative platform acceptance sequence; quantity never overtakes. Client timestamps do not determine priority, including same displayed times. Sequence-generation technology is unspecified here. | Deterministic, explainable ordering. | APPROVED |
| OQ-011 | Prevent same-account Trades by cancelling the eligible self-owned resting remainder, recording STP distinctly, and continuing the incoming order. No Trade/volume/Last Price/fee for the prevented match. Cross-account ownership grouping deferred. | Remove self-interest without fabricating execution. | APPROVED |
| OQ-012 | Limit supports GTC only; Market is intrinsically immediate-only with remainder ending. Explicit IOC/FOK deferred; Market never requires IOC. GTC ends only by fill/cancel/another defined V1 ending rule. | Separate Order Type and TIF. | APPROVED |
| OQ-013 | Support configurable simulated fees, including zero; apply enabled fees consistently to funding, reservation, settlement, balances, strategy results, and supported PnL. | Costs must reconcile throughout trading. | APPROVED |
| OQ-014 | Configurable Maker/Taker rates may equal or differ. Per Trade, resting side is Maker and incoming side Taker; the same order may change role on later executions. | Classification follows liquidity contribution. | APPROVED |
| OQ-015 | Both parties pay USDT fees into the experiment's non-trading platform Fee Treasury, initially zero. Buyer pays on top of notional; seller pays from proceeds. | One fee asset and explicit ownership. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-016 | Per-Trade, per-side notional × rate, floored to 8 USDT decimals; no minimum, rebate, discount, or aggregate-order fee. Require maker/taker rates each 0%–1% in 0.01 percentage-point steps, fixed per experiment. | Deterministic fees; seller proceeds and buyer reserves suffice. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-017 | All three symbols use 0.01 USDT price ticks and inclusive 0.01–1,000,000.00 prices, including Market boundaries. Reject off-grid/out-of-range prices. | Simple teaching-market grid. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-018 | Quantity steps: BTC 0.000001, ETH 0.00001, SOL 0.001 Base units. Reject off-step quantities, never round the request. | Exact divisible trading quantities. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-019 | Minimum order is one symbol quantity step; no minimum notional or separate minimum fill/remainder. | Avoid artificial cancellation of small valid remainders. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-020 | Maximum Base quantity per order: BTC 100, ETH 1,000, SOL 100,000. No additional notional cap. | Explicit bounded request sizes without price-reference ambiguity. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-021 | Validate full required funding before acceptance or STP; reject insufficient funds with no fills or automatic resizing. Do not rely on a later self-cancellation to supply funds. | Accepted orders remain fundable. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-022 | BUY LIMIT reserves quantity × limit × (1 + maximum maker/taker rate); MARKET BUY reserves quantity × maximum price × (1 + taker rate), each rounded up to 8 USDT decimals. SELL reserves Base quantity; fees are deducted from proceeds. | Cover price protection and possible role changes. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-023 | After each Trade, recompute reserve for active remainder and immediately release surplus. Release all unused reserve when any remainder ends. | Avoid unnecessary holds and duplicate release. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-024 | Immediate per-Trade simulated settlement; received Base and net USDT proceeds are immediately available. No unsettled balance stage. | Straightforward funded ownership. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-025 | NEW is not observable; acceptance is an event, followed by the initial state specified in section 13. | No ambiguous new-versus-accepted state. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-026 | No CANCEL_PENDING order state. Record the final cancellation outcome; an outstanding request does not imply effectiveness. | Distinguish intention from outcome without an extra state. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-027 | Five states: OPEN, PARTIALLY_FILLED, FILLED, CANCELLED, REJECTED. Last three terminal. Market incomplete/zero fill ends CANCELLED/MARKET_REMAINDER; distinguish user, STP, and experiment-end cancellation reasons. Filled history survives; active remaining is zero after termination. | Cover every V1 outcome with explicit meanings. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-028 | Order amendment is deferred beyond V1; original quantity/price/boundary do not change. | Stable commitments and priority. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-029 | Separate effective cancel then independent new order; no combined cancel-and-replace. New order gets fresh funding checks and acceptance priority; its rejection does not restore the old order. | Clear replacement risk and fairness. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-030 | No EXPIRED state, automatic expiry, or GTC lifetime cap within an experiment. Experiment end uses CANCELLED/EXPERIMENT_ENDED. | Consistent GTC behavior. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-031 | Platform business order governs order/cancel/end precedence; each eligible pair trades the smaller active remaining quantity in one Trade. Complete each incoming order's immediate outcome before the next effective action. Earlier fills stand; earlier cancellation stops its remainder. Too-late/already-ended outcomes make no further changes. | Deterministic Trades, fees, and races without technical design. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-032 | Keep all Stop/Stop-Limit and other advanced orders deferred; define no trigger behavior in V1. This preserves approved OQ-004. | No second conditional-order model. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-033 | Include balances, spot positions, fees, weighted-average cost, realized/unrealized PnL. BUY adds fee-inclusive cost; SELL removes proportional basis floored to 8 decimals, or all basis on full sale. Seeded Base uses declared scenario acquisition cost, never free trading profit. | Useful performance with one accounting method. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-034 | Value at experiment Last Price. Nonzero unpriced Base holdings make their valuation/unrealized PnL and total equity unavailable. Keep prices older than 60 experiment seconds but flag stale with age. Do not deduct hypothetical liquidation fees. | Honest indicative valuation without fabricated quotes. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-035 | Use bounded experiments with declared virtual clock starting 00:00:00 UTC on a scenario date. Trading is continuous while the experiment runs; end cancels all active remainders and preserves balances/history. No auctions or daily sessions. | Repeatable scope and explicit boundaries. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-036 | Seed initial liquidity with predefined funded scenario-account Limit Orders in declared action order while the experiment is running; setup funding alone leaves the book empty. Strategy participation may continue under normal rules. No guaranteed automated maker or automatic inventory replenishment. | Finite ordinary liquidity supports controlled experiments. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-037 | Full aggregated price-level depth, anonymous Trades, best quotes, and own-order queue count/quantity ahead, priority, fills and reasons. Do not disclose other-account identities or individual histories. Empty-side values are unavailable. | Enable queue study without unnecessary disclosure. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-038 | Count every actual Trade once per symbol, in Base and gross USDT notional, per one-minute interval and cumulatively per experiment. Exclude fees, funding, cancellations, and STP; no rolling 24-hour volume. | Unambiguous units, inclusion, and windows. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-039 | One-minute candles on experiment UTC time; include interval start, exclude end; ties follow execution order. Empty intervals have unavailable OHLC and zero volumes, with no carry-forward candle. Label the final incomplete interval. | Reproducible trade-derived charts. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-040 | Include algorithmic participation and repeatable synthetic scenario experiments. Historical-data backtesting and institutional research-suite features are deferred. | Meaningful quant interaction with manageable V1 scope. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-041 | Set fixed nonnegative observation/action delays per participant per experiment; compare different settings across experiments, without random jitter. Observe own queue position, fill ratio, average price, decision-observed-quote slippage, fees, fee-inclusive unit cost/proceeds, fill timing, cancellation outcomes, and PnL on experiment time. Adverse slippage is positive; missing references/fills give unavailable metrics. Production latency claims/benchmarks deferred. | Explain execution quality and timing sensitivity. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-042 | Admin configures explicit starting funds, seeded-asset costs, fee rates, and scenarios before start; normal accounts start USDT-only. No in-run funding, arbitrary Trade/price edits, or forced fills. End/reset archives the old experiment and starts a separate one; no simulated asset transfers between experiments. | Setup authority without rewriting history or performance. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-043 | Separate account and balance pool per manual participant, strategy, or liquidity scenario; no shared capital or account transfers. All obey the same trading rules; distinct accounts are not grouped for STP. | Clear funding attribution and approved account-level prevention. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-044 | Last Price unavailable until the first actual Trade; absent best quotes/Spread stay unavailable. Scenario cost references are separately labeled, never executable quotes, Last Price, or a valuation fallback. No external price integration in V1. | No invented market history. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-045 | No Trade correction, reversal, deletion, or editing in V1. Preserve invalid experiment history with an explicit research-validity label, then start a separate experiment. Funding cannot serve as a disguised correction. | Historical facts remain explainable. | PROPOSED — OWNER REVIEW REQUIRED |
| OQ-046 | All asset balances use 8 exact decimal places. Base setup allocations follow symbol quantity steps; USDT allocations follow 0.00000001. Tick × step makes notional exact, with no settlement-notional rounding. Floor fees and proportional cost removal to 8 decimals; ceil buy reserves to 8; retain residual funds/basis. Derived prices/costs display half-up to 8 decimals, percentages to 4; display rounding never changes obligations. | Conserve units without prescribing programming types. | PROPOSED — OWNER REVIEW REQUIRED |

## 33. Glossary

Definitions are educational; project-policy status follows the decision register. Deferred concepts remain useful vocabulary.

| English Term | Explanation | Example / V1 distinction |
|--------------|-------------|--------------------------|
| Trader / Trading Account | A participant / its separate funded trading identity. | STP compares accounts, not human ownership groups. |
| Asset | A unit that can be held and exchanged. | Simulated BTC, ETH, SOL, USDT. |
| Base / Quote Asset | Asset being bought/sold / asset expressing and paying its price. | BTC / USDT in BTC/USDT. |
| Symbol / Trading Pair | Named Base/Quote market. | ETH/USDT. |
| Spot Trading | Exchange of the assets themselves. | Receive BTC for USDT. |
| Order / Trade | Trading intention / actual matched exchange. | A cancelled remainder is not a reversed Trade. |
| BUY / SELL | Acquire / give Base units. | SELL SOL receives USDT. |
| Limit Order / Limit Price | Priced intention / maximum buy or minimum sell boundary. | BUY LIMIT 90,000 can execute at 86,000. |
| Market Order | Immediate execution against eligible interest. | V1 requires an absolute protection price and never rests. |
| Quote-budget Buy | Buying by a spending cap rather than Base quantity. | Domain concept — deferred beyond V1. |
| Original / Filled Quantity | Requested Base amount / actual executed amount. | 10 requested, 4 filled. |
| Unexecuted / Active Remaining | Not executed / still eligible to execute. | After cancelling the remaining 6, unexecuted is 6 but active remaining is 0. |
| Notional | Quantity × execution price before fees. | 0.5 × 86,000 = 43,000 USDT. |
| Fill / Partial Fill | Execution / execution of less than the original quantity. | 0.4 BTC of a 1 BTC order. |
| Order Book / Resting Order | Current standing interest / an active order in it. | Only Limit remainders rest in V1. |
| Incoming / Aggressive Order | Newly presented interest / interest consuming resting liquidity. | A marketable Limit can take liquidity. |
| Bid / Ask | Buying / selling offer. | Bid 85,500; Ask 86,000. |
| Best Bid / Best Ask | Highest bid / lowest ask. | Both may change without a Trade. |
| Spread | Best Ask minus Best Bid. | 500 when quotes are 85,500 / 86,000. |
| Price Level / Aggregated Depth | One price / summed active quantities at prices. | 0.4 + 0.6 = 1 BTC at 86,000. |
| Matching | Allocation of eligible opposite interest. | Price first, then platform acceptance order. |
| Price-Time Priority / FIFO | Better price first, then earlier same-price priority. | Later larger orders cannot overtake. |
| Acceptance Sequence | Authoritative order of platform acceptance. | #1001 precedes #1002 despite equal displayed time. |
| Queue Position | Position among competing same-price orders. | Count and quantity ahead of one's order. |
| Execution Price | Actual unit price of a Trade. | Always the matched resting price in V1. |
| Price Improvement | Execution better than the incoming boundary/comparison. | Buy at 86,000 rather than its 90,000 limit. |
| Average Execution Price | Total executed notional / executed Base quantity. | 173,000 / 2 = 86,500, excluding fees. |
| Last Price | Latest actual Trade price. | Unavailable before the first Trade. |
| Reference Price / Midpoint | Named comparison price / halfway between two named prices. | Midpoint is not V1's execution-price rule. |
| Available / Locked Balance | Reusable assets / assets committed to active orders. | Both still belong to the account. |
| Reservation / Release | Commit assets / return unused commitment to availability. | Release is not profit. |
| Settlement | Fulfillment of the actual exchange, including fees. | Recommended immediate settlement per Trade. |
| Cancellation | End an active unfilled remainder. | Existing fills and fees stand. |
| OPEN / PARTIALLY_FILLED | Resting without fills / resting with some fills. | Recommended nonterminal Limit states. |
| FILLED / CANCELLED / REJECTED | Completed / remainder ended / failed before acceptance. | Recommended terminal states. |
| NEW / CANCEL_PENDING / EXPIRED | Other possible industry lifecycle labels. | Not observable V1 states in the recommended model. |
| Termination Reason | Explanation separate from state. | USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, EXPERIMENT_ENDED. |
| Amendment / Cancel-and-Replace | Change an existing commitment / a combined replacement operation. | Deferred; separate cancel then new is recommended V1 behavior. |
| Time In Force (TIF) | Persistence/completeness instruction separate from type. | V1 Limit uses GTC. |
| GTC | Good Till Cancelled. | Rest until completion/cancellation or defined experiment end. |
| IOC | Immediate Or Cancel; execute available quantity and end the remainder. | Domain concept — explicit IOC deferred; Market does not require it. |
| FOK | Fill Or Kill; immediate complete execution or none. | Domain concept — deferred; never partial then reverse. |
| Self-Trade Prevention (STP) | Prevent execution within one trading account. | Cancel self-owned resting remainder; incoming continues. |
| Liquidity / Market Maker | Ability to execute size / participant supplying buying and selling interest. | Funded scenario accounts have no guaranteed replenishment. |
| Inventory | Assets held by a participant. | BTC available for scenario SELLs. |
| Slippage | Execution difference against a declared reference. | Adverse difference is positive in recommended reports. |
| Market Impact | Change in market conditions caused by trading. | Consuming low Asks can raise Last Price. |
| Price Protection | Absolute acceptable Market execution boundary in V1. | Maximum BUY 90,000; minimum SELL 80,000. |
| Market Data / Volume | Observations of interest/executions / quantity or value actually traded. | Funding creates no volume. |
| Candlestick / OHLCV | Interval summary of Open, High, Low, Close, Volume. | Recommended one-minute trade-derived candles. |
| Portfolio / Position | Holdings and performance / amount held. | Locked BTC remains part of the position. |
| Cost Basis / Average Cost | Assigned acquisition cost / basis per unit held. | Recommended weighted-average accounting includes buy fees. |
| Realized / Unrealized PnL | Gain/loss on sold units / indicative gain/loss on remaining holdings. | Seed funding is not profit. |
| Equity / Liquidation Value | Marked portfolio value / proceeds obtainable by selling holdings. | Last Price equity is not guaranteed liquidation proceeds. |
| Fee / Fee Treasury | Trading charge / its designated simulated platform recipient. | Recommended USDT fees are conserved with treasury balances. |
| Maker / Taker | Resting liquidity provider / incoming liquidity consumer for one Trade. | One order can take now and make later. |
| Tick / Quantity Step (Lot) | Allowed price / Base-quantity increments. | Recommended 0.01 USDT price tick. |
| Residual / Rounding | Amount left after allocation / a declared precision operation. | Retain cost residual until the position is fully sold. |
| Simulation Funding | Setup allocation of simulated capital. | No Trade, execution, volume, or profit. |
| Experiment / Experiment Clock | A bounded scenario / its authoritative virtual time. | Reset starts a separate history. |
| Algorithmic / Quantitative Trading | Automated decisions / decisions informed by quantitative evidence. | Strategy participation does not control matching rules. |
| Backtesting | Evaluating a strategy on historical scenarios/data with execution assumptions. | Historical-data backtesting is deferred beyond V1. |
| HFT / Latency | Timing-sensitive frequent automated trading / delay between relevant events. | Simulated observation/action delays are research inputs. |
| Execution Quality | Price, size, cost, completion, and timing of actual execution. | Speed without fills is not automatically better. |
| Stop / Stop-Limit / Trailing Stop | Triggered market/limit intent / a trigger that follows a reference. | Domain concepts — deferred, with no V1 trigger semantics. |
| Iceberg / Pegged Order | Partially displayed size / price linked to a reference. | Domain concepts — deferred beyond V1. |
| Margin / Leverage / Short Selling | Trading using credit or enlarged exposure / selling assets not owned. | Outside funded V1 spot scope. |
| Derivative / Futures / Perpetual / Option | Contracts tied to an underlying value, with differing expiry or exercise rights. | Domain concepts — all deferred beyond V1. |
| Business Invariant | Consistency condition within the stated model. | One Trade cannot settle twice. |

## 34. Team Knowledge Checklist

Explain the reason, not just the label. Distinguish approved decisions from recommended details.

1. What leaves and enters each account when buying BTC/USDT? How does selling SOL/USDT differ in units?
2. Why is an Order not a Trade, and why is administrative funding neither one?
3. A resting BUY is at 90,000 and an incoming SELL limit is 86,000. What is the execution price, and why?
4. Reverse which side is resting. Why does the execution price become 86,000?
5. How can one Limit Order be Taker on one Trade and Maker on a later Trade?
6. Why does a Market Buy specify Base quantity and an absolute maximum price rather than a Quote budget or slippage percentage?
7. What happens when only 3 BTC is eligible for a Market Buy of 5 BTC? What happens with zero eligible quantity?
8. Why does a GTC Limit remainder rest while a Market remainder never rests?
9. How do equal displayed timestamps, acceptance sequence, and order quantity affect priority?
10. When an incoming order encounters its own account's resting order, which order is cancelled and what market/fee effects are prohibited?
11. Why does STP not prevent a trade merely because two distinct accounts share a human owner?
12. How do aggregated levels differ from individual orders, and what do count/quantity ahead tell a researcher?
13. Why must BTC/USDT and ETH/USDT orders not reuse the same reserved USDT?
14. Calculate the protected Market Buy reservation for 2 BTC, maximum 90,000, taker fee 0.2%.
15. Why does a Limit Buy reserve using the larger maker/taker rate even while it is currently a taker?
16. In section 12's fee-enabled partial fill, explain the 30,060 cost, 48,096 remaining reserve, and 2,004 release.
17. Why can SELL reserve only Base while still funding its USDT fee under the recommended rate bounds?
18. Reconcile the buyer, seller, and treasury balances after each Trade in section 18.
19. Why can a CANCELLED order have nonzero Filled Quantity? Which events distinguish user cancellation, STP, and a Market remainder?
20. If cancellation is too late, why can it not reverse a Trade? What priority does a separately submitted replacement receive?
21. Explain how Last Price, Best Bid, Best Ask, and Average Execution Price can all differ.
22. Distinguish slippage from impact and fees. Why can mandatory protection still result in incomplete execution?
23. How do empty minutes, same-time Trades, and a Trade exactly on a minute boundary affect the recommended candles?
24. How do weighted-average basis, fees, seeded assets, and unavailable/stale Last Price affect PnL and portfolio valuation?
25. Which decisions are already APPROVED, which require final owner review, and why do simulated queue/delay experiments not establish production HFT performance?

## 35. Review Log

### Review Status

**DRAFT — REQUIRES FINAL OWNER REVIEW**

OQ-001 through OQ-014 remain APPROVED. OQ-015 through OQ-046 remain PROPOSED — OWNER REVIEW REQUIRED in both language editions. No final owner approval of those recommendations is asserted.

### Review Log

| Date | Reviewer | Section | Decision / Comment |
|------|----------|---------|--------------------|
| 2026-10-06 | Codex, incorporating owner instructions | 1–35; OQ-001–OQ-014 | Incorporated the decisions explicitly approved by the owner in the task. No human reviewer name was supplied. |
| 2026-10-06 | Codex | 1–35; OQ-015–OQ-046 | Selected concrete recommended V1 decisions requiring owner review; performed a document-wide consistency pass and aligned the English/Vietnamese editions. This is not final owner approval. |
