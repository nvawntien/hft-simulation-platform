# Algo Hft business scenarios

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Scenario index](README.md) · [Coverage](../review/coverage-matrix.md) · [Gaps](../review/gap-register.md)

## Reading convention

[A] identifies behavior supported by approved source. [P] identifies proposed source behavior and its derived consequences. [A/P] explicitly combines those layers; the dependency table determines which decision supports which layer, and the complete scenario remains PROVISIONAL. [OPEN] marks behavior with no selected policy. Fixture values are illustrative inputs, never defaults or approvals. A conditional fixture can supply effective order/observation facts without deciding how those facts are generated.

<a id="bs-hft-001"></a>

## [BS-HFT-001](algo-hft.md#bs-hft-001) — Observed quote is older than effective book

Status: PROVISIONAL

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | [GAP-HFT-001](../review/gap-register.md#gap-hft-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002)/[GAP-MKT-001](../review/gap-register.md#gap-mkt-001) for deriving the supplied facts; this is the conditional distinction only. |

### Business purpose

Avoid assuming observed liquidity is still executable.

### Preconditions

Strategy with explicit fixed nonnegative delays; particular decision-observed Ask 86,000 and later effective book/Order outcome supplied as fixture.

### Trigger

Strategy chooses protected BUY using already seen information; later action becomes effective.

### Main flow

1. [P] Observation delay affects information seen; no newer unobserved quote is substituted as decision reference.
2. [P] Action delay affects arrival into business order.
3. [A/P] Execute only actual eligible interest within absolute protection/priority; result can differ from old depth.

### Alternative flows

If actual eligible depth/price unchanged, observed and executed conditions may align; no guarantee is created.

### Failure / invalid flows

No grandfathered quote, privileged faster fill, client-time priority or price-protection percentage substitution.

### No-action behavior

No strategy action creates no new trading request; existing resting Orders can still match others.

### Time and boundary behavior

Observation delivery/coherence/precision are not inferred; fixture supplies seen/effective facts. Pending/end arrival branch excluded.

### Postconditions and expected state changes

A legitimate actual outcome is evaluated independently of earlier observed intention.

### Expected balance / Trade / fee / market-data effects

Fill/fee/slippage uses actual fills and old observed opposing reference; exact sample arithmetic [BS-HFT-005](algo-hft.md#bs-hft-005).

### Known unresolved questions

[GAP-HFT-001](../review/gap-register.md#gap-hft-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002)/[GAP-MKT-001](../review/gap-register.md#gap-mkt-001) for deriving the supplied facts; this is the conditional distinction only.

### Diagram and scenario-specific reading

[DIA-LATENCY](../diagrams/README.md#dia-latency) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-002"></a>

## [BS-HFT-002](algo-hft.md#bs-hft-002) — Cancellation action delay with known effective precedence

Status: PROVISIONAL

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment; Reservation, Trade.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-023](../decisions.md#oq-023), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Separate decision time from successful cancellation.

### Preconditions

Own active Limit; fixed action delay; supplied effective order has counterparty execution before cancellation, away from run end.

### Trigger

Strategy decides cancel; it reaches authoritative order later.

### Main flow

1. [P] Earlier entire incoming outcome completes; earlier fills stand.
2. [P] Effective cancel ends only remaining quantity or reports already filled; no CANCEL_PENDING state/success at decision time.

### Alternative flows

Supplied reverse effective order: cancellation stops remainder before later incoming action. Neither outcome is chosen for unsupplied identical arrivals.

### Failure / invalid flows

No refund of prior Trade/fee or automatic success because decision was made earlier.

### No-action behavior

Strategy doing nothing does not cancel existing active interest.

### Time and boundary behavior

Arrival exactly at/after end or still pending at end is BLOCKED [BS-EXP-008](experiment.md#bs-exp-008); fixture excludes it.

### Postconditions and expected state changes

Own observed cancel outcome reflects effective action, not requested intention.

### Expected balance / Trade / fee / market-data effects

Unused reserve releases on actual cancellation only; prior costs/fees remain.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-PRECEDENCE](../diagrams/README.md#dia-precedence) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-003"></a>

## [BS-HFT-003](algo-hft.md#bs-hft-003) — Zero delay and invalid delay boundaries

Status: PROVISIONAL

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | [GAP-HFT-001](../review/gap-register.md#gap-hft-001) for nonzero precision/limits, [GAP-HFT-002](../review/gap-register.md#gap-hft-002) for delivery; zero-input principle only. |

### Business purpose

Permit zero latency input without a priority exemption.

### Preconditions

Valid fixture with fixed observation/action delay zero; accepted Order sequence/effective facts supplied.

### Trigger

Configure or compare delay values.

### Main flow

1. [P] Nonnegative fixed input allows zero; negative invalid.
2. [A] Already accepted same-price sequence still governs priority.
3. [P] Zero delay never guarantees a fill or retrospectively changes priority.

### Alternative flows

Positive delay changes possible observation/arrival assumptions across separate runs; precise allowable maxima/grid remain [GAP-HFT-001](../review/gap-register.md#gap-hft-001).

### Failure / invalid flows

No implicit delay default, in-run change, random jitter or physical performance claim.

### No-action behavior

No trading decision at zero delay produces no Order; speed alone is not an execution event.

### Time and boundary behavior

Time resolution/representability/limits not selected; the specific zero fixture is admitted by nonnegative source.

### Postconditions and expected state changes

Priority principles remain intact with zero input.

### Expected balance / Trade / fee / market-data effects

No fee/balance effect from configuring a delay itself; real Trades still determine all financial results.

### Known unresolved questions

[GAP-HFT-001](../review/gap-register.md#gap-hft-001) for nonzero precision/limits, [GAP-HFT-002](../review/gap-register.md#gap-hft-002) for delivery; zero-input principle only.

### Diagram and scenario-specific reading

[DIA-LATENCY](../diagrams/README.md#dia-latency) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-004"></a>

## [BS-HFT-004](algo-hft.md#bs-hft-004) — Own resting queue count and quantity ahead

Status: PROVISIONAL

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [OQ-027](../decisions.md#oq-027) [ORD-INV-005](../invariants.md#ord-inv-005)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-027](../decisions.md#oq-027), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | [GAP-HFT-004](../review/gap-register.md#gap-hft-004) for never-resting/terminal measure; conditional resting snapshot only. |

### Business purpose

Relate queue observation to actual surviving interest.

### Preconditions

Own accepted resting Limit behind earlier same-price active external 0.4 BTC and 0.6 BTC; all snapshots/effective facts supplied.

### Trigger

Inspect queue, then observe first earlier Order fully filled or cancelled.

### Main flow

1. [P] Initial queue count 2 and quantity ahead 1 BTC.
2. [A/P] After first disappears, count 1/quantity 0.6 BTC; own sequence unchanged.
3. [P] Partial fill of second reduces quantity ahead while count remains 1 until it ends.

### Alternative flows

A better-price Order determines matching before same-price queue, but is not added to the stated same-price-ahead measure.

### Failure / invalid flows

No guarantee of future own fill, no larger-size overtaking, no participant-time reorder. Non-resting/terminal queue metric applicability is OPEN [GAP-HFT-004](../review/gap-register.md#gap-hft-004).

### No-action behavior

No book event changes no queue-ahead facts; elapsed time alone changes no accepted priority.

### Time and boundary behavior

Supplied observation instant; delivery/coherence [GAP-HFT-002](../review/gap-register.md#gap-hft-002)/[GAP-MKT-001](../review/gap-register.md#gap-mkt-001); initial same-time allocation [GAP-MAT-001](../review/gap-register.md#gap-mat-001) excluded.

### Postconditions and expected state changes

Queue ahead tracks active earlier same-price interest, not identities or guaranteed duration.

### Expected balance / Trade / fee / market-data effects

Queue reporting changes no balance/fee/Trade; actual predecessor events have their own settlement effects.

### Known unresolved questions

[GAP-HFT-004](../review/gap-register.md#gap-hft-004) for never-resting/terminal measure; conditional resting snapshot only.

### Diagram and scenario-specific reading

[DIA-MATCH](../diagrams/README.md#dia-match) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-005"></a>

## [BS-HFT-005](algo-hft.md#bs-hft-005) — Slippage, fees, partial/no-fill and missing reference metrics

Status: PROVISIONAL

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment; Trade, Fee, Position.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010), [OQ-013](../decisions.md#oq-013), [OQ-014](../decisions.md#oq-014); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-031](../decisions.md#oq-031), [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | None within this fixture; broader dependencies remain in the domain module. |

### Business purpose

Keep no result unavailable instead of fabricating zero price/cost.

### Preconditions

Positive original quantity; actual fills/fees and decision-observed opposing reference supplied, or explicitly absent. Example BUY 2 BTC at weighted 86,500, observed Ask 86,000, taker 0.2%.

### Trigger

Report execution quality after supplied actual outcome.

### Main flow

1. [P] BUY adverse 500 USDT/BTC = 0.5814%; total notional 173,000, fees 346, fee-inclusive unit cost 86,673.
2. [P] SELL average 85,000 vs observed Bid 85,500 gives adverse 500; improvement reverses sign.
3. [P] No fills: ratio 0, average/unit costs/slippage/first/full timing unavailable; partial fill: actual ratio and averages, full-fill timing unavailable.

### Alternative flows

Fills with missing observed reference: average/cost can be known but slippage unavailable. No-fill fee is zero because no Trade.

### Failure / invalid flows

No protection price/midpoint/later quote substitute for reference; no future liquidation fee in unrealized; no zero elapsed for unachieved fill.

### No-action behavior

No new Trade leaves achieved quantities/costs unchanged; derived report causes no transfer.

### Time and boundary behavior

Time-to-achieved fill origin remains [BS-HFT-008](algo-hft.md#bs-hft-008); this scenario only specifies unavailable unachieved time values.

### Postconditions and expected state changes

Metrics truthfully express actual executed quantity/cost and available reference.

### Expected balance / Trade / fee / market-data effects

Slippage is separate from fee; no metric authorizes execution outside absolute Market protection.

### Known unresolved questions

None within this fixture; broader dependencies remain in the domain module.

### Diagram and scenario-specific reading

[DIA-METRICS](../diagrams/README.md#dia-metrics) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-006"></a>

## [BS-HFT-006](algo-hft.md#bs-hft-006) — Separate experiments with different delays or same supplied inputs

Status: PROVISIONAL

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment; Scenario, Trade.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [OQ-016](../decisions.md#oq-016) [OQ-045](../decisions.md#oq-045)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-016](../decisions.md#oq-016), [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-045](../decisions.md#oq-045), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-003](../review/gap-register.md#gap-exp-003)/[GAP-HFT-001](../review/gap-register.md#gap-hft-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002) for generating effective facts without fixture supply. |

### Business purpose

Distinguish controlled comparison from changing a running policy.

### Preconditions

Two separately configured synthetic runs; funding/rates/scenario/strategy decisions and observed facts/effective order are identified; one comparison changes declared delays.

### Trigger

Review resulting histories and comparison inputs.

### Main flow

1. [P] Same complete supplied inputs/effective order support reproducible outcomes.
2. [P] Changed delay/decision is identified as changed input, fixed within each run.
3. [A/P] Faster setting can change acceptance/queue results in new run, never priority already established in old run.

### Alternative flows

Even equal nominal delays need not establish equal supplied effective order without tie/delivery policies; such unsupplied comparisons remain blocked.

### Failure / invalid flows

No random jitter, historical-data backtest, production speed/profit claim or old history overwrite.

### No-action behavior

Strategy can choose no action; comparative results then reflect other declared scenario/participant actions, not guaranteed zero activity.

### Time and boundary behavior

Complete reproduction from just scheduled inputs awaits clock/tie/observation policy; this fixture supplies those effective facts.

### Postconditions and expected state changes

Comparison lists changed inputs and preserves separate histories.

### Expected balance / Trade / fee / market-data effects

Actual fees/fills/PnL can worsen or improve; no monotonic profitability guarantee from smaller delay.

### Known unresolved questions

[GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-003](../review/gap-register.md#gap-exp-003)/[GAP-HFT-001](../review/gap-register.md#gap-hft-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002) for generating effective facts without fixture supply.

### Diagram and scenario-specific reading

[DIA-LATENCY](../diagrams/README.md#dia-latency) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-007"></a>

## [BS-HFT-007](algo-hft.md#bs-hft-007) — Scenario-hosted strategy capital and identity ambiguity

Status: OPEN / BLOCKED

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment; Participant, Balance.

**Exact trace:** [OQ-008](../decisions.md#oq-008) [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-040](../decisions.md#oq-040) [OQ-041](../decisions.md#oq-041) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [BR-ORD-001](../05-matching-stp.md#br-ord-001) [BR-MKT-002](../08-market-data.md#br-mkt-002) [OQ-036](../decisions.md#oq-036) [GAP-ACT-001](../review/gap-register.md#gap-act-001) [GAP-ACT-002](../review/gap-register.md#gap-act-002)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | [OQ-008](../decisions.md#oq-008), [OQ-010](../decisions.md#oq-010); supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-031](../decisions.md#oq-031), [OQ-035](../decisions.md#oq-035), [OQ-036](../decisions.md#oq-036), [OQ-037](../decisions.md#oq-037), [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041), [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-046](../decisions.md#oq-046) |
| Not defined by source | [GAP-ACT-001](../review/gap-register.md#gap-act-001)/[GAP-ACT-002](../review/gap-register.md#gap-act-002); no silent reconciliation of source tension. |

### Business purpose

Surface source tension rather than silently share strategy capital.

### Preconditions

A seeded scenario account owns active Orders; separately named automated strategy is described as hosted there.

### Trigger

Assign strategy account/capital and submit/cancel ordinary intent.

### Main flow

1. [P] §27/[OQ-036](../decisions.md#oq-036) permits scenario accounts hosting experimental strategies.
2. [P] §4/[OQ-043](../decisions.md#oq-043) gives each strategy/scenario participant separate balance pool.
3. [OPEN] Same participant versus separate hosted participant relationship is [GAP-ACT-002](../review/gap-register.md#gap-act-002).

### Alternative flows

Discussion alternatives: scenario itself acts algorithmically, or a distinct strategy gets separate configured account. Neither is silently selected.

### Failure / invalid flows

No invented pooled capital, inter-account transfer or admin-only strategy privilege; membership/change semantics [GAP-ACT-001](../review/gap-register.md#gap-act-001).

### No-action behavior

No new action leaves existing scenario facts but does not resolve strategy ownership attribution.

### Time and boundary behavior

Reassigning account during run can change STP/own-cancel/queue meaning and is not authorized.

### Postconditions and expected state changes

Complete strategy/account allocation outcome BLOCKED.

### Expected balance / Trade / fee / market-data effects

Which pool can reserve/spend, STP identity and reported profit remain undecided; no balances guessed.

### Known unresolved questions

[GAP-ACT-001](../review/gap-register.md#gap-act-001)/[GAP-ACT-002](../review/gap-register.md#gap-act-002); no silent reconciliation of source tension.

### Diagram and scenario-specific reading

[DIA-FUND](../diagrams/README.md#dia-fund) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.

<a id="bs-hft-008"></a>

## [BS-HFT-008](algo-hft.md#bs-hft-008) — Achieved time-to-first/full-fill has no chosen origin

Status: OPEN / BLOCKED

**Actors:** Strategy Participant and Exchange / Trading Platform; scenario/other participants as stated; Administrator supplies setup inputs without priority privilege.

**Related business objects:** Strategy Participant, Market Data Observation, Trading Account, Order, Order Book, Experiment; Trade.

**Exact trace:** [OQ-035](../decisions.md#oq-035) [OQ-041](../decisions.md#oq-041) [BR-ALGO-001](../10-algo-hft.md#br-algo-001) [GAP-HFT-003](../review/gap-register.md#gap-hft-003)

| Source layer | Applicability to this scenario |
|--------------|--------------------------------|
| Approved source | No approved project decision alone specifies this complete result; supporting domain definitions do not approve project refinements |
| Proposed source | [OQ-035](../decisions.md#oq-035), [OQ-041](../decisions.md#oq-041) |
| Not defined by source | [GAP-HFT-003](../review/gap-register.md#gap-hft-003); exact precision [GAP-HFT-001](../review/gap-register.md#gap-hft-001). |

### Business purpose

Prevent inconsistent elapsed metrics in research.

### Preconditions

Decision t0, submission t1, platform arrival t2, acceptance t3 and first/full fill occurrence supplied as ordered virtual facts.

### Trigger

Report achieved elapsed first/full fill value.

### Main flow

1. [P] Experiment time is the measuring clock; unachieved fill remains unavailable.
2. [OPEN] Achieved elapsed origin is [GAP-HFT-003](../review/gap-register.md#gap-hft-003); t0/t1/t2/t3 are distinct candidates, not selected values.

### Alternative flows

First achieved but full unachieved: full elapsed unavailable while first numeric origin remains blocked. Equal displayed time does not remove authoritative order.

### Failure / invalid flows

No guessed zero or subtraction from arbitrary decision/submission/arrival/acceptance time; exact metric assertions wait for owner choice.

### No-action behavior

If no fill event happens, both timing metrics unavailable per proposed source, regardless of elapsed physical time.

### Time and boundary behavior

No wall-clock performance measure or arbitrary unit/precision substituted; [GAP-HFT-001](../review/gap-register.md#gap-hft-001) remains separate.

### Postconditions and expected state changes

Numeric achieved timing outcome BLOCKED.

### Expected balance / Trade / fee / market-data effects

No financial transfer from computing timing; comparison may otherwise misattribute delay to execution.

### Known unresolved questions

[GAP-HFT-003](../review/gap-register.md#gap-hft-003); exact precision [GAP-HFT-001](../review/gap-register.md#gap-hft-001).

### Diagram and scenario-specific reading

[DIA-METRICS](../diagrams/README.md#dia-metrics) applies to the branch described in this scenario. Use this fixture's preconditions and main flow to select the branch; any OPEN node and any proposed extra branch retain their own status. Expected effects above delimit this scenario rather than approving every branch of a reusable diagram.
