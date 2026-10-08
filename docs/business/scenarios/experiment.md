# Experiment business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-exp-001"></a>

## [BS-EXP-001](experiment.md#bs-exp-001) — Prepare explicit valid scenario inputs

Status: PROVISIONAL

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-016](../decisions.md#oq-016) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-033](../decisions.md#oq-033) [OQ-035](../decisions.md#oq-035) [OQ-036](../decisions.md#oq-036) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-SIM-001](../11-administration.md#br-sim-001) [BR-SIM-002](../02-experiment.md#br-sim-002) [BR-ACT-002](../01-scope-actors.md#br-act-002) [SIM-INV-001](../invariants.md#sim-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-016](../decisions.md#oq-016), [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-033](../decisions.md#oq-033), [OQ-035](../decisions.md#oq-035), [OQ-036](../decisions.md#oq-036), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | [GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-BAL-001](../review/gap-register.md#gap-bal-001) for invalid setup/start; [GAP-ACT-001](../review/gap-register.md#gap-act-001)/[GAP-ACT-002](../review/gap-register.md#gap-act-002) for membership/hosting, excluded from this valid prepared fixture. |

### Business purpose

Establish capital and reproducible inputs without fabricated liquidity.

### Preconditions

Before running; administrator supplies date, normal USDT allocations, dedicated Base quantities/reference costs, explicit valid fees, scenario action order and nonnegative fixed delays.

### Trigger

Administrator prepares the complete valid fixture.

### Main flow

1. [A] Identify allocations as Simulation Funding, not execution/profit.
2. [P] Use setup-only inputs and proposed asset grids/seeded weighted cost.
3. [P] Normal accounts have declared USDT and zero Base; funded scenario accounts can have Base; book remains empty until ordinary Orders while running.

### Alternative flows

Funding amounts/rates/date/delays can differ across scenarios; no universal default is inferred.

### Failure / invalid flows

Off-grid rates/allocations or absent seeded reference cannot satisfy the valid fixture. Partial invalid preparation/readiness effects remain OPEN [GAP-BAL-001](../review/gap-register.md#gap-bal-001), outside this valid-input subflow.

### No-action behavior

No scenario/participant Order means no depth, Trade, volume or Last Price.

### Time and boundary behavior

Start permission/readiness and required duration are blocked in [BS-EXP-002](experiment.md#bs-exp-002)/[BS-EXP-008](experiment.md#bs-exp-008); not implied by valid funding.

### Postconditions and expected state changes

Valid declared input/capital facts exist; no start decision is asserted.

### Expected balance / Trade / fee / market-data effects

Only funded balances and seeded basis change; seed cost is neither Last Price nor realized profit.

### Known unresolved questions

[GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-BAL-001](../review/gap-register.md#gap-bal-001) for invalid setup/start; [GAP-ACT-001](../review/gap-register.md#gap-act-001)/[GAP-ACT-002](../review/gap-register.md#gap-act-002) for membership/hosting, excluded from this valid prepared fixture.

### Diagram and scenario-specific reading

[DIA-FUND](../diagrams/README.md#dia-fund) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-002"></a>

## [BS-EXP-002](experiment.md#bs-exp-002) — Start and repeated/invalid experiment controls

Status: OPEN / BLOCKED

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book.

**Exact trace:** [OQ-035](../decisions.md#oq-035) [OQ-042](../decisions.md#oq-042) [BR-SIM-002](../02-experiment.md#br-sim-002) [GAP-EXP-001](../review/gap-register.md#gap-exp-001) [GAP-BAL-001](../review/gap-register.md#gap-bal-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-042](../decisions.md#oq-042) |
| Not defined by source | [GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-BAL-001](../review/gap-register.md#gap-bal-001)/[GAP-EXP-003](../review/gap-register.md#gap-exp-003). |

### Business purpose

Keep lifecycle authority explicit instead of inventing states.

### Preconditions

Prepared scenario exists; who may start and readiness definition are absent.

### Trigger

A participant/admin requests start, then repeats start or requests it during a different phase.

### Main flow

1. [P] Known running clock begins declared date at UTC midnight if a valid run is established.
2. [OPEN] Start authorization, valid transition and repeated/invalid result: UNDECIDED — [GAP-EXP-001](../review/gap-register.md#gap-exp-001).

### Alternative flows

No READY/PAUSED state, default duration or administrator-only start is selected.

### Failure / invalid flows

Cannot assert whether a bad/repeated start leaves setup unchanged, creates a new run or is rejected; source does not specify.

### No-action behavior

Without an authorized start result, do not infer running from prepared funds.

### Time and boundary behavior

Clock-control/pause/resume policy and handling submissions while stopped remain [GAP-EXP-003](../review/gap-register.md#gap-exp-003).

### Postconditions and expected state changes

Complete start lifecycle scenario blocked.

### Expected balance / Trade / fee / market-data effects

Whether scenario Orders/funding become a running history is not yet determined.

### Known unresolved questions

[GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-BAL-001](../review/gap-register.md#gap-bal-001)/[GAP-EXP-003](../review/gap-register.md#gap-exp-003).

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-003"></a>

## [BS-EXP-003](experiment.md#bs-exp-003) — No participant action and empty running book

Status: OPEN / BLOCKED

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book; Candle Interval, Market Data Observation.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-035](../decisions.md#oq-035) [OQ-036](../decisions.md#oq-036) [OQ-039](../decisions.md#oq-039) [OQ-041](../decisions.md#oq-041) [OQ-044](../decisions.md#oq-044) [BR-MKT-004](../08-market-data.md#br-mkt-004) [SIM-INV-001](../invariants.md#sim-inv-001) [GAP-EXP-003](../review/gap-register.md#gap-exp-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-036](../decisions.md#oq-036), [OQ-039](../decisions.md#oq-039), [OQ-041](../decisions.md#oq-041), [OQ-044](../decisions.md#oq-044) |
| Not defined by source | [GAP-EXP-003](../review/gap-register.md#gap-exp-003); observation delivery [GAP-HFT-002](../review/gap-register.md#gap-hft-002). |

### Business purpose

Separate no trading from no time progression.

### Preconditions

Assume running; no prior Trades, empty book, no scheduled scenario Orders and no pending effective participant actions.

### Trigger

The team waits for a nominal virtual minute without any participant action.

### Main flow

1. [A] No actual exchange is manufactured by funding/no action.
2. [P] If a complete empty virtual interval exists, OHLC unavailable and volumes zero; Last Price unavailable before first Trade.
3. [OPEN] Whether/when idle clock reaches that minute is [GAP-EXP-003](../review/gap-register.md#gap-exp-003).

### Alternative flows

Existing resting Orders or scheduled actions would leave this empty/no-actions fixture; do not claim no Trades when scenario Orders can cross.

### Failure / invalid flows

No always-on maker/refill/forced Trade is added. Stopped-clock submissions are undefined, not automatically queued or rejected.

### No-action behavior

Core financial no-action effect is known; complete progression/delivery flow is blocked.

### Time and boundary behavior

Stopped clock never advances (§24); that fact is not a selected idle-running progression or pause/resume capability.

### Postconditions and expected state changes

No-action produces no trading facts; elapsed-time/candle-arrival assertions await clock policy.

### Expected balance / Trade / fee / market-data effects

Balances/fees/Trade/volume unchanged; candle count and price age are not asserted until time progress is known.

### Known unresolved questions

[GAP-EXP-003](../review/gap-register.md#gap-exp-003); observation delivery [GAP-HFT-002](../review/gap-register.md#gap-hft-002).

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-004"></a>

## [BS-EXP-004](experiment.md#bs-exp-004) — Effective end with open/partly filled/no-Trade variants

Status: PROVISIONAL

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book; Order, Reservation, Trade, Fee, Fee Treasury, Candle Interval.

**Exact trace:** [OQ-010](../decisions.md#oq-010) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-030](../decisions.md#oq-030) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-045](../decisions.md#oq-045) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-SIM-002](../02-experiment.md#br-sim-002) [BR-TRD-001](../07-trade-settlement-fees.md#br-trd-001) [BAL-INV-003](../invariants.md#bal-inv-003) [ORD-INV-004](../invariants.md#ord-inv-004) [TRD-INV-001](../invariants.md#trd-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-027](../decisions.md#oq-027), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-039](../decisions.md#oq-039), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-045](../decisions.md#oq-045) |
| Not defined by source | [GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-EXP-002](../review/gap-register.md#gap-exp-002)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004) govern obtaining effective end; [GAP-EXP-005](../review/gap-register.md#gap-exp-005) governs later access. This scenario specifies conditional effects only. |

### Business purpose

Define termination effects after end has already been made effective.

### Preconditions

A running run has a supplied authoritative effective end after earlier immediate outcomes; may contain untouched or partly filled Limits, no active Orders, or no previous Trades.

### Trigger

End becomes effective in that supplied business order.

### Main flow

1. [P] Earlier incoming immediate outcomes are complete; valid Trades/fees stand.
2. [P] All active remainders end CANCELLED/EXPERIMENT_ENDED; release every unused reserve once.
3. [P] Preserve balances/history and any final partial candle label.

### Alternative flows

OPEN untouched Order: filled zero, active zero after end. PARTIALLY_FILLED: retain filled/history, active zero. Already FILLED/CANCELLED/REJECTED: no reactivation/release. No prior Trade: no new Trade/price manufactured.

### Failure / invalid flows

Repeated end request and initiating actor are not specified by this conditional subflow; complete control case is [BS-EXP-002](experiment.md#bs-exp-002)/[BS-EXP-008](experiment.md#bs-exp-008).

### No-action behavior

If no active Orders, end creates no fill/cancelled fictional Order or asset exchange; final Trade history remains as it was.

### Time and boundary behavior

End time/order supplied; exact-at-end/pending arrival inclusion is explicitly excluded, OPEN [GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

No active remainder survives effective end; prior units/fees preserved.

### Expected balance / Trade / fee / market-data effects

Only unused lock releases; no Trade reversal, fee refund, asset transfer to new run or invented price.

### Known unresolved questions

[GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-EXP-002](../review/gap-register.md#gap-exp-002)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004) govern obtaining effective end; [GAP-EXP-005](../review/gap-register.md#gap-exp-005) governs later access. This scenario specifies conditional effects only.

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-005"></a>

## [BS-EXP-005](experiment.md#bs-exp-005) — Reset into separately funded experiment

Status: PROVISIONAL

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book; Trade, Fee Treasury.

**Exact trace:** [OQ-010](../decisions.md#oq-010) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-030](../decisions.md#oq-030) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-045](../decisions.md#oq-045) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-SIM-002](../02-experiment.md#br-sim-002) [BR-TRD-001](../07-trade-settlement-fees.md#br-trd-001) [BAL-INV-003](../invariants.md#bal-inv-003) [ORD-INV-004](../invariants.md#ord-inv-004) [TRD-INV-001](../invariants.md#trd-inv-001) [OQ-040](../decisions.md#oq-040) [OQ-015](../decisions.md#oq-015)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-023](../decisions.md#oq-023), [OQ-027](../decisions.md#oq-027), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-039](../decisions.md#oq-039), [OQ-040](../decisions.md#oq-040), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-045](../decisions.md#oq-045) |
| Not defined by source | [GAP-EXP-001](../review/gap-register.md#gap-exp-001) for actual control; [GAP-EXP-005](../review/gap-register.md#gap-exp-005) for archival access. Conditional run-separation effect only. |

### Business purpose

Keep old research facts separate from a rerun.

### Preconditions

Old effective end has been supplied; a separately configured replacement run is supplied. Start/control permission is not tested in this fixture.

### Trigger

Inspect the reset/rerun relation between old and replacement experiments.

### Main flow

1. [P] Preserve old balances/Trades/fees/history under old run.
2. [P] Replacement capital comes from its own declared Simulation Funding, never asset transfer.
3. [P] Declared inputs/decisions identify the rerun; changed inputs are identified.

### Alternative flows

Old run may have zero Trades or be invalid; both histories remain independently explainable.

### Failure / invalid flows

No erase, historical correction, balance carryover by transfer, or claim old run funding was new Trade/profit.

### No-action behavior

Without a replacement supplied, old preserved facts do not become new running activity.

### Time and boundary behavior

No duration, automatic start/end, retained access length or physical clock relation is chosen.

### Postconditions and expected state changes

Separate runs and capital/history attribution; no independent business approval created.

### Expected balance / Trade / fee / market-data effects

Old treasury receipts stay old; new treasury zero under [OQ-015](../decisions.md#oq-015) if fee policy adopted.

### Known unresolved questions

[GAP-EXP-001](../review/gap-register.md#gap-exp-001) for actual control; [GAP-EXP-005](../review/gap-register.md#gap-exp-005) for archival access. Conditional run-separation effect only.

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-006"></a>

## [BS-EXP-006](experiment.md#bs-exp-006) — Run already designated invalid for research

Status: PROVISIONAL

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book; Trade, Fee Treasury.

**Exact trace:** [OQ-010](../decisions.md#oq-010) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-030](../decisions.md#oq-030) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-045](../decisions.md#oq-045) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-SIM-002](../02-experiment.md#br-sim-002) [BR-TRD-001](../07-trade-settlement-fees.md#br-trd-001) [BAL-INV-003](../invariants.md#bal-inv-003) [ORD-INV-004](../invariants.md#ord-inv-004) [TRD-INV-001](../invariants.md#trd-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-027](../decisions.md#oq-027), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-039](../decisions.md#oq-039), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-045](../decisions.md#oq-045) |
| Not defined by source | [GAP-ADM-001](../review/gap-register.md#gap-adm-001)/[GAP-ADM-002](../review/gap-register.md#gap-adm-002) are blocked designation/access scenarios; this is only the supplied-invalid conditional effect. |

### Business purpose

Preserve invalid evidence without Trade corrections.

### Preconditions

Run already designated invalid by a decision supplied externally to this fixture; effective end known. Authority/reasons for designation not inferred.

### Trigger

Apply prescribed invalid-history handling.

### Main flow

1. [P] Keep original Trades, fees, prices, balances and explicit invalid-for-research label.
2. [P] End through the known effective end subflow.
3. [P] Use a separate configured run for a fresh experiment.

### Alternative flows

Run may have no Trades, open Orders or partial fills; effective-end handling still applies to actual active remainders.

### Failure / invalid flows

Edit/delete/reverse/exceptional correction and funding-as-disguised-correction remain prohibited under proposed [OQ-045](../decisions.md#oq-045).

### No-action behavior

Nothing new is traded by labelling historical research validity.

### Time and boundary behavior

Who can classify/relabel, when it may occur and later review access are [GAP-ADM-001](../review/gap-register.md#gap-adm-001)/[GAP-ADM-002](../review/gap-register.md#gap-adm-002)/[GAP-EXP-005](../review/gap-register.md#gap-exp-005).

### Postconditions and expected state changes

Historical financial facts preserved with research label; no new INVALID Order/Experiment state.

### Expected balance / Trade / fee / market-data effects

Invalid designation does not itself change actual Trade transfers; end releases only unused reserve.

### Known unresolved questions

[GAP-ADM-001](../review/gap-register.md#gap-adm-001)/[GAP-ADM-002](../review/gap-register.md#gap-adm-002) are blocked designation/access scenarios; this is only the supplied-invalid conditional effect.

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-007"></a>

## [BS-EXP-007](experiment.md#bs-exp-007) — Finite ordinary scenario liquidity

Status: PROVISIONAL

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book; Order, Reservation, Trade.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-016](../decisions.md#oq-016) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-033](../decisions.md#oq-033) [OQ-035](../decisions.md#oq-035) [OQ-036](../decisions.md#oq-036) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-SIM-001](../11-administration.md#br-sim-001) [BR-SIM-002](../02-experiment.md#br-sim-002) [BR-ACT-002](../01-scope-actors.md#br-act-002) [SIM-INV-001](../invariants.md#sim-inv-001) [OQ-005](../decisions.md#oq-005) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [BR-ACT-001](../01-scope-actors.md#br-act-001) [TRD-INV-004](../invariants.md#trd-inv-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-005](../decisions.md#oq-005), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-016](../decisions.md#oq-016), [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-033](../decisions.md#oq-033), [OQ-035](../decisions.md#oq-035), [OQ-036](../decisions.md#oq-036), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Do not equate seeded assets with guaranteed depth or fills.

### Preconditions

Running run; funded scenario accounts with predefined valid Limits and supplied action order; other participant interest may already exist.

### Trigger

Each planned scenario Order becomes an ordinary effective submission.

### Main flow

1. [A/P] Apply ordinary funding/priority/STP gates; no administrator exception.
2. [P] Rest eligible unfilled Limits or legitimately match crossing interest.
3. [P] Depleted available inventory does not automatically refill; empty/one-sided book is allowed.

### Alternative flows

A scenario account can host an automated experiment only after its identity/capital relationship is clarified ([GAP-ACT-002](../review/gap-register.md#gap-act-002)); no extra account pooling assumed.

### Failure / invalid flows

Insufficient funds use ordinary rejection; no forced fill/Last Price or in-run funding to repair inventory.

### No-action behavior

If no scheduled Orders occur, setup funding remains capital with empty book.

### Time and boundary behavior

Submission occurs while running in declared action order; start and action/end ties remain separate gaps.

### Postconditions and expected state changes

Liquidity is finite ordinary active interest, not promised always-on maker service.

### Expected balance / Trade / fee / market-data effects

Actual Orders/Trades determine reserve/balance/depth effects; no automatic capital or volume.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-FUND](../diagrams/README.md#dia-fund) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-exp-008"></a>

## [BS-EXP-008](experiment.md#bs-exp-008) — 30-minute / one-hour end and boundary policy probes

Status: OPEN / BLOCKED

**Actors:** Administrator prepares scenario inputs; Exchange / Trading Platform applies known effects. Start/end initiator and validity classifier are not inferred.

**Related business objects:** Experiment, Scenario, Participant, Trading Account, Balance, Portfolio Position, Order Book; Order, Market Data Observation, Candle Interval.

**Exact trace:** [OQ-010](../decisions.md#oq-010) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-030](../decisions.md#oq-030) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-045](../decisions.md#oq-045) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-SIM-002](../02-experiment.md#br-sim-002) [BR-TRD-001](../07-trade-settlement-fees.md#br-trd-001) [BAL-INV-003](../invariants.md#bal-inv-003) [ORD-INV-004](../invariants.md#ord-inv-004) [TRD-INV-001](../invariants.md#trd-inv-001) [OQ-041](../decisions.md#oq-041) [GAP-EXP-001](../review/gap-register.md#gap-exp-001) [GAP-EXP-002](../review/gap-register.md#gap-exp-002) [GAP-EXP-003](../review/gap-register.md#gap-exp-003) [GAP-EXP-004](../review/gap-register.md#gap-exp-004) [GAP-MAT-001](../review/gap-register.md#gap-mat-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-027](../decisions.md#oq-027), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-039](../decisions.md#oq-039), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-045](../decisions.md#oq-045) |
| Not defined by source | [GAP-EXP-001](../review/gap-register.md#gap-exp-001)–[GAP-EXP-004](../review/gap-register.md#gap-exp-004)/[GAP-MAT-001](../review/gap-register.md#gap-mat-001). |

### Business purpose

Block duration/default/end assumptions in time-sensitive research.

### Preconditions

Hypothetical intended duration 30 virtual minutes or one virtual hour; these are review probes, not declared supported values/defaults.

### Trigger

Run approaches/equals nominal end; a delayed Order/cancel was decided before it and arrives just before, exactly at, or after it.

### Main flow

1. [OPEN] Required duration, supported lengths, natural/manual end and authority: [GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-EXP-002](../review/gap-register.md#gap-exp-002).
2. [OPEN] Idle progression/stopped actions: [GAP-EXP-003](../review/gap-register.md#gap-exp-003).
3. [OPEN] Exact end inclusion, pending action/observation disposition and initial ties: [GAP-EXP-004](../review/gap-register.md#gap-exp-004)/[GAP-MAT-001](../review/gap-register.md#gap-mat-001).
4. [P] Once effective end/order is supplied, use [BS-EXP-004](experiment.md#bs-exp-004) consequences only.

### Alternative flows

Review alternatives: include end instant or exclude it; drop or report late actions; duration-controlled or explicit end. No alternative adopted.

### Failure / invalid flows

No automatic timer, admin-only end, pause/resume, after-end rejection label, or boundary fill is asserted.

### No-action behavior

Waiting 30 minutes/one hour does not prove an automatic end; no-action duration policy absent.

### Time and boundary behavior

Virtual time is separate from physical waiting/processing duration; a sent-before-end timestamp is not a decided accepted-before-end rule.

### Postconditions and expected state changes

Complete scenario BLOCKED until owner choices are recorded.

### Expected balance / Trade / fee / market-data effects

Trade counts, fees, final depth/candles/reserves/metrics can materially differ; no invented expected ledger.

### Known unresolved questions

[GAP-EXP-001](../review/gap-register.md#gap-exp-001)–[GAP-EXP-004](../review/gap-register.md#gap-exp-004)/[GAP-MAT-001](../review/gap-register.md#gap-mat-001).

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
