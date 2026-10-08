# Market Data business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-mkt-001"></a>

## [BS-MKT-001](market-data.md#bs-mkt-001) — Before first Trade; funding/reference cannot set Last Price

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment; Balance, Position.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004) [OQ-033](../decisions.md#oq-033) [OQ-042](../decisions.md#oq-042)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-033](../decisions.md#oq-033), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Do not invent market history from configured capital.

### Preconditions

No actual Trade yet; seeded Base/reference cost and possibly noncrossing resting Orders exist. Observation facts supplied without asserting delivery policy.

### Trigger

Inspect market observations.

### Main flow

1. [A] Funding/STP cannot create a Trade/price.
2. [P] Last Price unavailable before first actual Trade; seeded cost reference remains separately labelled.
3. [P] Actual book can supply Bid/Ask independently of absent Last Price.

### Alternative flows

Empty book makes both quotes/Spread unavailable; a one-sided book preserves known side only ([BS-MKT-002](market-data.md#bs-mkt-002)).

### Failure / invalid flows

No external/setup reference/midpoint/quote substitute for Last Price; no zero fake price.

### No-action behavior

No actual Trade keeps Last Price unavailable even as simulated capital or depth exists.

### Time and boundary behavior

Original virtual time is declared, not a real-market quotation timestamp.

### Postconditions and expected state changes

Observations preserve absent execution price and actual book distinction.

### Expected balance / Trade / fee / market-data effects

No traded volume/profit from funding/reference; portfolio nonzero unpriced Base valuation/equity unavailable.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-DATA](../diagrams/README.md#dia-data) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-002"></a>

## [BS-MKT-002](market-data.md#bs-mkt-002) — Empty and one-sided quotes / Spread

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Make absence different from a zero quote/spread.

### Preconditions

Supplied effective book facts for one symbol: empty, Bid-only, Ask-only or both sides.

### Trigger

Observe best quotes.

### Main flow

1. [P] Empty: Best Bid/Ask/Spread unavailable.
2. [P] One-sided: present-side best known, absent best/Spread unavailable.
3. [P] Both: best Bid 85,500, best Ask 86,000 gives Spread 500 USDT/BTC.

### Alternative flows

Last Price can be known from earlier Trades even when current book empty; quotes can exist before Last Price.

### Failure / invalid flows

Do not replace absent quote/Spread with zero or a setup reference; quantities are limited and not guaranteed liquidation value.

### No-action behavior

Unchanged book means same quote facts; no new Trade from observation.

### Time and boundary behavior

Use supplied business instant; precise grouped observation instant/delivery is [GAP-MKT-001](../review/gap-register.md#gap-mkt-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002).

### Postconditions and expected state changes

Quote availability and Spread reflect actual active sides.

### Expected balance / Trade / fee / market-data effects

No balance, fee or volume change from quoting; no promise of a fill.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-DATA](../diagrams/README.md#dia-data) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-003"></a>

## [BS-MKT-003](market-data.md#bs-mkt-003) — STP changes depth without execution data

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment; Order, Reservation.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004) [OQ-023](../decisions.md#oq-023)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Preserve execution measures through prevented self-interest.

### Preconditions

Known prior Last Price/volume and eligible self-owned resting quantity; accepted fully funded incoming reaches it; observe prevention event separately from any subsequent external Trade.

### Trigger

STP cancels self-owned active remainder.

### Main flow

1. [A] No Trade, fee, volume or Last Price update for prevention.
2. [P] Aggregate depth decreases by removed active quantity; own reason distinguishes STP.
3. [P] Release unused resting reserve under [OQ-023](../decisions.md#oq-023).

### Alternative flows

Later external matching changes data only by its actual Trades, never by cancelled self quantity.

### Failure / invalid flows

Do not count both a cancellation and a prevented pair as executions; prior legitimate history remains.

### No-action behavior

If no later actual Trade, Last Price stays the last real Trade even when new best quotes differ.

### Time and boundary behavior

Known immediate outcome/observation facts; delayed visibility remains separate [GAP-HFT-002](../review/gap-register.md#gap-hft-002).

### Postconditions and expected state changes

Book/own-order facts can change while execution summaries do not.

### Expected balance / Trade / fee / market-data effects

Zero prevented fee/volume; reservation release changes availability only.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-DATA](../diagrams/README.md#dia-data) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-004"></a>

## [BS-MKT-004](market-data.md#bs-mkt-004) — Source candle with Trades and once-counted gross volume

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Keep OHLCV distinct from average, fees and counterparty count.

### Preconditions

Four actual BTC Trades in one supplied virtual minute: 10:00:05 1 at 86,000; :20 0.5 at 88,000; :35 2 at 85,000; :50 0.5 at 87,000.

### Trigger

Summarize interval/cumulative contribution.

### Main flow

1. [P] OHLC 86,000 / 88,000 / 85,000 / 87,000 USDT/BTC.
2. [P] Base volume 4 BTC; gross Quote 86,000 + 44,000 + 170,000 + 43,500 = 343,500 USDT.
3. [P] Latest actual Trade sets Last Price 87,000.

### Alternative flows

Equal timestamps use actual execution order for first/last; later external Trades change cumulative totals by their own quantity/notional.

### Failure / invalid flows

No double count for buyer/seller, funding, fees, order submissions/cancellation or STP; Close is not Average Execution Price.

### No-action behavior

Re-observation of same actual occurrences does not add another volume occurrence.

### Time and boundary behavior

Each supplied Trade is inside one-minute start-inclusive/end-exclusive interval.

### Postconditions and expected state changes

Trade-derived summary reconciles exactly to actual history.

### Expected balance / Trade / fee / market-data effects

Summary/reporting changes no ownership or fee obligations; Quote volume is before fees.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANDLE](../diagrams/README.md#dia-candle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-005"></a>

## [BS-MKT-005](market-data.md#bs-mkt-005) — Empty candle after an earlier Trade

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Avoid carried-forward fake execution prices.

### Preconditions

A complete supplied one-minute interval has zero actual Trades; earlier latest Trade price 87,000 USDT/BTC.

### Trigger

Summarize the empty interval.

### Main flow

1. [P] OHLC unavailable, Base/Quote volumes zero.
2. [P] Separate Last Price remains actual prior 87,000, with virtual age relevant to Portfolio.

### Alternative flows

Before first Trade, separate Last Price also unavailable; same empty interval volumes still zero.

### Failure / invalid flows

No carry-forward candle Open/Close, fake flat candle execution or zero market price.

### No-action behavior

No Trade across minute is this explicit no-action execution case; quote changes/cancellations still create no volume.

### Time and boundary behavior

Interval already completed in fixture; whether idle running reaches completion is blocked [BS-EXP-003](experiment.md#bs-exp-003)/[GAP-EXP-003](../review/gap-register.md#gap-exp-003).

### Postconditions and expected state changes

Empty candle distinguishable from price retention.

### Expected balance / Trade / fee / market-data effects

No transfer, fee or volume increment; prior Last Price not re-timestamped as a fresh Trade.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANDLE](../diagrams/README.md#dia-candle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-006"></a>

## [BS-MKT-006](market-data.md#bs-mkt-006) — Trade exactly on minute boundary and execution-time ties

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Make interval membership and first/last ordering reproducible.

### Preconditions

Actual accepted execution facts supplied at 10:00:59 UTC and 10:01:00 UTC, with multiple 10:01:00 Trades already in authoritative execution order.

### Trigger

Group/summarize them.

### Main flow

1. [P] 10:00:59 Trade belongs to minute beginning 10:00.
2. [P] Exactly 10:01:00 belongs to next minute, never previous.
3. [P] Same-time Open/Close uses supplied execution order.

### Alternative flows

Trade at exactly minute start is included; Trade exactly minute end is excluded; no double assignment.

### Failure / invalid flows

Cannot choose first/last from client timestamp, displayed tie or counterparties. Initial scheduled arrival arbitration is [BS-MAT-004](matching-stp.md#bs-mat-004).

### No-action behavior

Intervals without actual Trades use [BS-MKT-005](market-data.md#bs-mkt-005).

### Time and boundary behavior

This is a candle boundary away from experiment end. Minute grouping does not decide execution eligibility exactly at end.

### Postconditions and expected state changes

Each actual Trade belongs once; order-based Open/Close unambiguous with given execution order.

### Expected balance / Trade / fee / market-data effects

Volume/notional unchanged by timestamp display format or client local timezone.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANDLE](../diagrams/README.md#dia-candle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-007"></a>

## [BS-MKT-007](market-data.md#bs-mkt-007) — Effective end inside minute retains partial final interval

Status: PROVISIONAL

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment; Order.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-042](../decisions.md#oq-042)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Separate final summary completeness from valid Trades.

### Preconditions

Effective end supplied at virtual 10:01:30 UTC, with actual prior Trades inside that minute or none. End eligibility/order already known.

### Trigger

Finalize history at that effective end.

### Main flow

1. [P] Retain actual summary of final incomplete one-minute interval and label partial.
2. [P] If no Trades in it, OHLC unavailable and volumes zero; no carried-forward price.

### Alternative flows

A run with no previous Trades retains no invented Last Price/volume; exact complete-minute end does not by itself create a fictitious traded candle.

### Failure / invalid flows

No reversal of earlier Trades or extension of trading to fill the minute. Exact-at-end arriving Order/pending observation outcomes are OPEN in [BS-EXP-008](experiment.md#bs-exp-008).

### No-action behavior

No new activity makes no new execution volume or candle prices.

### Time and boundary behavior

End time is supplied only for this fixture, not a default/supported duration or chosen auto-end mechanism.

### Postconditions and expected state changes

Partial label states interval incompleteness; actual Trade history stays valid as originally occurred.

### Expected balance / Trade / fee / market-data effects

Release/end Order effects are [BS-EXP-004](experiment.md#bs-exp-004); candle reporting changes no financial facts.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANDLE](../diagrams/README.md#dia-candle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mkt-008"></a>

## [BS-MKT-008](market-data.md#bs-mkt-008) — Changing book facts inside an alleged observation

Status: OPEN / BLOCKED

**Actors:** Observing participant, Exchange / Trading Platform; originating trading accounts only as anonymous public facts or own details.

**Related business objects:** Order Book, Market Data Observation, Trade history, Candle Interval, Experiment.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044) [BR-MKT-001](../08-market-data.md#br-mkt-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [BR-MKT-004](../08-market-data.md#br-mkt-004) [MKT-INV-001](../invariants.md#mkt-inv-001) [MKT-INV-002](../invariants.md#mkt-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [TRD-INV-004](../invariants.md#trd-inv-004) [OQ-041](../decisions.md#oq-041) [GAP-MKT-001](../review/gap-register.md#gap-mkt-001) [GAP-HFT-002](../review/gap-register.md#gap-hft-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039), [OQ-041](../decisions.md#oq-041), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | [GAP-MKT-001](../review/gap-register.md#gap-mkt-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002). |

### Business purpose

Prevent incompatible time references from silently becoming research input.

### Preconditions

Depth/queue facts before a fill and best quotes after it are supplied; source has not selected grouped observation meaning or delivery policy.

### Trigger

A strategy receives/uses purported combined market observation.

### Main flow

1. [P] Individual depth/quote/own-account concepts exist in [OQ-037](../decisions.md#oq-037).
2. [OPEN] Grouped business instant/coherence is [GAP-MKT-001](../review/gap-register.md#gap-mkt-001); delivery/seen scope is [GAP-HFT-002](../review/gap-register.md#gap-hft-002).

### Alternative flows

Consistent snapshot or explicitly time-labelled independent facts are discussion alternatives; neither adopted.

### Failure / invalid flows

Cannot assert actual observed reference/queue as one coherent fact without a decision; no technical sampling/transport/atomicity mechanism chosen.

### No-action behavior

No observation itself creates a Trade/fee; it can still change a strategy later decision, which remains input-dependent.

### Time and boundary behavior

Observation time and receipt/decision time differ conceptually; precise rules are OPEN.

### Postconditions and expected state changes

Complete expected observation/strategy input BLOCKED.

### Expected balance / Trade / fee / market-data effects

Potential slippage/queue/comparative research result differs; no selected data or balances.

### Known unresolved questions

[GAP-MKT-001](../review/gap-register.md#gap-mkt-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002).

### Diagram and scenario-specific reading

[DIA-OBS](../diagrams/README.md#dia-obs) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
