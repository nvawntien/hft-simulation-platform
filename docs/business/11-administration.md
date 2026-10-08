# Administration and scenario liquidity

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-27"></a>

## 27. Market Maker

**ESTABLISHED DOMAIN CONCEPT.** A Market Maker provides buying and selling interest. A bid `85,500` and ask `86,000` offer liquidity, but their `500` spread is not guaranteed profit: one side may fill, inventory may accumulate, and prices/fees affect results.

**APPROVED — [OQ-003](decisions.md#oq-003):** administrators seed assets into dedicated accounts; those accounts must trade through the book. Administrative direct Trades, forced fills, and direct Last Price changes are prohibited.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-036](decisions.md#oq-036), [OQ-043](decisions.md#oq-043):** V1 seeds liquidity through predefined, funded scenario-account Limit Orders submitted in declared scenario action order once the experiment is running. Setup funding itself leaves the book empty. Those accounts may also host experimental automated strategies under the same ordinary rules. V1 supplies no guaranteed always-on Market Maker, automatic inventory refill, or special priority. Designing an adaptive market-making strategy is beyond this specification. A market can legitimately be empty or one-sided.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-sim-001"></a>BR-SIM-001 | Initial USDT is configurable; seed approved assets into scenario accounts through Simulation Funding, never direct Trades. | Establish liquidity without fabricated execution. | APPROVED | [OQ-003](decisions.md#oq-003) |

## Phase B — behavioral analysis

### Purpose and responsibility

Administrator can allocate approved simulated assets to dedicated liquidity accounts under [OQ-003](decisions.md#oq-003). Proposed [OQ-042](decisions.md#oq-042) confines preparation inputs/funding before start. This role cannot manufacture actual Trades, force counterparties, grant queue priority or set Last Price. Research invalidity handling is proposed [OQ-045](decisions.md#oq-045), with authority still [GAP-ADM-001](review/gap-register.md#gap-adm-001).

### Objects, attributes and lifecycle

| Administrative object / input | Business meaning | Actions and undecided lifecycle |
|-------------------------------|------------------|--------------------------------|
| Scenario preparation | Date, normal USDT amounts, scenario Base amounts/acquisition references, rates, action schedule, delays | Configure before running; no implicit amounts/rates/delays; readiness and partial invalid setup [GAP-EXP-001](review/gap-register.md#gap-exp-001)/[GAP-BAL-001](review/gap-register.md#gap-bal-001) |
| Liquidity / Scenario Account | Finite capital placed through ordinary Limits | Starts funded but not a book Order; seeded action order while running; no auto replenishment |
| Research-validity label | Indicates whether preserved run is usable research evidence | [OQ-045](decisions.md#oq-045) prohibits correction; classifier/relabel timing [GAP-ADM-001](review/gap-register.md#gap-adm-001); not an Experiment trading state |

No separate administrator lifecycle is relevant to the source's responsibility model. Trading Account membership and detailed administrative observations require Actors/Data review, not invented roles. Scenario-hosted strategy capital remains [GAP-ACT-002](review/gap-register.md#gap-act-002).

### Decision table — setup versus running / historical action

| Initial phase / object | Trigger / condition | Outcome | State / financial / data effects | Basis | Status |
|------------------------|---------------------|---------|----------------------------------|-------|--------|
| Setup, explicit valid inputs | Allocate simulation capital | Fund balances / seeded basis | No Trade/volume/PnL/Last Price; book empty until Orders | [OQ-003](decisions.md#oq-003)/[OQ-033](decisions.md#oq-033)/[OQ-036](decisions.md#oq-036)/[OQ-042](decisions.md#oq-042)/[OQ-046](decisions.md#oq-046) | PROVISIONAL complete setup |
| Running | Scheduled funded scenario Limit | Ordinary validation, priority, STP and matching | May rest, partly fill or fully fill; no guaranteed initial depth if crossed | [OQ-003](decisions.md#oq-003)/[OQ-010](decisions.md#oq-010)/[OQ-011](decisions.md#oq-011)/[OQ-036](decisions.md#oq-036) | PROVISIONAL scenario timing |
| Running, depleted inventory | Demand more liquidity / in-run funding | No automatic inventory refill; in-run funding unavailable | Legitimate empty/one-sided market; no forced Trade | [OQ-036](decisions.md#oq-036)/[OQ-042](decisions.md#oq-042) | PROVISIONAL |
| Any trading account | Force fill/price or privileged matching | Prohibited | No fabricated execution/market price | [OQ-003](decisions.md#oq-003); [BR-ACT-001](01-scope-actors.md#br-act-001) | APPROVED |
| Running | Change fixed rates/delays | Cannot change this run's fixed inputs | Separate experiment for changed comparison | [OQ-016](decisions.md#oq-016)/[OQ-041](decisions.md#oq-041)/[OQ-042](decisions.md#oq-042) | PROVISIONAL |
| Confirmed history | Edit/delete/reverse/correct Trade | Prohibited in V1 | Preserve invalid label/history; fresh run separately | [OQ-045](decisions.md#oq-045) | PROVISIONAL |
| Investigation | Read other participant individual history | UNDECIDED — [GAP-ADM-002](review/gap-register.md#gap-adm-002) | Participant privacy does not grant unrestricted administrator view | [OQ-037](decisions.md#oq-037)/[OQ-042](decisions.md#oq-042) | OPEN |

### No action, failure and review dependencies

If no account submits Orders, allocated capital itself creates no interest. Declared liquidity Orders still require full funds; no later refill can pre-fund them. Missing setup values cannot be replaced by analyst defaults; the complete invalid-configuration outcome remains [GAP-BAL-001](review/gap-register.md#gap-bal-001). Control of manual/natural end, duration and pause/resume remain Experiment gaps.

[BS-EXP-001](scenarios/experiment.md#bs-exp-001)/[BS-EXP-006](scenarios/experiment.md#bs-exp-006)/[BS-EXP-007](scenarios/experiment.md#bs-exp-007), [BS-BAL-001](scenarios/balance-settlement.md#bs-bal-001) and [BS-HFT-007](scenarios/algo-hft.md#bs-hft-007) cover setup, invalid runs, ordinary finite inventory and hosting ambiguity. [DIA-FUND](diagrams/README.md#dia-fund) and [DIA-EXP](diagrams/README.md#dia-exp) show sourced interactions. Administration depends on Actors, Markets, Experiment, Portfolio and Fees. Retention/export business needs are [GAP-EXP-005](review/gap-register.md#gap-exp-005); technical formats are deferred.
