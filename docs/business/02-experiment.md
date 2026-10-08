# Experiment and scenario

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

The source has no dedicated Experiment chapter. Its canonical policies are [OQ-035](decisions.md#oq-035), [OQ-042](decisions.md#oq-042), [OQ-045](decisions.md#oq-045), with related order/end details in [source sections 13–15](04-orders.md#source-section-13) and [candle time semantics](08-market-data.md#source-section-24). No full Experiment state model is migrated because none was defined.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-sim-002"></a>BR-SIM-002 | Run bounded experiments; setup-only funding/configuration; end by cancelling remainders and preserve history. | Reset by starting a separate experiment. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-035](decisions.md#oq-035), [OQ-042](decisions.md#oq-042), [OQ-045](decisions.md#oq-045) |

## Phase B — behavioral analysis

### Purpose, responsibility and boundary

An Experiment bounds one synthetic research run; a Scenario declares inputs and ordered actions used by a run. They are related concepts, not interchangeable histories. Administrator preparation is proposed [OQ-042](decisions.md#oq-042). The initiator authorized to start/end is not selected. Start/end, duration or pause/resume capabilities cannot be inferred from the word “bounded”.

### Objects and meaningful attributes

| Object | Known attributes / meaning | Creation, state and ownership limits |
|--------|----------------------------|-------------------------------------|
| Scenario | Declared date, initial capital, seeded acquisition references, maker/taker rates, planned scenario Orders/action order, participant decisions and delays | Administrator prepares inputs ([OQ-042](decisions.md#oq-042)); versioning, creation/validation outcomes are not formally defined |
| Experiment | Separate funding, participants, Trade/balance history, virtual clock, declared scenario inputs, research-validity label | Bounded run proposed [OQ-035](decisions.md#oq-035); start/reset creates separate history under [OQ-042](decisions.md#oq-042); formal state names/control authorization are [GAP-EXP-001](review/gap-register.md#gap-exp-001) |
| Experiment Clock | Virtual UTC date/start, elapsed simulated time and authoritative action/execution order | Starts at declared date 00:00:00 UTC ([OQ-035](decisions.md#oq-035)); progression/resolution/control are [GAP-EXP-003](review/gap-register.md#gap-exp-003)/[GAP-HFT-001](review/gap-register.md#gap-hft-001) |

Scenario values are explicit inputs, not defaults. Duration, required end time and supported end triggers remain [GAP-EXP-002](review/gap-register.md#gap-exp-002). Research validity is a label under [OQ-045](decisions.md#oq-045), not a new trading state. Old experimental facts remain associated with the old run; reset is not balance transfer.

### Preconditions, actions and postconditions

Known preparation includes declared funds/rates/delays and seeded cost references on the proposed grids; setup funding alone leaves the book empty ([OQ-036](decisions.md#oq-036)/[OQ-042](decisions.md#oq-042)/[OQ-046](decisions.md#oq-046)). A scenario account submits ordinary funded Limits in declared action order **while running**, so planned liquidity can itself match existing participant interest. The exact start readiness check and failed partial preparation are [GAP-EXP-001](review/gap-register.md#gap-exp-001)/[GAP-BAL-001](review/gap-register.md#gap-bal-001).

### Transition / decision table — phase descriptions, not new enum states

| Initial business phase | Event / precondition | Outcome / resulting phase | Related objects / finances | Basis | Status |
|------------------------|----------------------|---------------------------|----------------------------|-------|--------|
| Preparation | Declare valid inputs and Simulation Funding | Configured inputs; book still empty until submission | Capital / seeded basis; no Trade or Last Price | [OQ-003](decisions.md#oq-003)/[OQ-033](decisions.md#oq-033)/[OQ-036](decisions.md#oq-036)/[OQ-042](decisions.md#oq-042)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Prepared | Start requested | UNDECIDED — [GAP-EXP-001](review/gap-register.md#gap-exp-001) | Readiness/authority/invalid start unresolved | [OQ-035](decisions.md#oq-035)/[OQ-042](decisions.md#oq-042) | OPEN |
| Running | Scenario action reaches effective order | Apply ordinary trading rules | Orders/Trades/reserves may change | [OQ-035](decisions.md#oq-035)/[OQ-036](decisions.md#oq-036); trading modules | PROVISIONAL |
| Running, active remainders | End already made effective in known order | End all active remainders | CANCELLED/EXPERIMENT_ENDED; unused reserves released; prior facts retained | [OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027)/[OQ-030](decisions.md#oq-030)/[OQ-031](decisions.md#oq-031)/[OQ-035](decisions.md#oq-035)/[OQ-042](decisions.md#oq-042)/[OQ-045](decisions.md#oq-045) | PROVISIONAL conditional subflow |
| Running, no actions | Virtual time progresses | Trading creates no Trade; whether/when time progresses is UNDECIDED — [GAP-EXP-003](review/gap-register.md#gap-exp-003) | Candles, price age and pending arrivals depend on clock | [OQ-035](decisions.md#oq-035)/[OQ-039](decisions.md#oq-039)/[OQ-041](decisions.md#oq-041) | OPEN complete time flow |
| Preserved old run | Reset into another run | Separate inputs/funding/history; no transfer | Old balances/history remain | [OQ-035](decisions.md#oq-035)/[OQ-042](decisions.md#oq-042)/[OQ-045](decisions.md#oq-045) | PROVISIONAL; start control still OPEN |
| Run deemed invalid | Research validity recorded | Preserve and label facts; end and fresh separate run | No Trade edit/reversal/funding correction | [OQ-045](decisions.md#oq-045) | PROVISIONAL conditional subflow; classifier [GAP-ADM-001](review/gap-register.md#gap-adm-001) |

### No action, time boundaries and failure behavior

An empty/one-sided book is legitimate; the platform does not manufacture counterparties ([OQ-003](decisions.md#oq-003)/[OQ-036](decisions.md#oq-036)). No action implies no new trading effects; it does not answer whether virtual time advances. Stopped clock time does not advance (source §24); this is not authorization for a pause/resume feature. Requesting Orders while stopped, after end, or at exactly end is OPEN.

30-minute and one-hour examples are **blocked policy probes**, not supported defaults/presets. See [BS-EXP-008](scenarios/experiment.md#bs-exp-008) and [GAP-EXP-002](review/gap-register.md#gap-exp-002)/[GAP-EXP-004](review/gap-register.md#gap-exp-004). For actions already ordered before an effective end, the proposed [OQ-031](decisions.md#oq-031) complete immediate outcome stands. There is no assumed wall-clock conversion, automatic timeout or client timestamp tie-break.

### Scenarios, diagrams and dependencies

[BS-EXP-001](scenarios/experiment.md#bs-exp-001) through [BS-EXP-008](scenarios/experiment.md#bs-exp-008) cover preparation, start, idle, effective end, reset, invalidity, finite liquidity and duration/boundaries. [DIA-EXP](diagrams/README.md#dia-exp) shows only the conditional end subflow with OPEN control nodes. End impacts Orders, Reservation, Trade, Fee, Candle and Portfolio; pending latency observations/actions require Algo/HFT review. [GAP-EXP-001](review/gap-register.md#gap-exp-001) through [GAP-EXP-005](review/gap-register.md#gap-exp-005) and [GAP-MAT-001](review/gap-register.md#gap-mat-001) are reviewed before claiming a complete executable experiment lifecycle.
