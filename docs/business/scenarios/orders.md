# Orders business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-ord-001"></a>

## [BS-ORD-001](orders.md#bs-ord-001) — Limit with no immediate match

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-030](../decisions.md#oq-030)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Keep unfixed intent funded without inventing a fill.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. Account A has 100,000 USDT; maker 0.1%, taker 0.2%; no eligible Ask at/below 80,000 USDT/BTC.

### Trigger

A submits BUY LIMIT 1 BTC at 80,000 USDT/BTC.

### Main flow

1. [P] Validate full funding and accept/reserve 80,160 USDT.
2. [A] Find no eligible counterparty; retain 1 BTC GTC at 80,000 with acceptance sequence.
3. [P] Observe OPEN with filled 0, unexecuted/active 1 BTC.

### Alternative flows

If an Ask at 80,000 is eligible, apply matching rather than force the no-fill branch; an Ask at 80,000.01 is outside this BUY limit.

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

No later effective action: no Trade/fee; remainder and commitment stay active.

### Time and boundary behavior

No automatic within-run expiry is only proposed [OQ-030](../decisions.md#oq-030); see [BS-ORD-010](orders.md#bs-ord-010). Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

An active unfilled Limit exists; nothing was bought.

### Expected balance / Trade / fee / market-data effects

Available 19,840 USDT, Locked 80,160 USDT, Total 100,000 USDT; no Base/treasury transfer.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-002"></a>

## [BS-ORD-002](orders.md#bs-ord-002) — Limit partial immediate execution

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-028](../decisions.md#oq-028) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Separate executed ownership from still-active intention.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. A starts 100,000 USDT; rates 0.1% maker/0.2% taker; only external Ask 0.4 BTC at 75,000 eligible.

### Trigger

BUY LIMIT 1 BTC at 80,000 becomes effective.

### Main flow

1. [P] Reserve 80,160, accept.
2. [A/P] Select resting Ask at 75,000; execute 0.4 BTC under proposed pair quantity.
3. [P] Settle actual fee/cost and retain 0.6 BTC at 80,000 as PARTIALLY_FILLED with original sequence.

### Alternative flows

Later eligible SELL can fill the resting BUY at 80,000; the incoming seller lower limit is not the execution price. Cancel/end releases only unused reserve.

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

No later counterparty/cancel/end: 0.6 BTC stays active; the 0.4 BTC remains owned.

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

Original 1, filled 0.4, unexecuted/active 0.6 BTC; no guarantee of remaining fill.

### Expected balance / Trade / fee / market-data effects

BUY Trade cost 30,060 USDT; reserve 48,096; release 2,004; Available 21,844, Total 69,940 USDT. Full financial derivation [BS-BAL-002](balance-settlement.md#bs-bal-002).

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-RESERVE](../diagrams/README.md#dia-reserve) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-003"></a>

## [BS-ORD-003](orders.md#bs-ord-003) — Limit immediate full execution

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Show a Limit can be aggressive and finish immediately.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. Buyer has 100,000 USDT; one external resting SELL 1 BTC at 86,000; rates maker 0.1%, taker 0.2%.

### Trigger

BUY LIMIT 1 BTC at 90,000.

### Main flow

1. [P] Reserve 90,180 and accept.
2. [A] Eligible resting price is 86,000; BUY is Taker.
3. [P] Execute/settle 1 BTC and terminate FILLED; release 4,008 surplus.

### Alternative flows

If available eligible quantity is less than 1 BTC, [BS-ORD-002](orders.md#bs-ord-002); if none, [BS-ORD-001](orders.md#bs-ord-001).

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

With both Orders fully executed, later inactivity creates no new financial effect.

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

BUY filled/original 1 BTC; unexecuted/active zero; no resting BUY.

### Expected balance / Trade / fee / market-data effects

Buyer Available 13,828 USDT and 1 BTC; seller receives 85,914 USDT; treasury 258 (buyer fee 172 + seller fee 86). No BUY reserve remains.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-004"></a>

## [BS-ORD-004](orders.md#bs-ord-004) — Protected Market full execution across levels

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-006](../decisions.md#oq-006) [BR-ORD-004](../04-orders.md#br-ord-004)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-006](../decisions.md#oq-006), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Consume multiple eligible prices without using the protection boundary as execution price.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. Buyer 200,000 USDT; Asks 1 BTC at 86,000 then 2 BTC at 87,000; maker 0.1%, taker 0.2%.

### Trigger

Market BUY 2 BTC maximum 90,000.

### Main flow

1. [P] Reserve full 180,360 and accept.
2. [A/P] Execute 1 BTC at each resting level under price priority and pair-quantity proposal.
3. [P] Settle both; observe FILLED and no Market remainder/reserve.

### Alternative flows

Maximum 86,000 allows only first 1 BTC: [BS-ORD-006](orders.md#bs-ord-006). Thin eligible book: [BS-ORD-005](orders.md#bs-ord-005).

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

After immediate outcome, this Market Order never waits for later liquidity.

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

Filled 2 BTC; average executed price 86,500 USDT/BTC before fees; external second Ask retains 1 BTC.

### Expected balance / Trade / fee / market-data effects

Executed notional 173,000, buyer fees 346; buyer available 26,654 USDT; seller fees total 173, treasury 519. No active Market quantity.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-005"></a>

## [BS-ORD-005](orders.md#bs-ord-005) — Market partial because eligible liquidity is insufficient

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-006](../decisions.md#oq-006) [ORD-INV-002](../invariants.md#ord-inv-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-006](../decisions.md#oq-006), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Keep legitimate fills and terminate unavailable quantity.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. Buyer 500,000 USDT; explicit zero fees; only external 3 BTC Ask at 86,000 within maximum 90,000.

### Trigger

Market BUY 5 BTC maximum 90,000.

### Main flow

1. [P] Full protected reserve 450,000 before acceptance despite thin book.
2. [A/P] Execute eligible 3 BTC at 86,000. [P] Recompute the protected 2 BTC remainder reserve as 180,000 USDT and release 12,000 USDT surplus after the Trade.
3. [A/P] End 2 BTC remainder immediately, CANCELLED/MARKET_REMAINDER. [P] Release the remaining 180,000 USDT commitment; total unused release is 192,000 USDT.

### Alternative flows

Zero eligible quantity: [BS-ORD-007](orders.md#bs-ord-007); all 5 eligible: full Market; adverse outside-boundary quantity: [BS-ORD-006](orders.md#bs-ord-006).

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

Subsequent 2 BTC liquidity cannot fill this ended Market.

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

Original 5, filled 3, unexecuted 2, active zero BTC; prior purchase stands.

### Expected balance / Trade / fee / market-data effects

Buyer spends 258,000 USDT, ends with 242,000 USDT available and 3 BTC; treasury zero; no Market book interest.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-006"></a>

## [BS-ORD-006](orders.md#bs-ord-006) — Market stops at price protection

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-006](../decisions.md#oq-006)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-006](../decisions.md#oq-006), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Distinguish available depth from eligible depth.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. Zero fees; buyer 200,000 USDT; Asks 1 BTC at 86,000 and 1 BTC at 87,000.

### Trigger

Market BUY 2 BTC maximum 86,000.

### Main flow

1. [P] Reserve 172,000 and accept.
2. [A] Execute eligible 1 BTC at exactly 86,000; do not execute 87,000.
3. [P] End remaining 1 BTC as CANCELLED/MARKET_REMAINDER; release 86,000.

### Alternative flows

SELL mirror: minimum 80,000; eligible Bids 82,000 and exactly 80,000, never 79,000. Unit protection excludes fees, does not guarantee full quantity.

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

Later improved prices cannot reactivate the Market Order.

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

Filled 1, unexecuted 1, active zero BTC; external 87,000 Ask unchanged.

### Expected balance / Trade / fee / market-data effects

Buyer 114,000 USDT available, 1 BTC owned, no reserve; no execution effects for out-of-boundary quantity.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-007"></a>

## [BS-ORD-007](orders.md#bs-ord-007) — Valid Market with zero eligible liquidity

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-006](../decisions.md#oq-006)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-006](../decisions.md#oq-006), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Avoid mistaking an accepted zero result for validation failure.

### Preconditions

Running experiment assumed; request valid, full protected funding available, external accounts distinct; effective action order known. Example rates are explicit fixture inputs, not defaults. No eligible external or self-owned opposing interest remains; protection and capital are valid.

### Trigger

Protected Market reaches immediate matching.

### Main flow

1. [P] Validate, reserve and accept.
2. [A] Zero eligible interest yields zero Trades.
3. [P] CANCELLED/MARKET_REMAINDER, filled 0, active 0; release all unused reserve.

### Alternative flows

If self-owned eligible interest exists, apply STP before the zero-external remainder outcome: [BS-STP-004](matching-stp.md#bs-stp-004). If invalid funding, [BS-ORD-009](orders.md#bs-ord-009).

### Failure / invalid flows

Invalid price/quantity/type/protection uses [BS-ORD-008](orders.md#bs-ord-008); insufficient available funds uses [BS-ORD-009](orders.md#bs-ord-009). Those requests reject before acceptance or STP under proposed [OQ-021](../decisions.md#oq-021)/[OQ-027](../decisions.md#oq-027).

### No-action behavior

Later book liquidity cannot fill the ended Market; no execution price or fee exists.

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

Unexecuted equals original; no accepted resting interest or transferred Base.

### Expected balance / Trade / fee / market-data effects

Total balances unchanged; temporary commitment entirely released; fee/Trade/volume increments zero.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-008"></a>

## [BS-ORD-008](orders.md#bs-ord-008) — Invalid request and exact numeric boundaries

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-001](../decisions.md#oq-001) [OQ-004](../decisions.md#oq-004) [OQ-006](../decisions.md#oq-006) [OQ-008](../decisions.md#oq-008) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-019](../decisions.md#oq-019) [OQ-020](../decisions.md#oq-020) [OQ-021](../decisions.md#oq-021) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-046](../decisions.md#oq-046) [BR-ORD-004](../04-orders.md#br-ord-004) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [BR-ORD-003](../04-orders.md#br-ord-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-001](../decisions.md#oq-001), [OQ-004](../decisions.md#oq-004), [OQ-006](../decisions.md#oq-006), [OQ-008](../decisions.md#oq-008); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-019](../decisions.md#oq-019), [OQ-020](../decisions.md#oq-020), [OQ-021](../decisions.md#oq-021), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Make validation testable without rounding requests or inferring notional thresholds.

### Preconditions

Running; sufficient funds for otherwise valid fixtures. Only one gate varies at a time.

### Trigger

Participant submits price/type/symbol/quantity/protection under review.

### Main flow

1. [A] Unsupported symbol/type/Quote-budget or absent required absolute protection cannot authorize V1 execution.
2. [P] Off-grid/out-of-range/nonpositive/above-max values reject before trading; no silent changes.
3. [P] Exact valid boundary passes its gate, with other gates still required.

### Alternative flows

Prices 0.01 and 1,000,000.00 pass; 0, 1,000,000.01 and 86,000.001 fail. BTC 0.000001/100, ETH 0.00001/1,000, SOL 0.001/100,000 pass size gates; zero, negatives, half-step, or maximum plus one step fail. One-step fill/remainder needs no additional minimum.

### Failure / invalid flows

Rejected request creates no Trade or STP effect; no acceptance sequence is claimed for a rejected request. Priority of multiple reported invalid reasons is not specified or invented.

### No-action behavior

No corrected new intent means no valid Order appears.

### Time and boundary behavior

Price bounds inclusive, quantities positive step multiples; no new min/max notional. Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

Each valid fixture passes only the gate tested; invalid request REJECTED.

### Expected balance / Trade / fee / market-data effects

No accepted commitment/execution from invalid request; existing balances/Orders stay as before.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-VALID](../diagrams/README.md#dia-valid) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-009"></a>

## [BS-ORD-009](orders.md#bs-ord-009) — Insufficient funding before any STP

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-004](../decisions.md#oq-004) [OQ-005](../decisions.md#oq-005) [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-046](../decisions.md#oq-046) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-BAL-002](../06-balance-reservation.md#br-bal-002) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-STL-001](../07-trade-settlement-fees.md#br-stl-001) [ORD-INV-003](../invariants.md#ord-inv-003) [BAL-INV-001](../invariants.md#bal-inv-001) [TRD-INV-003](../invariants.md#trd-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-006](../decisions.md#oq-006)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-004](../decisions.md#oq-004), [OQ-005](../decisions.md#oq-005), [OQ-006](../decisions.md#oq-006), [OQ-007](../decisions.md#oq-007), [OQ-008](../decisions.md#oq-008), [OQ-009](../decisions.md#oq-009), [OQ-010](../decisions.md#oq-010), [OQ-011](../decisions.md#oq-011), [OQ-012](../decisions.md#oq-012), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Prevent future self-cancellation from financing incoming intent.

### Preconditions

Running; A available 180,359 USDT, own eligible resting SELL with reserved BTC; new BUY otherwise valid; taker 0.2%.

### Trigger

A submits Market BUY 2 BTC maximum 90,000.

### Main flow

1. [P] Required full reserve is 180,360 USDT.
2. [P] Reject before acceptance/trading/STP; do not resize to available money or visible book.

### Alternative flows

At exactly 180,360, funding gate passes and only then ordinary STP/matching can occur; other validations still apply.

### Failure / invalid flows

No self-resting cancellation, fill or new accepted priority; existing self-owned reservation remains unchanged.

### No-action behavior

Existing Order can remain active; repeated underfunded intent remains independently subject to gates, with duplicate-intent semantics [GAP-ORD-001](../review/gap-register.md#gap-ord-001).

### Time and boundary behavior

Within running experiment, away from end; inclusive execution boundary applies. Known acceptance sequence governs priority; initial scheduled ties/end arrivals are excluded and remain [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Postconditions and expected state changes

New request REJECTED; active existing self-owned interest unchanged.

### Expected balance / Trade / fee / market-data effects

No financial/Trade/fee/volume effect; no reliance on future Base or Quote release.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-VALID](../diagrams/README.md#dia-valid) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-010"></a>

## [BS-ORD-010](orders.md#bs-ord-010) — GTC with no later business actions

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Order, Reservation, Balance, Experiment, Order Book.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-010](../decisions.md#oq-010) [OQ-012](../decisions.md#oq-012) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-030](../decisions.md#oq-030) [OQ-035](../decisions.md#oq-035) [BR-ORD-002](../04-orders.md#br-ord-002) [BR-ORD-007](../04-orders.md#br-ord-007) [ORD-INV-005](../invariants.md#ord-inv-005) [BAL-INV-001](../invariants.md#bal-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-010](../decisions.md#oq-010), [OQ-012](../decisions.md#oq-012); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-030](../decisions.md#oq-030), [OQ-035](../decisions.md#oq-035) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Expose no-action persistence without choosing clock progress.

### Preconditions

An accepted funded OPEN or PARTIALLY_FILLED Limit is resting during a running interval; no fill/cancel/STP/end events occur.

### Trigger

Inspect Order before and after a supplied within-run virtual-time interval.

### Main flow

1. [A] Limit GTC interest has no guaranteed future fill and retains original acceptance sequence.
2. [P] No automatic expiry/lifetime cap is applied during this interval; state/active quantity/reserve stay as they were.

### Alternative flows

Another effective counterparty/cancel/STP/end would leave this fixture; use its respective flow.

### Failure / invalid flows

Do not label EXPIRED or assume a Trade because time elapsed; no late action is authorized by this scenario.

### No-action behavior

This is the explicit no-action case: Filled, Trade history, owned units and required commitment unchanged.

### Time and boundary behavior

Time interval is supplied as a fixture; whether idle time progresses is [BS-EXP-003](experiment.md#bs-exp-003)/[GAP-EXP-003](../review/gap-register.md#gap-exp-003). End-trigger/duration is excluded and blocked separately.

### Postconditions and expected state changes

Same active interest and original sequence remain.

### Expected balance / Trade / fee / market-data effects

No fill/fee/transfer; assets stay reserved and cannot fund another Order.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-ORD](../diagrams/README.md#dia-ord) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-011"></a>

## [BS-ORD-011](orders.md#bs-ord-011) — Cancel own OPEN remainder

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

End untouched active intent once.

### Preconditions

Own OPEN Limit; no fills; full unused reserve; cancel effective before any next counterparty action.

### Trigger

Own cancellation becomes effective.

### Main flow

1. [P] Verify own active remainder under source §14.
2. [P] End active quantity, CANCELLED/USER_REQUEST.
3. [P] Release all unused reserve; filled remains zero.

### Alternative flows

If a fill precedes effectiveness, [BS-ORD-012](orders.md#bs-ord-012) or [BS-ORD-013](orders.md#bs-ord-013); if repeated cancellation, [BS-ORD-014](orders.md#bs-ord-014).

### Failure / invalid flows

Foreign target uses [BS-ORD-016](orders.md#bs-ord-016); absent/rejected [BS-ORD-015](orders.md#bs-ord-015). Request alone is not cancellation success.

### No-action behavior

Without a cancellation or another effective event, the Order remains active.

### Time and boundary behavior

Known effective order only; no scheduled tie/end policy chosen.

### Postconditions and expected state changes

Original quantity unchanged; unexecuted equals original; active zero.

### Expected balance / Trade / fee / market-data effects

All this Order unused locked capital becomes Available; Total unchanged; no Trade/fee generated.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANCEL](../diagrams/README.md#dia-cancel) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-012"></a>

## [BS-ORD-012](orders.md#bs-ord-012) — Cancel own PARTIALLY_FILLED remainder

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-022](../decisions.md#oq-022) [OQ-024](../decisions.md#oq-024) [OQ-046](../decisions.md#oq-046)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Preserve purchase and paid fee after cancellation.

### Preconditions

[BS-ORD-002](orders.md#bs-ord-002) outcome: filled 0.4 BTC, active 0.6, reserve 48,096 USDT; original 1 BTC; Available 21,844.

### Trigger

Own cancel becomes effective before a further fill.

### Main flow

1. [P] End 0.6 BTC as CANCELLED/USER_REQUEST.
2. [P] Release 48,096 USDT once; preserve filled 0.4 and its 60 USDT fee.

### Alternative flows

If another partial fill precedes cancel, stop only smaller remaining quantity; full earlier fill gives too late.

### Failure / invalid flows

Request alone never reverses the 30,000 USDT notional or 60 fee. Terminal/foreign targets use the cancellation table.

### No-action behavior

If no cancel becomes effective and no other event occurs, the resting remainder remains funded.

### Time and boundary behavior

Effective order known; cancellation cannot interrupt an incoming Order immediate outcome under [OQ-031](../decisions.md#oq-031).

### Postconditions and expected state changes

Filled 0.4, unexecuted 0.6, active 0 BTC; same original/history.

### Expected balance / Trade / fee / market-data effects

Buyer Available 69,940 USDT, Locked 0 and 0.4 BTC received; no second fee or refund.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANCEL](../diagrams/README.md#dia-cancel) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-013"></a>

## [BS-ORD-013](orders.md#bs-ord-013) — Cancel FILLED Order is too late

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Keep completed ownership despite a later cancellation.

### Preconditions

Own Order FILLED after full actual execution; no active quantity/reserve.

### Trigger

Later cancellation decision.

### Main flow

1. [P] Return too late/already filled.
2. [P] Keep FILLED, all filled totals, Trades/fees and balances unchanged.

### Alternative flows

If only partial execution occurred, [BS-ORD-012](orders.md#bs-ord-012), not this branch.

### Failure / invalid flows

No reactivation or refund; a cancel sent earlier physically but effective later does not override prior fill.

### No-action behavior

Completed history remains when nothing further happens.

### Time and boundary behavior

Effective execution precedes cancel; client sent time is not precedence. End eligibility is outside fixture.

### Postconditions and expected state changes

Terminal full execution remains final.

### Expected balance / Trade / fee / market-data effects

No release because no unused commitment remains; no duplicate settlement or fee change.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PRECEDENCE](../diagrams/README.md#dia-precedence) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-014"></a>

## [BS-ORD-014](orders.md#bs-ord-014) — Cancel already CANCELLED Order

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Define repeated cancellation without another release.

### Preconditions

Own CANCELLED Order, possibly containing valid prior fills; unused reserve already released.

### Trigger

Participant requests cancellation again.

### Main flow

1. [P] Report already ended.
2. [P] Keep terminal state/reason/history and no further financial effect.

### Alternative flows

Previous termination may be user cancellation, STP or Market remainder; there is still no active remainder. Post-end request eligibility itself remains [GAP-EXP-004](../review/gap-register.md#gap-exp-004).

### Failure / invalid flows

No repeated release or restoration; request repetition is not a new Order.

### No-action behavior

No events means no changes to terminal history.

### Time and boundary behavior

Assume request is evaluated within allowed running context, not unspecified post-end arrival.

### Postconditions and expected state changes

Same CANCELLED outcome and original/fill accounting.

### Expected balance / Trade / fee / market-data effects

Available/Locked/Trade/fee/treasury exactly unchanged.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANCEL](../diagrams/README.md#dia-cancel) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-015"></a>

## [BS-ORD-015](orders.md#bs-ord-015) — Cancel REJECTED or nonexistent target

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Do not fabricate an active Order from a cancel target.

### Preconditions

Own rejected request, or target does not identify an existing active Order; effective decision can be evaluated.

### Trigger

Cancellation request.

### Main flow

1. [P] Determine there is no active Order to cancel.
2. [P] Make no active-quantity or financial change.

### Alternative flows

An existing active owned Limit uses [BS-ORD-011](orders.md#bs-ord-011)/[BS-ORD-012](orders.md#bs-ord-012).

### Failure / invalid flows

Do not invent successful cancellation, a fresh CANCELLED Order, reserve release or historical Trade. Exact UI/error wording is outside this business outcome.

### No-action behavior

No action changes none of the facts.

### Time and boundary behavior

No end/scheduled-tie policy is supplied.

### Postconditions and expected state changes

Rejected request remains REJECTED; nonexistent target remains without an Order.

### Expected balance / Trade / fee / market-data effects

No reserve/Trade/fee/volume effect.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANCEL](../diagrams/README.md#dia-cancel) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-016"></a>

## [BS-ORD-016](orders.md#bs-ord-016) — Cancel another account Order

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-037](../decisions.md#oq-037)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-031](../decisions.md#oq-031), [OQ-037](../decisions.md#oq-037) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Enforce own-account intent ownership without exposing private histories.

### Preconditions

Participant A targets active Order of account B; no authorized B cancellation occurs in fixture.

### Trigger

A requests cancellation of B target.

### Main flow

1. [P] Source §14 own-account restriction prevents this cancellation.
2. [P] Leave B target, reserve and prior facts unchanged by A request.

### Alternative flows

B can cancel its own active remainder in a separate action; administrative intervention entitlement is [GAP-ADM-002](../review/gap-register.md#gap-adm-002), not assumed here.

### Failure / invalid flows

No target termination or private-history disclosure is authorized; exact outward error phrasing is not chosen.

### No-action behavior

B Order can still match normally if other permitted actions occur; in this fixture none occurs.

### Time and boundary behavior

Known request evaluation; neither human co-ownership nor earlier client timestamp grants target authority.

### Postconditions and expected state changes

B interest retains state and priority.

### Expected balance / Trade / fee / market-data effects

A request alone changes neither account balances/Trades/fees.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-CANCEL](../diagrams/README.md#dia-cancel) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-017"></a>

## [BS-ORD-017](orders.md#bs-ord-017) — Known effective cancel/fill order and independent replacement

Status: PROVISIONAL

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Participant, Trading Account, Order, Reservation, Balance, Trade history, Fee history.

**Exact trace:** [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [OQ-031](../decisions.md#oq-031) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-ORD-007](../04-orders.md#br-ord-007) [BR-BAL-003](../06-balance-reservation.md#br-bal-003) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-002](../invariants.md#bal-inv-002) [BAL-INV-003](../invariants.md#bal-inv-003) [STL-INV-003](../invariants.md#stl-inv-003) [OQ-010](../decisions.md#oq-010) [OQ-021](../decisions.md#oq-021) [OQ-029](../decisions.md#oq-029)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-021](../decisions.md#oq-021), [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-029](../decisions.md#oq-029), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Explain precedence and replacement risk without deciding arrival ties.

### Preconditions

An owned active Limit and a counterparty Order/cancel/new intent have an explicitly supplied effective order.

### Trigger

Process either fill-before-cancel or cancel-before-counterparty, then independently submit replacement.

### Main flow

1. [P] Earlier incoming Order completes all immediate outcomes before next action.
2. [P] Earlier fill stands, cancel stops remaining quantity or is too late; earlier cancel prevents later execution of that remainder.
3. [P] After successful cancel, validate/fund new independent Order; [A] new accepted sequence gives fresh priority.

### Alternative flows

Rejected replacement leaves old remainder ended; same price does not retain old sequence. If initial cancel is too late, no successful old remainder cancellation is claimed.

### Failure / invalid flows

No combined amendment/replace, no history reversal or old-order restoration from new failure.

### No-action behavior

Absent new submission, effectively cancelled old interest remains ended.

### Time and boundary behavior

Effective order is given; equal scheduled arrival/end allocation is [BS-MAT-004](matching-stp.md#bs-mat-004)/[BS-EXP-008](experiment.md#bs-exp-008), not silently resolved.

### Postconditions and expected state changes

Every terminal old remainder stays terminal; a valid new Order is a separate commitment.

### Expected balance / Trade / fee / market-data effects

Prior Trade/fee costs stay; release unused old funds only once; new request must use then-Available capital.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PRECEDENCE](../diagrams/README.md#dia-precedence) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-018"></a>

## [BS-ORD-018](orders.md#bs-ord-018) — Repeated submission with uncertain prior acceptance

Status: OPEN / BLOCKED

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Trading Account, Order, Reservation, Balance, Order Book, Trade, Fee, Fee Treasury.

**Exact trace:** [OQ-005](../decisions.md#oq-005) [OQ-010](../decisions.md#oq-010) [OQ-021](../decisions.md#oq-021) [OQ-031](../decisions.md#oq-031) [BR-BAL-004](../06-balance-reservation.md#br-bal-004) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [GAP-ORD-001](../review/gap-register.md#gap-ord-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-005](../decisions.md#oq-005), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-021](../decisions.md#oq-021), [OQ-031](../decisions.md#oq-031) |
| Not defined by source | [GAP-ORD-001](../review/gap-register.md#gap-ord-001); repeated cancellation is already defined and not substituted for repeated submission. |

### Business purpose

Block duplicate execution assumptions before a policy exists.

### Preconditions

Participant does not know whether its original submission was accepted; no source-defined way to classify the repeated intent is given.

### Trigger

Same symbol/side/quantity/price is submitted again.

### Main flow

1. [OPEN] Determine whether this is a new intent or repetition of the same submission: UNDECIDED — [GAP-ORD-001](../review/gap-register.md#gap-ord-001).
2. [A/P] If independently known as new intent, ordinary funding/acceptance priority applies; that does not decide this ambiguous case.

### Alternative flows

Discussion only: reuse original outcome versus treat independently identified second intent. Neither is adopted.

### Failure / invalid flows

Cannot assert one versus two Orders, acceptance priorities, Trades or reservations; no duplicate protection mechanism invented.

### No-action behavior

Re-observing a known Trade does not settle again; that invariant is distinct from resubmitting unknown intent.

### Time and boundary behavior

Participant timestamp equality does not itself identify a duplicate.

### Postconditions and expected state changes

Complete result blocked; obtain owner decision before assertions.

### Expected balance / Trade / fee / market-data effects

One or two funded commitments/executions can materially differ; no expected numeric balances supplied.

### Known unresolved questions

[GAP-ORD-001](../review/gap-register.md#gap-ord-001); repeated cancellation is already defined and not substituted for repeated submission.

### Diagram and scenario-specific reading

[DIA-VALID](../diagrams/README.md#dia-valid) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-ord-019"></a>

## [BS-ORD-019](orders.md#bs-ord-019) — Rejected invalid quantity and every-order invariant scope

Status: OPEN / BLOCKED

**Actors:** Order-owning/submitting participant, Exchange / Trading Platform, and external or self-owned counterparties where stated. Strategy participation retains its own proposed policy.

**Related business objects:** Order / rejected request, submitted intent, quantity facts.

**Exact trace:** [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-019](../decisions.md#oq-019) [OQ-020](../decisions.md#oq-020) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [BR-ORD-003](../04-orders.md#br-ord-003) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002) [GAP-ORD-002](../review/gap-register.md#gap-ord-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-019](../decisions.md#oq-019), [OQ-020](../decisions.md#oq-020), [OQ-025](../decisions.md#oq-025), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028) |
| Not defined by source | [GAP-ORD-002](../review/gap-register.md#gap-ord-002); preserved Business and downstream FR-ORD-016 need human reconciliation. |

### Business purpose

Surface quantity-history applicability instead of changing submitted input.

### Preconditions

Request quantity is negative, missing or not interpretable as valid Base; no trading acceptance or execution occurs.

### Trigger

Inspect quantity facts of a REJECTED request.

### Main flow

1. [P] Invalid numeric input rejects before trading with no rounding/resizing.
2. [P] REJECTED terminal outcome is defined.
3. [OPEN] Original/filled/unexecuted quantity facts and scope of 0 ≤ filled ≤ original for this invalid request are [GAP-ORD-002](../review/gap-register.md#gap-ord-002).

### Alternative flows

A valid positive request rejected only for insufficient funds has an interpretable original quantity; that does not decide malformed/negative quantity facts.

### Failure / invalid flows

Do not invent original zero, replace negative input, claim invariant compliance for undefined values, or change canonical [ORD-INV-001](../invariants.md#ord-inv-001)/002.

### No-action behavior

No acceptance means no Trade/reservation; repeated history inspection does not repair the semantic ambiguity.

### Time and boundary behavior

Rejection occurs before acceptance; no acceptance sequence or valid trading lifetime inferred.

### Postconditions and expected state changes

Complete invalid-request quantity/history result BLOCKED; rejection/no-execution subflow remains known proposed behavior.

### Expected balance / Trade / fee / market-data effects

No transfers/fees arise; reported quantity/state/research rejection history could materially differ by interpretation.

### Known unresolved questions

[GAP-ORD-002](../review/gap-register.md#gap-ord-002); preserved Business and downstream FR-ORD-016 need human reconciliation.

### Diagram and scenario-specific reading

[DIA-VALID](../diagrams/README.md#dia-valid) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
