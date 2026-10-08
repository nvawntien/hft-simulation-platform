# Balance Settlement business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-bal-001"></a>

## [BS-BAL-001](balance-settlement.md#bs-bal-001) — BUY and SELL reserve full fee-supported obligations

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-008](../decisions.md#oq-008)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-008](../decisions.md#oq-008), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Explain what is committed before any trade or STP.

### Preconditions

Valid otherwise-funded intents in running experiment; explicit maker 0.1%, taker 0.2%; input validation passed.

### Trigger

Independently evaluate BUY LIMIT 1 BTC at 80,000, Market BUY 2 BTC cap 90,000, and SELL 2 BTC from owned 3 BTC.

### Main flow

1. [P] Limit BUY reserve 80,160 using larger role rate.
2. [P] Protected Market BUY reserve 180,360 using full quantity/cap/taker rate, not visible Ask cost.
3. [P] SELL reserves 2 BTC, leaving 1 BTC Available, with fees payable from actual proceeds.

### Alternative flows

At zero fees, BUY amounts become 80,000 and 180,000; SELL still reserves only Base. Maker rate greater than taker is [BS-BAL-005](balance-settlement.md#bs-bal-005).

### Failure / invalid flows

Below full Available requirement rejects before any STP/trading; equality passes only funding gate. No downsizing or using other Order Locked funds.

### No-action behavior

With no execution/termination, committed assets stay owned but unavailable for a second commitment.

### Time and boundary behavior

Reservation precedes trading/prevention; later self-cancellation cannot pre-fund this event.

### Postconditions and expected state changes

Each accepted commitment is covered for all requested protected quantity and enabled fees.

### Expected balance / Trade / fee / market-data effects

Available moves to Locked without Total change; reserve/fee rounding follows canonical §12/[OQ-046](../decisions.md#oq-046).

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-RESERVE](../diagrams/README.md#dia-reserve) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-bal-002"></a>

## [BS-BAL-002](balance-settlement.md#bs-bal-002) — Partial BUY/SELL settlement and immediate surplus release

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Reconcile reserve, executed cost and still-owned available capital.

### Preconditions

Original §12 fixtures supplied; BUY initial 100,000 USDT, LIMIT 1 BTC at 80,000, rates maker 0.1%/taker 0.2%; external fill 0.4 at 75,000.

### Trigger

First Trade settles.

### Main flow

1. [P] Consume 30,060 from initial 80,160 reserve.
2. [P] Remaining 0.6 BTC obligation 48,096; release surplus 2,004 immediately.
3. [P] Observe Available 21,844, Locked 48,096, Total 69,940 USDT plus received 0.4 BTC.

### Alternative flows

Zero-fee source fixture: cost 30,000, reserve 48,000, release 2,000, Available 22,000, Total 70,000. SELL source fixture: from 3 BTC reserve 2; sale 0.5 at 86,000 leaves Available 1/Locked 1.5 BTC and net proceeds 42,957 USDT at maker 0.1%.

### Failure / invalid flows

No extra minimum remainder, transfer of unused funding to seller, price-improvement profit attribution or hypothetical refund.

### No-action behavior

Without another fill/cancel/end, computed remainder commitment stays locked.

### Time and boundary behavior

Surplus release after every Trade, not delayed until eventual full fill; immediately received assets/proceeds are Available.

### Postconditions and expected state changes

Ownership and required remainder obligation reconcile; filled amounts remain actual.

### Expected balance / Trade / fee / market-data effects

SELL cancel releases 1.5 BTC to Available 2.5 total; BUY cancel releases remaining 48,096 to Available 69,940; costs/fees stand.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-RESERVE](../diagrams/README.md#dia-reserve) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-bal-003"></a>

## [BS-BAL-003](balance-settlement.md#bs-bal-003) — Release all unused reserve on every sourced termination

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury; Experiment.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-007](../decisions.md#oq-007) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-030](../decisions.md#oq-030) [OQ-035](../decisions.md#oq-035) [OQ-042](../decisions.md#oq-042)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-042](../decisions.md#oq-042), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Cover release branches without refunding executed obligations.

### Preconditions

Known accepted commitment; termination already effective as user cancel, STP, Market remainder, full completion or known experiment end.

### Trigger

That Order no longer has active quantity.

### Main flow

1. [P] Release all unused reservation of the affected Order.
2. [A/P] Keep completed Trades/fees and transferred units.
3. [P] Repeated ended cancellation cannot release again.

### Alternative flows

Market zero fill releases all incoming reserve; partial fill only unused; STP cancels/release resting remainder while incoming continues separately; full fill releases price/fee surplus only.

### Failure / invalid flows

Invalid rejected request has no accepted commitment to refund; experiment trigger/exact end and delayed arrivals are excluded from this supplied-termination fixture.

### No-action behavior

An active Order with no terminating event keeps reserve; terminal unused reservation does not persist.

### Time and boundary behavior

Each effective termination releases once; known earlier fills stand; no auto-unlock timer selected.

### Postconditions and expected state changes

Active remaining zero; no unused reserve for the terminated Order.

### Expected balance / Trade / fee / market-data effects

Total ownership unchanged by release itself; no restored sold Base, spent notional or fee; conservation still holds.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-RESERVE](../diagrams/README.md#dia-reserve) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-bal-004"></a>

## [BS-BAL-004](balance-settlement.md#bs-bal-004) — Reserved USDT cannot fund another market Order

Status: APPROVED-DERIVED

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Balance, Reservation, Order.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-013](../decisions.md#oq-013); supporting domain definitions do not approve project refinements |
| Proposed source | None within this narrow normative scope; proposed diagram branches are excluded where stated |
| Not defined by source | Diagram includes proposed calculations; this approved narrow scenario uses only its ownership/exclusivity concept. |

### Business purpose

Check approved exclusivity across the common Quote asset.

### Preconditions

Owned USDT already committed to an active BTC/USDT Order; another ETH/USDT or SOL/USDT request attempts to count that Locked portion as usable.

### Trigger

Assess capital available for the additional commitment.

### Main flow

1. [A] Committed assets cannot simultaneously fund a second active Order across any symbol.
2. [A] Only genuinely available owned units can fund new obligations; no credit.

### Alternative flows

If a distinct amount is Available and covers the new obligation, exclusivity alone does not block the other Order. Locked Base likewise cannot fund two SELLs.

### Failure / invalid flows

This approved principle does not choose the proposed formula/REJECTED lifecycle/pre-STP ordering; those are in [BS-BAL-001](balance-settlement.md#bs-bal-001)/[BS-ORD-009](orders.md#bs-ord-009).

### No-action behavior

No original fill/cancel/end leaves the commitment exclusive.

### Time and boundary behavior

Cross-market effect follows ownership, not display time, timing configuration or action size.

### Postconditions and expected state changes

The same units are not double committed; existing reserve remains owned.

### Expected balance / Trade / fee / market-data effects

No transfer/fee/Trade occurs from merely assessing exclusivity.

### Known unresolved questions

Diagram includes proposed calculations; this approved narrow scenario uses only its ownership/exclusivity concept.

### Diagram and scenario-specific reading

[DIA-RESERVE](../diagrams/README.md#dia-reserve) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-bal-005"></a>

## [BS-BAL-005](balance-settlement.md#bs-bal-005) — Maker rate exceeds Taker; Limit changes role

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-012](../decisions.md#oq-012)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Demonstrate why Limit must reserve the larger role rate.

### Preconditions

Buyer 100,000 USDT; maker 0.3%, taker 0.1%; BUY LIMIT 1 BTC at 80,000; first eligible Ask 0.4 BTC at 75,000; later incoming SELL supplied for remaining 0.6.

### Trigger

First taker execution then later resting-maker execution.

### Main flow

1. [P] Initial reserve 80,240 uses maker 0.3%.
2. [P] First cost 30,030 (notional 30,000 + taker fee 30); remaining reserve 48,144; release 2,066; Available 21,826.
3. [A/P] Later SELL hits resting BUY at 80,000: BUY Maker fee 144 on 48,000; remaining reserve suffices exactly.

### Alternative flows

Maker lower/equal changes chosen maximum accordingly; zero rates remain explicit allowed settings.

### Failure / invalid flows

Do not reserve only current taker rate or change rates in-run; rate/price display cannot alter obligations.

### No-action behavior

After first fill, no later SELL leaves 0.6 BTC funded at same sequence.

### Time and boundary behavior

Later fill is a separate effective action; old priority retained while active.

### Postconditions and expected state changes

Buyer ends FILLED with 1 BTC, 21,826 USDT Available, no Locked USDT.

### Expected balance / Trade / fee / market-data effects

Buyer fees 174 total; exact costs 30,030 + 48,144 = 78,174. Scenario-specific rates, not defaults.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-ROLES](../diagrams/README.md#dia-roles) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-trd-001"></a>

## [BS-TRD-001](balance-settlement.md#bs-trd-001) — Original two-Trade settlement ledger

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-003](../decisions.md#oq-003) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-005](../decisions.md#oq-005), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Make full source ledger independently reviewable as a business scenario.

### Preconditions

Source §18 fixture: Alice 200,000 USDT, Bob 3 BTC, treasury 0; Bob rests 0.5 BTC then 1.5 BTC both 86,000; fees maker 0.1%/taker 0.2%.

### Trigger

Alice BUY LIMIT 2 BTC at 90,000 matches those accepted sells.

### Main flow

1. [A/P] Resting price 86,000; Alice Taker/Bob Maker on both Trades; pair sizes 0.5 and 1.5 under [OQ-031](../decisions.md#oq-031).
2. [P] Trade 1 notional 43,000, fees buyer 86/seller 43; recompute reservation.
3. [P] Trade 2 notional 129,000, fees buyer 258/seller 129; finish and release surplus.

### Alternative flows

After first Trade, the arithmetic ledger checkpoint is Alice 21,644 Available/135,270 Locked USDT and 0.5 BTC; Bob 42,957 USDT, 1 BTC Available/1.5 Locked; treasury 129. This is a checkpoint within the supplied complete incoming outcome, not authorization for another effective action to interrupt it.

### Failure / invalid flows

An attempted cancellation/end cannot interrupt between these two eligible fills under [OQ-031](../decisions.md#oq-031). Invalid input/full funding failure instead occurs before acceptance. A different fixture with less eligible liquidity must be reviewed separately; it does not change this source ledger.

### No-action behavior

After the supplied complete outcome, no later effective action means no additional Trade/fee. Both resting sells are eligible here; inactivity cannot erase the second fill of this already effective incoming Order.

### Time and boundary behavior

Both Trades in one complete immediate outcome; next effective action cannot interrupt it under proposed [OQ-031](../decisions.md#oq-031).

### Postconditions and expected state changes

Final Alice 27,656 USDT/2 BTC; Bob 171,828 USDT/1 BTC; treasury 516; all these Orders reserves zero.

### Expected balance / Trade / fee / market-data effects

Complete stage-by-stage canonical ledger is source §18; separate once-only/conservation cases [BS-TRD-002](balance-settlement.md#bs-trd-002)/[BS-TRD-003](balance-settlement.md#bs-trd-003).

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-SETTLE](../diagrams/README.md#dia-settle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-trd-002"></a>

## [BS-TRD-002](balance-settlement.md#bs-trd-002) — Report the same settled Trade again

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003) [TRD-INV-003](../invariants.md#trd-inv-003) [MKT-INV-002](../invariants.md#mkt-inv-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Prevent observation from becoming another financial occurrence.

### Preconditions

One actual Trade already settled and included once in each Order filled total; participant balances/treasury/history known.

### Trigger

Same Trade is reported/observed again.

### Main flow

1. [P] Present the existing execution fact, not a new Trade.
2. [P] No second settlement, fee transfer, filled-count increment or reserve release.

### Alternative flows

A genuinely distinct later Trade is a separate occurrence with its own effects; that does not redefine this Trade.

### Failure / invalid flows

No duplicated receipt/presentation can create further obligations; repeated Order submission identity is separately OPEN [GAP-ORD-001](../review/gap-register.md#gap-ord-001).

### No-action behavior

Even without repeated presentation, settled facts persist unchanged.

### Time and boundary behavior

Original occurrence time/order/price/roles remain the same; no new latest occurrence is fabricated.

### Postconditions and expected state changes

All before/after units, filled totals and fee amounts exactly equal.

### Expected balance / Trade / fee / market-data effects

Observed Trade contributes once to ownership and each Order filled quantity; market volume occurrence counting separately [BS-MKT-004](market-data.md#bs-mkt-004).

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-SETTLE](../diagrams/README.md#dia-settle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-trd-003"></a>

## [BS-TRD-003](balance-settlement.md#bs-trd-003) — Conserve every asset including Fee Treasury

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-046](../decisions.md#oq-046) [BR-BAL-001](../06-balance-reservation.md#br-bal-001) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [BAL-INV-001](../invariants.md#bal-inv-001) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [BAL-INV-004](../invariants.md#bal-inv-004) [STL-INV-001](../invariants.md#stl-inv-001) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-003](../decisions.md#oq-003) [SIM-INV-001](../invariants.md#sim-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003), [OQ-005](../decisions.md#oq-005), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Differentiate ownership conservation from indicative valuation.

### Preconditions

[BS-TRD-001](balance-settlement.md#bs-trd-001) fixture and canonical §18 ledger; no in-run funding or unrelated transfers in scope.

### Trigger

Reconcile after each Trade and at final completion.

### Main flow

1. [P] Count Available plus Locked owned units across participants and treasury per asset.
2. [P] First-stage USDT 21,644 + 135,270 + 42,957 + 129 = 200,000.
3. [P] Final USDT 27,656 + 171,828 + 516 = 200,000; BTC 2 + 1 = 3.

### Alternative flows

Reservation/release alone redistributes usability with no owner-unit creation; Simulation Funding is separately labelled capital before the run.

### Failure / invalid flows

A mismatch is an invariant violation to report, not permission to inject funding/correct a Trade. No technical repair mechanism selected.

### No-action behavior

No actual Trade/funding means units do not change from time alone; market valuation may still change separately.

### Time and boundary behavior

Reconcile each intermediate completed Trade, not merely eventual totals.

### Postconditions and expected state changes

All asset totals conserved, fees accounted where their paid units arrive.

### Expected balance / Trade / fee / market-data effects

Treasury is included; ignoring it falsely makes USDT disappear. Portfolio market value is not required to stay constant.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-SETTLE](../diagrams/README.md#dia-settle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-trd-004"></a>

## [BS-TRD-004](balance-settlement.md#bs-trd-004) — Confirmed Trade cannot be exceptionally corrected

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trade, Experiment, Balance, Fee, Fee Treasury.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-035](../decisions.md#oq-035) [OQ-042](../decisions.md#oq-042) [OQ-045](../decisions.md#oq-045) [BR-TRD-001](../07-trade-settlement-fees.md#br-trd-001) [BR-SIM-002](../02-experiment.md#br-sim-002) [TRD-INV-001](../invariants.md#trd-inv-001) [SIM-INV-001](../invariants.md#sim-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-042](../decisions.md#oq-042), [OQ-045](../decisions.md#oq-045) |
| Not defined by source | [GAP-ADM-001](../review/gap-register.md#gap-adm-001)/[GAP-ADM-002](../review/gap-register.md#gap-adm-002)/[GAP-EXP-005](../review/gap-register.md#gap-exp-005) for classification/access; prohibition itself is already proposed source policy. |

### Business purpose

Keep financial facts immutable even for flawed research.

### Preconditions

Confirmed Trade exists; requester wants edit/delete/reverse/exceptional correction.

### Trigger

Evaluate the proposed correction request.

### Main flow

1. [P] No such Trade correction operation is supported in V1.
2. [P] Preserve original execution facts; if run already designated invalid, use [BS-EXP-006](experiment.md#bs-exp-006) without rewriting Trades.

### Alternative flows

A separately funded new experiment can generate different Trades; it does not repair old history.

### Failure / invalid flows

No administrator exception, disguised funding correction, deletion or reversal; validity designation authority is separately OPEN.

### No-action behavior

No correction request leaves facts untouched.

### Time and boundary behavior

Confirmed original occurrence remains immutable; retention/access duration remains [GAP-EXP-005](../review/gap-register.md#gap-exp-005).

### Postconditions and expected state changes

Trade facts and executed ownership/fees remain as originally settled.

### Expected balance / Trade / fee / market-data effects

No altered price, quantity, participating identity, fee or opposite transfer is authorized.

### Known unresolved questions

[GAP-ADM-001](../review/gap-register.md#gap-adm-001)/[GAP-ADM-002](../review/gap-register.md#gap-adm-002)/[GAP-EXP-005](../review/gap-register.md#gap-exp-005) for classification/access; prohibition itself is already proposed source policy.

### Diagram and scenario-specific reading

[DIA-EXP](../diagrams/README.md#dia-exp) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-fee-001"></a>

## [BS-FEE-001](balance-settlement.md#bs-fee-001) — Fee rounding and configured-rate boundaries

Status: PROVISIONAL

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Trading Account, Order, Balance, Reservation, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-019](../decisions.md#oq-019) [OQ-022](../decisions.md#oq-022) [OQ-042](../decisions.md#oq-042) [OQ-046](../decisions.md#oq-046) [BR-FEE-002](../07-trade-settlement-fees.md#br-fee-002) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [STL-INV-002](../invariants.md#stl-inv-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-019](../decisions.md#oq-019), [OQ-022](../decisions.md#oq-022), [OQ-042](../decisions.md#oq-042), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Make tiny charges exact without hidden minimums.

### Preconditions

Valid BTC price/quantity fixtures; explicit maker/taker rates. Trading pairs define actual Trades; arbitrary fragmentation is not introduced.

### Trigger

Calculate per-side Trade fees or validate rates.

### Main flow

1. [P] BTC 0.000001 at 0.01 has exact notional 0.00000001 USDT; 0.01% rate gives 0.000000000001, floored to zero.
2. [P] BTC 0.000001 at 1.00 has notional 0.000001; at 1% fee is exactly 0.00000001, retained.
3. [P] Apply separately per Trade/side, no per-Order aggregation or minimum.

### Alternative flows

Rates 0%, 0.01%, 1% pass; negative, 1.01%, 0.015% fail proposed rate gate. Round derived displays independently, never financial notional.

### Failure / invalid flows

No rebate/discount/token/default/minimum/hidden rounding fee. Fixed rates cannot be altered in-run.

### No-action behavior

No actual Trade means no per-Trade fee; choosing zero setting in fixture does not delete fee capability.

### Time and boundary behavior

Rates fixed at setup; changed rate uses separate experiment. Minimum valid notional follows tick × step, not an invented monetary floor.

### Postconditions and expected state changes

Fees/treasury units equal exact floored charged values; invalid configuration not treated as valid input.

### Expected balance / Trade / fee / market-data effects

Buyer pays on top; seller fee comes from proceeds; per-side zero fee remains a legitimate actual Trade.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-SETTLE](../diagrams/README.md#dia-settle) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-fee-002"></a>

## [BS-FEE-002](balance-settlement.md#bs-fee-002) — Same Limit takes now and makes later

Status: APPROVED-DERIVED

**Actors:** Buyer/seller or committing account, Exchange / Trading Platform; Fee Treasury is a business recipient under the proposal.

**Related business objects:** Order, Order Book, Trade, Maker/Taker role.

**Exact trace:** [OQ-007](../decisions.md#oq-007) [OQ-009](../decisions.md#oq-009) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [BR-FEE-001](../07-trade-settlement-fees.md#br-fee-001) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-005](../05-matching-stp.md#br-ord-005)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-007](../decisions.md#oq-007), [OQ-009](../decisions.md#oq-009), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | None within this narrow normative scope; proposed diagram branches are excluded where stated |
| Not defined by source | No missing decision within the narrow role scope; composed financial outcome remains provisional. |

### Business purpose

Keep approved per-Trade role separate from Order type.

### Preconditions

Fixture actual history: a funded BUY LIMIT originally 2 BTC at 90,000 has a first actual 1 BTC execution against a resting external SELL at 86,000, then its remainder rests and a later eligible SELL produces another actual Trade. These executed facts are supplied, not a rule selecting pair quantity.

### Trigger

BUY consumes Ask, rests remainder, then later SELL hits it.

### Main flow

1. [A] BUY is Taker on execution against resting SELL at 86,000.
2. [A] Its remaining 1 BTC rests at 90,000.
3. [A] Same BUY is Maker on later execution at resting 90,000.

### Alternative flows

Maker/taker rates can equal/differ or be zero; the role remains per execution.

### Failure / invalid flows

Do not classify every Limit as Maker or permanently label one Order Taker for all Trades. Full validation/reservation/state mechanics are outside this narrow role fixture.

### No-action behavior

Without the later SELL, resting BUY has not made another Trade/fee merely by waiting.

### Time and boundary behavior

Later execution follows earlier rest; no retrospective role change for first Trade.

### Postconditions and expected state changes

Each actual execution keeps its own maker/taker attribution and resting price.

### Expected balance / Trade / fee / market-data effects

Exact USDT charges and reservations are proposed policies in [BS-BAL-005](balance-settlement.md#bs-bal-005)/[BS-FEE-001](balance-settlement.md#bs-fee-001); this approved scenario selects no precise fee formula.

### Known unresolved questions

No missing decision within the narrow role scope; composed financial outcome remains provisional.

### Diagram and scenario-specific reading

[DIA-ROLES](../diagrams/README.md#dia-roles) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
