# Matching Stp business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-mat-001"></a>

## [BS-MAT-001](matching-stp.md#bs-mat-001) — Better price dominates earlier worse price

Status: APPROVED-DERIVED

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Order, Order Book, Trading Account, Trade price.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-012](../decisions.md#oq-012) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-ORD-005](../05-matching-stp.md#br-ord-005) [ORD-INV-003](../invariants.md#ord-inv-003) [ORD-INV-005](../invariants.md#ord-inv-005)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-012](../decisions.md#oq-012); supporting domain definitions do not approve project refinements |
| Proposed source | None within this narrow normative scope; proposed diagram branches are excluded where stated |
| Not defined by source | [GAP-MAT-001](../review/gap-register.md#gap-mat-001) is excluded by known accepted order. Diagram has provisional extra branches; only its approved price/sequence nodes apply here. |

### Business purpose

Isolate approved selection behavior from proposed financial mechanics.

### Preconditions

Already accepted funded eligible BUY; external earlier Ask at 86,000 and later Ask at 85,000 USDT/BTC; BUY boundary 86,000.

### Trigger

Choose next eligible counterparty.

### Main flow

1. [A] Select 85,000 Ask despite later acceptance.
2. [A] Execute any resulting Trade at its resting 85,000 price.

### Alternative flows

SELL mirror: highest eligible Bid 85,500 precedes earlier 85,000. Equal prices use [BS-MAT-002](matching-stp.md#bs-mat-002).

### Failure / invalid flows

Incompatible symbol/side or price outside boundary is not eligible; invalid/unfunded acceptance is outside this selection-only fixture.

### No-action behavior

No new incoming interest yields no selection/execution; elapsed/client time never overtakes price priority.

### Time and boundary behavior

Existing acceptance sequences known; no rule for unaccepted same scheduled arrivals is inferred.

### Postconditions and expected state changes

Selected next interest and price are unambiguous.

### Expected balance / Trade / fee / market-data effects

This narrow scenario fixes selection/price, not pair granularity, five-state labels, exact settlement or treasury; those complete outcomes are provisional in [BS-TRD-001](balance-settlement.md#bs-trd-001).

### Known unresolved questions

[GAP-MAT-001](../review/gap-register.md#gap-mat-001) is excluded by known accepted order. Diagram has provisional extra branches; only its approved price/sequence nodes apply here.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mat-002"></a>

## [BS-MAT-002](matching-stp.md#bs-mat-002) — Same price: sequence before size or displayed time

Status: PROVISIONAL

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Order, Order Book, Trade.

**Exact trace:** [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [ORD-INV-005](../invariants.md#ord-inv-005) [TRD-INV-003](../invariants.md#trd-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Specify representative queue quantities without approving pair granularity.

### Preconditions

Ask A 0.4 BTC at sequence #1001, Ask B 2 BTC at #1002, both 86,000 USDT/BTC, accepted/funded external; incoming valid BUY 1 BTC.

### Trigger

BUY immediate matching.

### Main flow

1. [A] A has priority regardless of equal displayed timestamps, B larger size or B earlier client time.
2. [P] One Trade A 0.4 BTC, then B 0.6 BTC per [OQ-031](../decisions.md#oq-031).
3. [A/P] B remaining 1.4 BTC retains #1002 and is PARTIALLY_FILLED under lifecycle proposal.

### Alternative flows

A partial earlier order remains ahead of later same-price interest; an earlier better price would take precedence before this level.

### Failure / invalid flows

Do not split each selected pair arbitrarily or assign B priority from size/client timestamps. Validation failures excluded before acceptance.

### No-action behavior

Without another incoming action, B 1.4 remains; time alone does not change accepted sequence.

### Time and boundary behavior

Accepted displayed-time equality is resolved; initial simultaneous arrival generation remains [BS-MAT-004](matching-stp.md#bs-mat-004).

### Postconditions and expected state changes

BUY fully executed; A no active remainder; B 1.4 active with original sequence.

### Expected balance / Trade / fee / market-data effects

Only actual executed quantities enter Trade/fill history; exact fee/reserve settlement covered by Balance/Trade scenarios, not asserted here.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mat-003"></a>

## [BS-MAT-003](matching-stp.md#bs-mat-003) — Multiple levels and partial resting order

Status: PROVISIONAL

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Order, Order Book, Trade, Reservation, Balance.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [TRD-INV-002](../invariants.md#trd-inv-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Show price traversal and deterministic pair sizes.

### Preconditions

Funded incoming BUY LIMIT 2 BTC at 88,000; external Ask A 0.5 BTC at 86,000, B 0.5 at 87,000, C 2 at 88,000; supplied accepted sequence; zero fees.

### Trigger

BUY matching becomes effective.

### Main flow

1. [A] Select prices 86,000, then 87,000, then 88,000.
2. [P] Trade quantities 0.5, 0.5, 1 BTC from minimum-active pair rule.
3. [P] BUY FILLED; C retains 1 BTC and its original sequence.

### Alternative flows

Lower BUY cap stops eligibility; GTC remaining rests at own limit. SELL traverses Bids in decreasing price.

### Failure / invalid flows

No execution at midpoint/incoming price; unsupported/underfunded incoming request excluded by gate.

### No-action behavior

Without subsequent event, C 1 BTC stays active; price level can contain multiple independently owned Orders.

### Time and boundary behavior

Immediate outcome completes before next effective action under proposal; no client time intervention.

### Postconditions and expected state changes

Three actual Trades, 2 BTC purchased; remaining C funded for 1 BTC.

### Expected balance / Trade / fee / market-data effects

Zero-fee notional 43,000 + 43,500 + 88,000 = 174,500 USDT; average 87,250; only actual fills settle, no fee; reference source §22.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-mat-004"></a>

## [BS-MAT-004](matching-stp.md#bs-mat-004) — Identical scheduled arrivals without acceptance order

Status: OPEN / BLOCKED

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Order, Experiment, Trade, Strategy Participant.

**Exact trace:** [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-041](../decisions.md#oq-041) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [ORD-INV-005](../invariants.md#ord-inv-005) [GAP-MAT-001](../review/gap-register.md#gap-mat-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-041](../decisions.md#oq-041) |
| Not defined by source | [GAP-MAT-001](../review/gap-register.md#gap-mat-001); exact end inclusion additionally [GAP-EXP-004](../review/gap-register.md#gap-exp-004). |

### Business purpose

Separate approved accepted-order priority from missing tie policy.

### Preconditions

Two same-price submissions have identical scheduled virtual arrival; no authoritative sequence/effective order yet supplied.

### Trigger

Allocate initial business order; optionally tied cancel/end also exists.

### Main flow

1. [A] Once sequence exists, earlier accepted same-price Order wins; client time/size cannot override.
2. [OPEN] Which tied arrival becomes earlier is [GAP-MAT-001](../review/gap-register.md#gap-mat-001).

### Alternative flows

Different supplied effective orders produce different fills; this demonstrates the ambiguity, not adoption of either order.

### Failure / invalid flows

Do not break tie by arbitrary human owner/name/size/client timestamp or presumed technical arrival.

### No-action behavior

Without allocation, no deterministic comparative result can be asserted.

### Time and boundary behavior

Equal displayed timestamps **with known accepted sequence** are [BS-MAT-002](matching-stp.md#bs-mat-002) and are already resolved.

### Postconditions and expected state changes

Complete tie-allocation outcome BLOCKED.

### Expected balance / Trade / fee / market-data effects

Priority, Trade quantity/price, cancellation results and per-Trade fees may differ; no selected balances.

### Known unresolved questions

[GAP-MAT-001](../review/gap-register.md#gap-mat-001); exact end inclusion additionally [GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Diagram and scenario-specific reading

[DIA-PRECEDENCE](../diagrams/README.md#dia-precedence) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-stp-001"></a>

## [BS-STP-001](matching-stp.md#bs-stp-001) — Own resting Order first; continue externally

Status: PROVISIONAL

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [BR-ORD-006](../05-matching-stp.md#br-ord-006) [TRD-INV-004](../invariants.md#trd-inv-004) [BAL-INV-003](../invariants.md#bal-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Cancel self-owned standing interest rather than skip it.

### Preconditions

Accepted funded A BUY 1 BTC at 86,000; next eligible Ask A-own 0.4 BTC then external B 0.6 at 86,000; full funding validated before STP.

### Trigger

Immediate matching reaches first self-owned Ask.

### Main flow

1. [A] Cancel A-own resting remainder, distinguish SELF_TRADE_PREVENTION; no self-Trade/fee/volume/Last Price.
2. [P] Release that remainder unused reserve; continue to external B.
3. [P] External Trade 0.6; Limit remaining 0.4 rests PARTIALLY_FILLED.

### Alternative flows

Market mirror consumes B 0.6 and ends remaining 0.4 CANCELLED/MARKET_REMAINDER; Market never rests.

### Failure / invalid flows

Underfunded incoming must reject before self-cancellation ([BS-ORD-009](orders.md#bs-ord-009)); out-of-boundary own Order is not reached as eligible.

### No-action behavior

Cancelled own remainder never fills later; incoming GTC remainder can wait without guarantee.

### Time and boundary behavior

Complete immediate outcome precedes next effective action; no initial tie/end assumption.

### Postconditions and expected state changes

Own resting interest gone; only external fill actual; incoming type remainder respected.

### Expected balance / Trade / fee / market-data effects

STP releases unused owned funding, not profit; only B exchange contributes settlement/fee/data.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-STP](../diagrams/README.md#dia-stp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-stp-002"></a>

## [BS-STP-002](matching-stp.md#bs-stp-002) — External fill before own interest and further external fill

Status: PROVISIONAL

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [BR-ORD-006](../05-matching-stp.md#br-ord-006) [TRD-INV-004](../invariants.md#trd-inv-004) [BAL-INV-003](../invariants.md#bal-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Preserve legitimate earlier execution when STP occurs later.

### Preconditions

A incoming BUY 1 BTC cap 88,000; external B Ask 0.4 at 86,000, A-own 0.4 at 87,000, external C 0.2 at 88,000; funded accepted interest.

### Trigger

Process selected eligible counterparts.

### Main flow

1. [A/P] Execute B 0.4 at 86,000.
2. [A] Cancel eligible A-own resting 0.4 at 87,000 by STP, with zero execution effects for prevention.
3. [P] Execute C 0.2 at 88,000; incoming Limit rests remaining 0.4.

### Alternative flows

With no C, only B actual fill remains; with Market incoming, end all remaining quantity immediately.

### Failure / invalid flows

Do not reverse B fill or charge fee on prevented 0.4; validation had to pass before any effects.

### No-action behavior

No later action leaves incoming funded remainder active; cancelled self-interest is gone.

### Time and boundary behavior

All three steps belong to one proposed uninterrupted incoming outcome.

### Postconditions and expected state changes

Filled 0.6, unexecuted/active 0.4 BTC on incoming Limit; no Trade at 87,000.

### Expected balance / Trade / fee / market-data effects

Only notional 34,400 + 17,600 = 52,000 USDT plus actual per-side fees transfers; STP releases unused own reserve.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-stp-003"></a>

## [BS-STP-003](matching-stp.md#bs-stp-003) — Multiple own resting remainders with earlier legitimate history

Status: PROVISIONAL

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [BR-ORD-006](../05-matching-stp.md#br-ord-006) [TRD-INV-004](../invariants.md#trd-inv-004) [BAL-INV-003](../invariants.md#bal-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Apply prevention each time without erasing prior fills.

### Preconditions

Accepted funded incoming A BUY; two next-eligible A-owned Asks, one already partially filled legitimately; later external interest supplied.

### Trigger

Matching reaches each own eligible remainder.

### Main flow

1. [A] Cancel first active self-owned remainder, keeping prior Trade history.
2. [A] Continue and cancel second own remainder when next eligible.
3. [P] Release each unused commitment once; only later external fills affect incoming filled totals.

### Alternative flows

Different accounts with same human are [BS-STP-005](matching-stp.md#bs-stp-005), not grouped; outside-boundary own interests remain ineligible and untouched.

### Failure / invalid flows

No skipped-live self-order, synthetic Trade, duplicate release or rollback of previous legitimate fills.

### No-action behavior

If no external interest follows, [BS-STP-004](matching-stp.md#bs-stp-004) governs incoming type remainder.

### Time and boundary behavior

Previous fills occurred earlier in authoritative order and cannot be rewritten by prevention.

### Postconditions and expected state changes

Each reached own remainder CANCELLED/SELF_TRADE_PREVENTION under proposal; original filled histories retained.

### Expected balance / Trade / fee / market-data effects

Prior costs/fees persist; removed depth adds no traded volume; each unused reserve releases at cancellation.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-STP](../diagrams/README.md#dia-stp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-stp-004"></a>

## [BS-STP-004](matching-stp.md#bs-stp-004) — STP leaves no external eligible liquidity

Status: PROVISIONAL

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [BR-ORD-006](../05-matching-stp.md#br-ord-006) [TRD-INV-004](../invariants.md#trd-inv-004) [BAL-INV-003](../invariants.md#bal-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Combine prevention with zero external execution without mislabelling validation.

### Preconditions

Accepted fully funded incoming Limit or Market; all immediately eligible opposing interest belongs to same account; no external eligible liquidity.

### Trigger

STP consumes the sequence of self-owned resting cancellations.

### Main flow

1. [A] Cancel each eligible own remainder and create no self-Trade.
2. [A/P] Incoming Limit rests with no fills as OPEN; incoming Market ends CANCELLED/MARKET_REMAINDER.
3. [P] Release cancelled resting reserves; for Market release incoming unused reserve too.

### Alternative flows

External interest outside protection does not become eligible just because prevention removed own interest.

### Failure / invalid flows

Cannot treat accepted valid Market as REJECTED or finance it retroactively using STP-released assets.

### No-action behavior

GTC can continue waiting; ended Market cannot fill when external liquidity later appears.

### Time and boundary behavior

Known eligible sequence and effective order; no equal-arrival/end policy supplied.

### Postconditions and expected state changes

Zero incoming fills/Trade volume/fee; original Last Price unchanged; book depth changes by cancellation/resting Limit.

### Expected balance / Trade / fee / market-data effects

Total owned units unchanged without actual Trade; locks redistributed/released as type requires, no STP profit.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-STP](../diagrams/README.md#dia-stp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-stp-005"></a>

## [BS-STP-005](matching-stp.md#bs-stp-005) — Distinct accounts with common human owner

Status: APPROVED-DERIVED

**Actors:** Exchange / Trading Platform and accounts owning incoming/resting interest; common human ownership only where stated.

**Related business objects:** Trading Account, Participant, Order, Order Book.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [BR-ORD-006](../05-matching-stp.md#br-ord-006) [TRD-INV-004](../invariants.md#trd-inv-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011); supporting domain definitions do not approve project refinements |
| Proposed source | None within this narrow normative scope; proposed diagram branches are excluded where stated |
| Not defined by source | [GAP-ACT-001](../review/gap-register.md#gap-act-001) for identity lifecycle; approved core here assumes fixed distinct accounts. |

### Business purpose

Preserve approved account-level identity boundary.

### Preconditions

Different accepted funded trading-account identities A and B share a human owner; opposite same-symbol interest is eligible.

### Trigger

Compare identity at next potential match.

### Main flow

1. [A] Distinct trading accounts are not grouped as self-owned by human ownership.
2. [A] Apply ordinary eligibility/priority/resting-price rules; no same-account STP cancellation occurs solely from common human owner.

### Alternative flows

Same trading account is the prevention branch ([DIA-STP](../diagrams/README.md#dia-stp)).

### Failure / invalid flows

No human-owner grouping, privileged funding, priority bypass or forced Trade. Detailed account reassignment is outside fixture.

### No-action behavior

Without incoming interest no Trade/identity comparison occurs.

### Time and boundary behavior

Known fixed account identities/sequences; changed identity during run is [GAP-ACT-001](../review/gap-register.md#gap-act-001).

### Postconditions and expected state changes

Identity comparison treats pair as different-account interest.

### Expected balance / Trade / fee / market-data effects

This narrow scenario does not assert pair quantity granularity, proposed states or precise settlement/fees. Those depend on separate proposed decisions.

### Known unresolved questions

[GAP-ACT-001](../review/gap-register.md#gap-act-001) for identity lifecycle; approved core here assumes fixed distinct accounts.

### Diagram and scenario-specific reading

[DIA-STP](../diagrams/README.md#dia-stp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
