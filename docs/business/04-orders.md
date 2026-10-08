# Orders and cancellation

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-6"></a>

## 6. Orders

**ESTABLISHED DOMAIN CONCEPT.** An Order can produce zero, one, or several Trades. BUY spends Quote and acquires Base; SELL gives Base and receives Quote.

| Quantity | Meaning |
|----------|---------|
| Original Quantity | Base amount requested, unchanged throughout this V1 order. |
| Filled Quantity | Sum of this order's actual Trade quantities. |
| Unexecuted Quantity | Original minus Filled, including any portion that has ended. |
| Active Remaining Quantity | Unexecuted quantity still eligible to trade; zero after termination. |

**APPROVED — [OQ-004](decisions.md#oq-004), [OQ-006](decisions.md#oq-006), [OQ-008](decisions.md#oq-008):** all V1 orders specify Base quantity. Limit Orders include a limit; Market Buy includes a maximum price and Market Sell a minimum price. “Spend up to `100,000 USDT`” is not a V1 Market Buy instruction. Price limits/protection constrain unit execution price; fees are funded separately.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-017](decisions.md#oq-017), [OQ-018](decisions.md#oq-018), [OQ-019](decisions.md#oq-019), [OQ-020](decisions.md#oq-020), [OQ-046](decisions.md#oq-046):** use these explicit teaching-market bounds, not real-exchange specifications:

| Symbol | Price tick (USDT) | Inclusive price range (USDT) | Quantity step = minimum order | Maximum order quantity |
|--------|-------------------|------------------------------|-------------------------------|------------------------|
| BTC/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.000001 BTC` | `100 BTC` |
| ETH/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.00001 ETH` | `1,000 ETH` |
| SOL/USDT | `0.01` | `0.01`–`1,000,000.00` | `0.001 SOL` | `100,000 SOL` |

Prices include Limit and Market protection prices. Quantities must be positive step multiples within the bounds. There is no additional minimum or maximum notional. Invalid values are rejected before trading, never silently rounded or resized. Fills and active remainders remain step multiples; no separate minimum applies to them. A positive remainder cannot fall below one step.

<a id="source-section-7"></a>

## 7. Limit Order Business Behavior

**ESTABLISHED DOMAIN CONCEPT.** BUY LIMIT sets the maximum price; SELL LIMIT sets the minimum. A limit is a boundary, not an exact execution-price request. See [Investor.gov — Types of Orders](https://www.investor.gov/introduction-investing/investing-basics/how-stock-markets-work/types-orders).

| Incoming order | Opposing resting order | V1 outcome before fees |
|----------------|------------------------|------------------------|
| BUY LIMIT `1 BTC @ 90,000` | SELL `1 BTC @ 86,000` | Trade at `86,000`; buyer improves by `4,000 USDT`. |
| BUY LIMIT `1 BTC @ 90,000` | SELL `1 BTC @ 91,000` | No match at this price. |
| SELL LIMIT `1 BTC @ 85,000` | BUY `1 BTC @ 87,000` | Trade at `87,000`; seller improves by `2,000 USDT`. |
| SELL LIMIT `1 BTC @ 85,000` | BUY `1 BTC @ 84,000` | No match at this price. |

**APPROVED — [OQ-009](decisions.md#oq-009), [OQ-012](decisions.md#oq-012):** an incoming Limit Order first consumes eligible resting interest at resting prices; its unfilled remainder rests as GTC. A Limit Order can therefore be immediately aggressive. It need not fill at all, and its resting remainder has no guaranteed future fill.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-022](decisions.md#oq-022), [OQ-023](decisions.md#oq-023):** reserve enough Quote for a BUY's limit price and the larger possible fee rate; release excess after each Trade. Amendment is deferred (**[OQ-028](decisions.md#oq-028), [OQ-029](decisions.md#oq-029)**).

<a id="source-section-8"></a>

## 8. Market Order Business Behavior

**APPROVED — [OQ-006](decisions.md#oq-006), [OQ-007](decisions.md#oq-007), [OQ-008](decisions.md#oq-008), [OQ-012](decisions.md#oq-012):** Market Buy and Sell request a Base quantity, execute immediately within a mandatory absolute price boundary, and end any unfilled portion. A buy consumes Asks from lowest price; a sell consumes Bids from highest price. The boundary is a maximum for BUY and a minimum for SELL, never a percentage-based slippage instruction.

Example: Market Buy `2 BTC`, maximum `90,000`. Resting Asks are `1 BTC @ 86,000` and `2 BTC @ 87,000`. It buys `1 BTC` at each price, spending `173,000 USDT` before fees. Average Execution Price is `173,000 / 2 = 86,500`.

If that maximum were `86,000`, it would buy only `1 BTC`; the other `1 BTC` ends immediately. If there is zero eligible liquidity, it creates zero Trades and ends without execution. Completed partial fills stand. No Market remainder rests, and the trader does not supply IOC.

Market Sell `2 BTC`, minimum `80,000`, can sell to bids of `82,000` and `80,000`, but not `79,000`. Protection limits execution prices; it does not guarantee quantity or execution at the boundary.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-021](decisions.md#oq-021), [OQ-022](decisions.md#oq-022), [OQ-027](decisions.md#oq-027):** validate full protected funding before accepting the order, even if the book looks thin. Invalid/unfunded requests are REJECTED. A valid Market Order ends FILLED if complete or CANCELLED with reason MARKET_REMAINDER otherwise, including zero fills. Section 13 defines this terminology.

<a id="source-section-11"></a>

## 11. Partial Fills

**ESTABLISHED DOMAIN CONCEPT.** Filled Quantity accumulates across Trades. For a zero-fee incoming BUY LIMIT `10 BTC @ 90,000` consuming already resting Asks:

| Trade | Quantity | Resting price | Cumulative filled | Unexecuted |
|-------|----------|---------------|-------------------|------------|
| 1 | `4 BTC` | `86,000` | `4 BTC` | `6 BTC` |
| 2 | `3 BTC` | `87,000` | `7 BTC` | `3 BTC` |
| 3 | `3 BTC` | `88,000` | `10 BTC` | `0 BTC` |

Total value is `344,000 + 261,000 + 264,000 = 869,000 USDT`; the average is `86,900`. All three prices are existing resting prices, not prices later imposed on an already resting buy.

**APPROVED — [OQ-007](decisions.md#oq-007), [OQ-012](decisions.md#oq-012):** if only Trade 1 is immediately possible, a GTC Limit remainder of `6 BTC` rests at `90,000`. The equivalent protected Market Buy ends that remainder instead. A later sell against the resting buy executes at `90,000`, not at the new seller's lower limit.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-027](decisions.md#oq-027):** after cancelling that Limit remainder, filled is `4`, unexecuted is `6`, active remaining is `0 BTC`. A cancelled order may contain valid Trade history.

<a id="source-section-13"></a>

## 13. Order Lifecycle

**PROJECT V1 RULE — PROPOSED — OWNER REVIEW REQUIRED — [OQ-025](decisions.md#oq-025), [OQ-026](decisions.md#oq-026), [OQ-027](decisions.md#oq-027).** Use five observable states. NEW is not an observable V1 state; acceptance is a business event. CANCEL_PENDING is not an order state. A cancellation request alone does not establish a successful cancellation.

| State | Meaning | Terminal? |
|-------|---------|-----------|
| OPEN | Accepted GTC Limit Order resting with no fills. | No |
| PARTIALLY_FILLED | GTC Limit Order with fills and an active resting remainder. | No |
| FILLED | Filled Quantity equals Original Quantity. | Yes |
| CANCELLED | Unfilled portion ended; Filled Quantity may be zero or positive. | Yes |
| REJECTED | Request failed validation/funding before acceptance, Trades, or STP effects. | Yes |

Initial outcomes: accepted Limit → OPEN, PARTIALLY_FILLED, or FILLED; accepted Market → FILLED or CANCELLED. Invalid requests → REJECTED. A Market's partial fills remain in history, but PARTIALLY_FILLED is not its final state.

For resting Limits: OPEN → PARTIALLY_FILLED/FILLED/CANCELLED; PARTIALLY_FILLED → PARTIALLY_FILLED/FILLED/CANCELLED. Terminal orders never reactivate. Active Remaining equals Unexecuted only for active orders; it is zero for every terminal order. Filled Quantity remains cumulative.

Cancellation reasons are USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, and EXPERIMENT_ENDED. They explain the event without adding lifecycle states. MARKET_REMAINDER covers insufficient liquidity or the protection boundary, including zero fills. EXPIRED and amendment transitions are deferred (**[OQ-028](decisions.md#oq-028), [OQ-030](decisions.md#oq-030)**). The end-of-experiment rule is proposed under **[OQ-035](decisions.md#oq-035), [OQ-042](decisions.md#oq-042)**.

<a id="source-section-14"></a>

## 14. Order Cancellation

**ESTABLISHED DOMAIN CONCEPT.** **Cancel Order != Reverse Trade.** Only an active unfilled remainder can be cancelled. Already exchanged assets and fees are not returned by cancelling that remainder.

Example: a zero-fee buy of `1 BTC` fills `0.4 BTC` for `30,000 USDT`; cancelling the remaining `0.6 BTC` preserves that purchase and releases only unused reservation. A FILLED order has nothing left to stop.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-031](decisions.md#oq-031):** order acceptance, cancellation decisions, and experiment end share authoritative business ordering. Client timestamps do not override it. An incoming order's entire immediate matching outcome completes before the next order/cancel action becomes effective. This is a business precedence rule, not a threading or sequencing implementation.

If the effective fill comes first, it stands and a later cancellation stops only the then-active remainder. If cancellation comes first, that remainder cannot trade later. After full fill, return “too late/already filled”; after previous cancellation, return “already ended”; neither changes history or releases funds twice. A nonexistent or rejected request has no active order to cancel. Participant cancellation applies only to its own account's orders.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-028](decisions.md#oq-028), [OQ-029](decisions.md#oq-029):** changing intent means successfully cancelling the old remainder, then independently submitting a new order. There is no combined amend/cancel-and-replace operation. The new order must pass all checks and receives new acceptance priority; rejection does not restore the old order.

<a id="source-section-15"></a>

## 15. Time In Force

**ESTABLISHED DOMAIN CONCEPT.** Time In Force describes persistence/completeness separately from Order Type. [Investor.gov's order guidance](https://www.investor.gov/introduction-investing/general-resources/news-alerts/alerts-bulletins/investor-bulletins-14) explains these common concepts:

| Concept | General meaning | V1 status |
|---------|-----------------|-----------|
| GTC — Good Till Cancelled | Remain eligible until filled or cancelled, subject to venue rules. | APPROVED for every Limit Order. |
| IOC — Immediate Or Cancel | Execute eligible immediate quantity and cancel the rest. | Domain concept — not supported as an explicit V1 instruction. |
| FOK — Fill Or Kill | Execute the full quantity immediately or execute none. | Domain concept — not supported in V1. |

For `5 BTC` requested with `3 BTC` immediately eligible: V1 GTC Limit may fill `3` and rest `2`; V1 Market may fill `3` and end `2`. Industry FOK would execute zero, not execute `3` then reverse it.

**APPROVED — [OQ-007](decisions.md#oq-007), [OQ-012](decisions.md#oq-012):** V1 Market is intrinsically immediate-only and never requires the trader to specify IOC. GTC persists until fill, explicit cancellation, or another defined V1 ending rule.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-030](decisions.md#oq-030), [OQ-035](decisions.md#oq-035):** no automatic expiry or maximum GTC lifetime within an experiment. Ending an experiment cancels its resting remainders with EXPERIMENT_ENDED; this is not EXPIRED.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-ord-002"></a>BR-ORD-002 | Limit uses GTC; Market is immediate-only and its remainder never rests. Explicit IOC/FOK are deferred. | Separate type from persistence. | APPROVED | [OQ-007](decisions.md#oq-007), [OQ-012](decisions.md#oq-012) |
| <a id="br-ord-003"></a>BR-ORD-003 | Use OPEN, PARTIALLY_FILLED, FILLED, CANCELLED, REJECTED and the transitions/reasons in section 13. | Represent outcomes without unnecessary states. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-025](decisions.md#oq-025), [OQ-026](decisions.md#oq-026), [OQ-027](decisions.md#oq-027) |
| <a id="br-ord-004"></a>BR-ORD-004 | Support Limit and protected Market only; both BUY and SELL use Base quantity. | Explicit trading intent and bounds. | APPROVED | [OQ-004](decisions.md#oq-004), [OQ-006](decisions.md#oq-006), [OQ-008](decisions.md#oq-008) |
| <a id="br-ord-007"></a>BR-ORD-007 | Use separate cancel then new order with new priority; no amendment, combined replace, or expiry. | Keep order commitments stable. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-028](decisions.md#oq-028), [OQ-029](decisions.md#oq-029), [OQ-030](decisions.md#oq-030), [OQ-031](decisions.md#oq-031), [OQ-032](decisions.md#oq-032) |

## Phase B — behavioral analysis

### Purpose, actors and attributes

An account submits BUY/SELL intent; matching can yield no execution, one fill or several. The platform determines acceptance and effective cancellation. Cancellation affects active intent, never the earlier exchange. Strategy permissions retain their proposed participation dependencies.

| Attribute | Business meaning / stability | Source |
|-----------|------------------------------|--------|
| Owning trading account / symbol / side / type | Own-cancel/STP identity; market; acquire/give Base; Limit or protected Market | [OQ-001](decisions.md#oq-001)/[OQ-004](decisions.md#oq-004)/[OQ-011](decisions.md#oq-011); source §6/§14 |
| Original Base quantity | Requested units; unchanged under no-amendment proposal | Source §6; [OQ-028](decisions.md#oq-028); [ORD-INV-001](invariants.md#ord-inv-001) |
| Limit / Market protection | Unit-price eligibility, independent of fees; no changes under [OQ-028](decisions.md#oq-028) | [OQ-008](decisions.md#oq-008)/[OQ-009](decisions.md#oq-009)/[OQ-028](decisions.md#oq-028) |
| Filled / Unexecuted / Active Remaining | Cumulative actual executions / not executed / still eligible | Source §6; [OQ-027](decisions.md#oq-027); [ORD-INV-002](invariants.md#ord-inv-002) |
| Acceptance sequence | Authoritative same-price priority; resting remainder retains original | [OQ-010](decisions.md#oq-010); [ORD-INV-005](invariants.md#ord-inv-005) |
| Observable state / termination reason | Proposed outcomes separate from intent/request events | [OQ-025](decisions.md#oq-025)/[OQ-026](decisions.md#oq-026)/[OQ-027](decisions.md#oq-027) |

The request exists as submitted intent before acceptance; REJECTED describes a failed request without an accepted trading commitment ([OQ-027](decisions.md#oq-027)). No NEW/CANCEL_PENDING state is invented. Account reassignment and repeated submission identity remain [GAP-ACT-001](review/gap-register.md#gap-act-001)/[GAP-ORD-001](review/gap-register.md#gap-ord-001). Exact original attribute definitions and five states are canonical in Phase A above.

### Preconditions and actions

For a complete proposed acceptance flow: type/symbol/absolute boundary/numeric gates pass and full available funding covers the requested protected quantity before Trades or STP ([OQ-021](decisions.md#oq-021)/[OQ-022](decisions.md#oq-022)). Invalid values are not rounded; underfunding is not downsized. A valid accepted Market with no eligible liquidity is a different outcome from a REJECTED request. Book snapshots do not guarantee the effective book.

### Transition table — derived, all named states PROVISIONAL

| Initial business state | Trigger / conditions | Resulting state | Quantity, financial and history effect | Basis |
|------------------------|----------------------|-----------------|---------------------------------------|-------|
| Submitted request, not accepted | Invalid validation or full funding | REJECTED | No acceptance, Trade or STP; quantity facts for malformed/negative requests UNDECIDED — [GAP-ORD-002](review/gap-register.md#gap-ord-002) | [OQ-017](decisions.md#oq-017)–[OQ-022](decisions.md#oq-022)/[OQ-027](decisions.md#oq-027) |
| Accepted incoming Limit | No immediate fill | OPEN | Full funded quantity rests GTC | [OQ-012](decisions.md#oq-012)/[OQ-025](decisions.md#oq-025)/[OQ-027](decisions.md#oq-027) |
| Accepted incoming Limit | Some filled; positive remainder | PARTIALLY_FILLED | Fills stand; remainder rests with original sequence/reserve | [OQ-010](decisions.md#oq-010)/[OQ-012](decisions.md#oq-012)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) |
| Accepted incoming Limit or Market | All quantity executed | FILLED | Active/unexecuted zero; unused reserve released | [OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) |
| Accepted incoming Market | Zero/some filled; remainder remains | CANCELLED / MARKET_REMAINDER | No resting remainder; filled history stands; release unused reserve | [OQ-007](decisions.md#oq-007)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) |
| OPEN | Partial or full later fill | PARTIALLY_FILLED or FILLED | Filled accumulates once; remainder/reserve recomputed | [OQ-023](decisions.md#oq-023)/[OQ-024](decisions.md#oq-024)/[OQ-027](decisions.md#oq-027); [TRD-INV-003](invariants.md#trd-inv-003) |
| PARTIALLY_FILLED | Further partial or full fill | PARTIALLY_FILLED or FILLED | Cumulative fills; original sequence retained if active | [OQ-010](decisions.md#oq-010)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) |
| OPEN / PARTIALLY_FILLED | Effective own cancel / STP / experiment end | CANCELLED with applicable reason | Active zero; preserve filled/unexecuted; release unused reserve only | [OQ-011](decisions.md#oq-011)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027)/[OQ-031](decisions.md#oq-031)/[OQ-035](decisions.md#oq-035) |
| FILLED / CANCELLED / REJECTED | Later activity | Same terminal state | No reactivation or duplicate release/history reversal | [OQ-027](decisions.md#oq-027)/[OQ-031](decisions.md#oq-031) |

### Decision table — type × eligible quantity × protection

| Initial state / gates | Action and liquidity condition | Outcome / state | Financial effect | Rule | Status |
|-----------------------|-------------------------------|-----------------|------------------|------|--------|
| Valid fully funded Limit | No eligible external interest after STP | Remainder rests; OPEN if zero filled | Keep required reserve; no fill fee | [OQ-011](decisions.md#oq-011)/[OQ-012](decisions.md#oq-012)/[OQ-022](decisions.md#oq-022)/[OQ-027](decisions.md#oq-027) | PROVISIONAL complete outcome |
| Valid fully funded Limit | Some / all quantity eligible | Rest partial remainder / finish FILLED | Settle actual fills; recompute/release | [OQ-009](decisions.md#oq-009)/[OQ-012](decisions.md#oq-012)/[OQ-023](decisions.md#oq-023)/[OQ-024](decisions.md#oq-024)/[OQ-027](decisions.md#oq-027) | PROVISIONAL |
| Valid fully funded Market | Full quantity within inclusive protection | FILLED, never rests | Actual fills/fees; unused funding released | [OQ-007](decisions.md#oq-007)/[OQ-008](decisions.md#oq-008)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) | PROVISIONAL |
| Valid fully funded Market | Thin book or next price outside protection | CANCELLED/MARKET_REMAINDER after partial/zero result | Earlier fills/fees stand; release unused | [OQ-007](decisions.md#oq-007)/[OQ-008](decisions.md#oq-008)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) | PROVISIONAL |
| Submitted request | Invalid or underfunded | REJECTED before STP | No new Trade/fee/old-order cancellation | [BR-BAL-004](06-balance-reservation.md#br-bal-004); [OQ-027](decisions.md#oq-027) | PROVISIONAL |
| Arrival exactly at/after experiment end | Time eligibility unknown | UNDECIDED — [GAP-EXP-004](review/gap-register.md#gap-exp-004) | Complete acceptance/cancellation result blocked | [OQ-031](decisions.md#oq-031)/[OQ-035](decisions.md#oq-035) | OPEN |

### Decision table — state × cancellation

Each row assumes known effective business order and participant account identity. Request arrival ties/end eligibility are separate gaps.

| Initial state / target | Trigger / condition | Outcome / resulting state | Financial consequence | Basis | Status |
|------------------------|---------------------|---------------------------|-----------------------|-------|--------|
| Own OPEN | Effective cancel before later match | CANCELLED/USER_REQUEST | Release all unused reservation; no Trade | [OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027)/[OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Own PARTIALLY_FILLED | Effective cancel | CANCELLED/USER_REQUEST; filled history kept | Release only unused reserve; no refund of fills/fees | [OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027)/[OQ-031](decisions.md#oq-031); [BAL-INV-003](invariants.md#bal-inv-003) | PROVISIONAL |
| Own FILLED | Cancel request | Too late/already filled; remains FILLED | No change/release | [OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Own CANCELLED | Cancel again | Already ended; remains CANCELLED | No second release | [OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Own REJECTED / nonexistent | Cancel | No active Order to cancel | No trading/financial change | Source §14/[OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Another account target | Participant cancel | Cannot cancel target; no authorized transition | Target reservation/history unaffected | Source §14/[OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Own active remainder | Fill effective before cancel | Fill stands; cancel only what remains, or too late if filled | Actual settlement/fees stand | [OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Any target near end | Arrival/effective order not decided | UNDECIDED — [GAP-EXP-004](review/gap-register.md#gap-exp-004)/[GAP-MAT-001](review/gap-register.md#gap-mat-001) | Do not infer cancel success | [OQ-031](decisions.md#oq-031)/[OQ-035](decisions.md#oq-035) | OPEN |

### No action, boundaries and replacement

Within a running experiment, GTC has no automatic expiry or lifetime cap under proposed [OQ-030](decisions.md#oq-030). With no counterparty action/cancel/end, it gains no fill and retains its acceptance sequence/reserve; virtual progression itself is [GAP-EXP-003](review/gap-register.md#gap-exp-003). Separate effective cancel then independent new Order is proposed [OQ-029](decisions.md#oq-029): fresh validation/funding/priority, no restoration if new request rejects. Repeated cancellation is defined; repeated submission is [GAP-ORD-001](review/gap-register.md#gap-ord-001).

Review [BS-ORD-001](scenarios/orders.md#bs-ord-001) through [BS-ORD-019](scenarios/orders.md#bs-ord-019), [DIA-ORD](diagrams/README.md#dia-ord), [DIA-CANCEL](diagrams/README.md#dia-cancel), [DIA-PRECEDENCE](diagrams/README.md#dia-precedence) and [DIA-VALID](diagrams/README.md#dia-valid). Exact numeric boundaries remain in source §6/[OQ-017](decisions.md#oq-017)–[OQ-020](decisions.md#oq-020); no additional notional cap or minimum fill is implied. Read Matching, Reservation, Settlement and Experiment for complete outcomes. Missing experiment timing/identity must remain visible even when the local Order table is complete. [BS-ORD-019](scenarios/orders.md#bs-ord-019)/[GAP-ORD-002](review/gap-register.md#gap-ord-002) records the tension between invalid rejected quantities and the preserved every-order quantity invariants; no submitted quantity is normalized to hide it.
