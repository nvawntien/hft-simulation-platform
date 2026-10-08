# Portfolio business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-pf-001"></a>

## [BS-PF-001](portfolio.md#bs-pf-001) — Weighted cost and realized/unrealized PnL without fees

Status: PROVISIONAL

**Actors:** Account-owning participant and Exchange / Trading Platform; other accounts may establish market prices.

**Related business objects:** Trading Account, Balance, Portfolio Position, Trade, Fee, Last Price observation.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-024](../decisions.md#oq-024) [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-046](../decisions.md#oq-046) [BR-PF-001](../09-portfolio-pnl.md#br-pf-001) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [BAL-INV-002](../invariants.md#bal-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-024](../decisions.md#oq-024), [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Reproduce canonical cost example, keeping valuation distinct from execution.

### Preconditions

Explicit zero fees; actual purchases 1 BTC at 80,000 then 1 at 90,000; enough funds; supplied later Last Price 87,000.

### Trigger

Sell actual 0.5 BTC at 88,000, then value remaining holdings.

### Main flow

1. [P] Basis 170,000, average cost 85,000 USDT/BTC.
2. [P] Remove proportional basis 42,500; sale proceeds 44,000; realized 1,500.
3. [P] Remaining 1.5 BTC basis 127,500; mark 130,500; unrealized 3,000.

### Alternative flows

If no Last Price, unrealized/marked value unavailable; if full sale, remove all remaining basis.

### Failure / invalid flows

No FIFO/tax-lot substitution, fee double subtraction, setup-cost fallback or hypothetical future sale fee.

### No-action behavior

Without own Trade, basis/realized stay as known; other actual Trades can change indicative valuation.

### Time and boundary behavior

Every price is actual execution or supplied Last Price, not an assumed simultaneous quote.

### Postconditions and expected state changes

Position and known performance reconcile to weighted cost.

### Expected balance / Trade / fee / market-data effects

Locked BTC still included in 1.5 held quantity; reserve/release alone changes no basis/PnL.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PF](../diagrams/README.md#dia-pf) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-pf-002"></a>

## [BS-PF-002](portfolio.md#bs-pf-002) — Fee-inclusive weighted cost without double deduction

Status: PROVISIONAL

**Actors:** Account-owning participant and Exchange / Trading Platform; other accounts may establish market prices.

**Related business objects:** Trading Account, Balance, Portfolio Position, Trade, Fee, Last Price observation.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-024](../decisions.md#oq-024) [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-046](../decisions.md#oq-046) [BR-PF-001](../09-portfolio-pnl.md#br-pf-001) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [BAL-INV-002](../invariants.md#bal-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-024](../decisions.md#oq-024), [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Show fees reduce execution results exactly once.

### Preconditions

Same source purchases/sale as [BS-PF-001](portfolio.md#bs-pf-001); all three executions Taker at explicit 0.2%; source Last Price 87,000 supplied.

### Trigger

Compute basis and PnL after actual sale.

### Main flow

1. [P] Purchases add 80,160 + 90,180 = 170,340 basis; average 85,170.
2. [P] Sale 44,000 notional minus 88 fee = 43,912; remove 42,585 basis; realized 1,327.
3. [P] Remaining basis 127,755; marked value 130,500; unrealized 2,745.

### Alternative flows

Maker/Taker role can vary per actual Trade; replace fixture rates only in a separately configured comparison.

### Failure / invalid flows

Do not subtract recorded buy/sell fees again from already fee-inclusive cost/net proceeds; unrealized has no hypothetical sale fee.

### No-action behavior

No new own Trade changes neither assigned basis nor realized amount; observed price changes only indicative portion.

### Time and boundary behavior

Actual roles/rates supplied per Trade; fixed experiment rates not changed mid-run.

### Postconditions and expected state changes

Performance reconciles with net executed costs and holdings.

### Expected balance / Trade / fee / market-data effects

Position 1.5 BTC remains; fees 160 + 180 + 88 = 428 USDT are separately reportable without double counting.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PF](../diagrams/README.md#dia-pf) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-pf-003"></a>

## [BS-PF-003](portfolio.md#bs-pf-003) — Seeded Base with unavailable Last Price

Status: PROVISIONAL

**Actors:** Account-owning participant and Exchange / Trading Platform; other accounts may establish market prices.

**Related business objects:** Trading Account, Balance, Portfolio Position, Trade, Fee, Last Price observation; Scenario.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-024](../decisions.md#oq-024) [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-046](../decisions.md#oq-046) [BR-PF-001](../09-portfolio-pnl.md#br-pf-001) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [BAL-INV-002](../invariants.md#bal-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-024](../decisions.md#oq-024), [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Keep funding cost reference separate from indicative market price.

### Preconditions

Seeded 1 BTC at explicit valid acquisition reference 80,000 USDT/BTC; no actual Trade yet; own seeded USDT separately identified.

### Trigger

Report holdings/performance.

### Main flow

1. [A/P] Funding establishes 1 BTC capital with basis 80,000; no Trade/profit/Last Price.
2. [P] Nonzero Base lacks market valuation/unrealized; total equity unavailable.
3. [P] Known balances and known realized amounts remain reportable.

### Alternative flows

After a genuine first Trade establishes Last Price, mark holdings at it; reference cost remains assigned basis, never guaranteed liquidation price.

### Failure / invalid flows

No invented zero market value or acquisition-reference valuation fallback; invalid reference/grid is not silently replaced.

### No-action behavior

No Trade keeps price absence; capital ownership remains known.

### Time and boundary behavior

Funding happens at proposed setup; repeated observation does not make the reference a more recent market execution.

### Postconditions and expected state changes

Cost and funding are identifiable while indicative market valuation is unavailable.

### Expected balance / Trade / fee / market-data effects

No trading fee/volume/PnL from seeding; locked holdings if later reserved remain part of owned position.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PF](../diagrams/README.md#dia-pf) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-pf-004"></a>

## [BS-PF-004](portfolio.md#bs-pf-004) — Retain proportional rounding residual; full sale removes all

Status: PROVISIONAL

**Actors:** Account-owning participant and Exchange / Trading Platform; other accounts may establish market prices.

**Related business objects:** Trading Account, Balance, Portfolio Position, Trade, Fee, Last Price observation.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-024](../decisions.md#oq-024) [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-046](../decisions.md#oq-046) [BR-PF-001](../09-portfolio-pnl.md#br-pf-001) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [BAL-INV-002](../invariants.md#bal-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-024](../decisions.md#oq-024), [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Exercise a meaningful financial precision boundary.

### Preconditions

Explicit zero fees; actual BUY 1 BTC at 0.01 and 2 BTC at 0.02 USDT/BTC; position 3 BTC with 0.05 USDT basis; each input valid on proposed grid.

### Trigger

SELL 1 BTC at 0.02, then SELL remaining 2 BTC at 0.02.

### Main flow

1. [P] First removed basis floor8(0.05 / 3) = 0.01666666 USDT.
2. [P] Remaining 2 BTC basis 0.03333334; first realized 0.00333334.
3. [P] Full final sale removes **all** 0.03333334; final realized 0.00666666; cumulative 0.01 USDT.

### Alternative flows

Average cost display rounds half-up to eight; that displayed number is not used to discard remaining exact basis.

### Failure / invalid flows

No extra minimum notional, lost residual, repeated fee subtraction or nonzero basis after full liquidation.

### No-action behavior

Between sells with no Trade, quantity/basis remain 2 BTC/0.03333334.

### Time and boundary behavior

Prices are tiny simulated teaching examples, not real quotes; orders remain ordinary positive quantities.

### Postconditions and expected state changes

Quantity zero, basis zero, average cost unavailable; cumulative realized equals total 0.06 proceeds minus 0.05 cost.

### Expected balance / Trade / fee / market-data effects

Exact 8-decimal residual is conserved in remaining cost until full sale; asset units unaffected by display rounding.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PF](../diagrams/README.md#dia-pf) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-pf-005"></a>

## [BS-PF-005](portfolio.md#bs-pf-005) — No own action, other Trades and 60-second price age

Status: PROVISIONAL

**Actors:** Account-owning participant and Exchange / Trading Platform; other accounts may establish market prices.

**Related business objects:** Trading Account, Balance, Portfolio Position, Trade, Fee, Last Price observation; Experiment.

**Exact trace:** [OQ-003](../decisions.md#oq-003) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-024](../decisions.md#oq-024) [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034) [OQ-042](../decisions.md#oq-042) [OQ-044](../decisions.md#oq-044) [OQ-046](../decisions.md#oq-046) [BR-PF-001](../09-portfolio-pnl.md#br-pf-001) [BR-PREC-001](../03-markets-assets.md#br-prec-001) [BAL-INV-002](../invariants.md#bal-inv-002) [SIM-INV-001](../invariants.md#sim-inv-001) [OQ-035](../decisions.md#oq-035)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-003](../decisions.md#oq-003); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-024](../decisions.md#oq-024), [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034), [OQ-035](../decisions.md#oq-035), [OQ-042](../decisions.md#oq-042), [OQ-044](../decisions.md#oq-044), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Distinguish fixed holdings/cost from changing indicative value and stale price.

### Preconditions

Known 1.5 BTC position/basis 127,500; actual Last Price 87,000 at supplied virtual time; clock ages supplied for fixture.

### Trigger

Observe at age exactly 60 seconds, greater than 60 (e.g. supplied age 61), or after another account establishes a new actual Last Price.

### Main flow

1. [P] At age 60, no older-than-60 flag.
2. [P] At greater age, retain price but mark stale with age; units/basis/realized unchanged.
3. [P] New actual Trade price may change unrealized/equity without an own-account Trade.

### Alternative flows

No Trade yet on another nonzero Base makes total equity unavailable even when BTC is priced. Zero holdings need no invented price for that holding.

### Failure / invalid flows

Do not timestamp old Last Price as fresh, replace it with quotes or subtract future sale fees.

### No-action behavior

No own Trade means no own ownership/basis/realized change; no market Trade means same price with aging only if virtual clock progresses.

### Time and boundary behavior

Ages supplied; fractional resolution [GAP-HFT-001](../review/gap-register.md#gap-hft-001) and idle/end clock progress [GAP-EXP-003](../review/gap-register.md#gap-exp-003) are not selected.

### Postconditions and expected state changes

Known indicative valuation distinguishes freshness without claiming liquidity guarantee.

### Expected balance / Trade / fee / market-data effects

At retained 87,000 mark 130,500/unrealized 3,000 persists even stale; reservation does not reduce holdings.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PF](../diagrams/README.md#dia-pf) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
