# Open business gap register

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](../README.md) · [Coverage](coverage-matrix.md)

## Meaning and triage

All entries below have **Status: OPEN**. Priority is an analyst proposal for review, not an approved requirement. An OPEN gap is missing policy or a source tension; it does not erase an existing proposed recommendation. No new OQ is allocated. Source links point to migrated Business, while FRs and Requirements TBDs are impact hints only.

**Lưu ý:** các phương án thảo luận không được dùng làm yêu cầu. Team cần quyết định rõ bởi người có thẩm quyền, cập nhật Business rồi mới đối chiếu Requirements và triển khai.

## Gaps

<a id="gap-exp-001"></a>

## [GAP-EXP-001](gap-register.md#gap-exp-001) — Start/end authority and complete lifecycle

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Experiment; missing observable business behavior |
| Proposed priority | BLOCKER |
| Exact Business source | [Source §32 OQ-035/OQ-042; §4 actors](../decisions.md#oq-035) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-042](../decisions.md#oq-042) [BR-SIM-002](../02-experiment.md#br-sim-002); consult canonical status, no new approval |
| Concrete ambiguous scenario | A prepared run is started twice, or a participant requests end while an administrator also requests it. |
| Already known | Setup precedes running; trading is continuous while running; effective end cancels active remainders (proposed). |
| Not defined | Who may initiate start/end, readiness conditions, repeated/invalid control outcomes and complete states are absent. |
| Why it matters | Different control outcomes determine whether Orders and Trades can exist. |
| Impacted objects / BS / FR / TBD | Experiment, Scenario; [BS-EXP-002](../scenarios/experiment.md#bs-exp-002), [BS-EXP-008](../scenarios/experiment.md#bs-exp-008); FR-SIM-001, FR-SIM-002, FR-ADM-003; Requirements TBD-001 |
| Owner decision needed / discussion options | Name permitted actors and lifecycle/control outcomes. Do not infer a named READY/PAUSED state. |

<a id="gap-exp-002"></a>

## [GAP-EXP-002](gap-register.md#gap-exp-002) — Duration and natural versus explicit termination

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Experiment; missing observable business behavior |
| Proposed priority | BLOCKER |
| Exact Business source | [Source §32 OQ-035/OQ-042](../decisions.md#oq-035) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-042](../decisions.md#oq-042) [BR-SIM-002](../02-experiment.md#br-sim-002); consult canonical status, no new approval |
| Concrete ambiguous scenario | After 30 virtual minutes with no actions, one team expects automatic end; another continues until a manual end. Repeat at one hour. |
| Already known | Bounded experiments and effective end consequences are proposed. No universal length is selected. |
| Not defined | Required duration input, valid duration bounds/defaults, auto-end support, manual-end support and their interaction are absent. |
| Why it matters | End time changes fills, reservations, candles and research comparability. |
| Impacted objects / BS / FR / TBD | Experiment, Order, Candle Interval; [BS-EXP-008](../scenarios/experiment.md#bs-exp-008); FR-SIM-001, FR-SIM-002; TBD-001 |
| Owner decision needed / discussion options | Decide whether duration is required and which end triggers exist. Fixed-duration auto-end, explicit end, or a combination are discussion alternatives. Neither 30 minutes nor one hour is authorized as a default or supported preset. |

<a id="gap-exp-003"></a>

## [GAP-EXP-003](gap-register.md#gap-exp-003) — Clock progression and submissions while stopped

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Experiment; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §24 stopped time; §29 virtual delays; §32 OQ-035](../08-market-data.md#source-section-24) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | An empty book has no actions for one minute; does virtual time reach the next candle? A participant submits while time is stopped. |
| Already known | The virtual start is declared UTC midnight; stopped experiment time does not progress (proposed). |
| Not defined | Progression while idle, who controls the clock, treatment of submissions while stopped, and whether pause/resume exists are absent. |
| Why it matters | Idle runs can produce different price ages, observation arrivals and intervals. |
| Impacted objects / BS / FR / TBD | Experiment, Observation, Candle; [BS-EXP-003](../scenarios/experiment.md#bs-exp-003), [BS-EXP-008](../scenarios/experiment.md#bs-exp-008); FR-SIM-001, FR-HFT-001, FR-MKT-009; TBD-001/TBD-002 |
| Owner decision needed / discussion options | Define observable time progress and stopped-input handling. Event-driven advancement or continuously advancing virtual time are options, not adoption of a mechanism. Decide whether pause/resume is supported before adding states. |

<a id="gap-exp-004"></a>

## [GAP-EXP-004](gap-register.md#gap-exp-004) — Exact end boundary and delayed pending actions

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Experiment; missing observable business behavior |
| Proposed priority | BLOCKER |
| Exact Business source | [Source §14 precedence; §24 partial interval; §32 OQ-031/OQ-035/OQ-041](../decisions.md#oq-031) |
| Related approved/proposed decisions | [OQ-010](../decisions.md#oq-010) [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | A delayed BUY or cancel arrives exactly at the intended end; another action was decided before end but arrives afterward. |
| Already known | Authoritative action order and complete immediate incoming outcome precede the next effective action (proposed). End cancels active remainders and preserves earlier Trades. |
| Not defined | Whether the exact end instant accepts actions, treatment of arrivals after end, discarded/reported pending actions and undelivered observations are absent. |
| Why it matters | Changes ownership, Trade timestamps, final candles, cancellation results and latency comparisons. |
| Impacted objects / BS / FR / TBD | Order, Experiment, Observation, Reservation; [BS-EXP-004](../scenarios/experiment.md#bs-exp-004), [BS-EXP-008](../scenarios/experiment.md#bs-exp-008), [BS-ORD-017](../scenarios/orders.md#bs-ord-017), [BS-HFT-002](../scenarios/algo-hft.md#bs-hft-002); FR-MAT-007, FR-SIM-002, FR-HFT-001; TBD-001/TBD-002 |
| Owner decision needed / discussion options | Choose boundary inclusion and pending-action/observation outcomes; preserve approved accepted-order priority. Do not equate participant decision time with eligibility before end. |

<a id="gap-exp-005"></a>

## [GAP-EXP-005](gap-register.md#gap-exp-005) — Preserved history access and retention

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Experiment; missing observable business behavior |
| Proposed priority | MEDIUM |
| Exact Business source | [Source §16 history; §32 OQ-035/OQ-042/OQ-045](../decisions.md#oq-045) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-037](../decisions.md#oq-037) [OQ-042](../decisions.md#oq-042) [OQ-045](../decisions.md#oq-045); consult canonical status, no new approval |
| Concrete ambiguous scenario | A team member needs an earlier invalid run for comparison after a reset. |
| Already known | Old history/balances are preserved; invalid history is labelled; reset creates a separate experiment (proposed). |
| Not defined | Who can inspect past runs, retention commitment and required research export content are absent. |
| Why it matters | A preserved run may still be unusable for review or reproducibility. |
| Impacted objects / BS / FR / TBD | Experiment, Trade, Participant; [BS-EXP-005](../scenarios/experiment.md#bs-exp-005), [BS-EXP-006](../scenarios/experiment.md#bs-exp-006); FR-SIM-003, FR-TRD-004; TBD-005 |
| Owner decision needed / discussion options | Specify business access/retention and whether research export is required. File formats and storage mechanisms are not business decisions here. |

<a id="gap-mat-001"></a>

## [GAP-MAT-001](gap-register.md#gap-mat-001) — Equal scheduled arrivals before authoritative order exists

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Matching; missing observable business behavior |
| Proposed priority | BLOCKER |
| Exact Business source | [Source §10 unique acceptance sequence; §14 precedence; §29 delays](../decisions.md#oq-010) |
| Related approved/proposed decisions | [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | Two participants have the same scheduled virtual arrival, or a Trade-producing order ties with cancel/end. Their authoritative order has not yet been established. |
| Already known | Accepted same-price Orders follow unique acceptance sequence, never client time or size (approved). Once effective action order is known, precedence is proposed. |
| Not defined | Business tie allocation for identical scheduled arrivals is absent. |
| Why it matters | Research reruns may award different queue position, fills and fees with the same inputs. |
| Impacted objects / BS / FR / TBD | Order, Trade, Experiment; [BS-MAT-004](../scenarios/matching-stp.md#bs-mat-004), [BS-HFT-004](../scenarios/algo-hft.md#bs-hft-004), [BS-EXP-008](../scenarios/experiment.md#bs-exp-008); FR-MAT-007, FR-HFT-001; TBD-002 |
| Owner decision needed / discussion options | Select a reproducible business ordering convention for scheduled ties; sequence generation technology stays outside scope. The approved rule is not itself a tie convention for unaccepted arrivals. |

<a id="gap-hft-001"></a>

## [GAP-HFT-001](gap-register.md#gap-hft-001) — Virtual time and delay precision / limits

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | HFT; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §29 and §32 OQ-035/OQ-041](../decisions.md#oq-041) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | Two delays differ by a fractional virtual time unit near a candle or end boundary. |
| Already known | Explicit nonnegative fixed per-participant observation/action delays, including zero, are proposed. |
| Not defined | Representable time unit, precision, permitted delay maxima and off-grid treatment are absent. |
| Why it matters | Different timing granularity changes accepted order, prices and elapsed metrics. |
| Impacted objects / BS / FR / TBD | Observation, Strategy Participant, Experiment; [BS-HFT-001](../scenarios/algo-hft.md#bs-hft-001), [BS-HFT-003](../scenarios/algo-hft.md#bs-hft-003); FR-HFT-001; TBD-002 |
| Owner decision needed / discussion options | Specify business time/delay units and validation, without selecting a clock implementation. |

<a id="gap-hft-002"></a>

## [GAP-HFT-002](gap-register.md#gap-hft-002) — Which observations are delayed and how they arrive

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | HFT; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §23 own observations; §29 delay](../08-market-data.md#source-section-23) |
| Related approved/proposed decisions | [OQ-037](../decisions.md#oq-037) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | A strategy sees an old quote but a newer own fill/balance, or receives two delayed observations in a different order. |
| Already known | Observation delay affects seen market information; action delay affects arrival into authoritative order (proposed). |
| Not defined | Delivery/ordering, eligibility of old observations, own-balance/fill delays and post-end delivery are absent. |
| Why it matters | Strategies may trade on different information with identical declared delays. |
| Impacted objects / BS / FR / TBD | Market Data Observation, Balance, Strategy; [BS-HFT-001](../scenarios/algo-hft.md#bs-hft-001), [BS-HFT-007](../scenarios/algo-hft.md#bs-hft-007); FR-HFT-001, FR-MKT-003; TBD-002 |
| Owner decision needed / discussion options | Define observable delivery and information scope. Per-change, sampled or coalesced observations are discussion alternatives, not adopted requirements. |

<a id="gap-hft-003"></a>

## [GAP-HFT-003](gap-register.md#gap-hft-003) — Origin of time-to-fill metrics

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | HFT; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §29 fill timing measures](../10-algo-hft.md#source-section-29) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-041](../decisions.md#oq-041) [BR-ALGO-001](../10-algo-hft.md#br-algo-001); consult canonical status, no new approval |
| Concrete ambiguous scenario | Decision at t0, submission t1, arrival t2, acceptance t3, first fill t4 yields four possible elapsed values. |
| Already known | Metrics use experiment time; unachieved first/full fill is unavailable (proposed). |
| Not defined | Start event for first/full elapsed time is absent. |
| Why it matters | Comparisons can wrongly attribute action delay or acceptance delay to trading execution. |
| Impacted objects / BS / FR / TBD | Order, Observation; [BS-HFT-008](../scenarios/algo-hft.md#bs-hft-008); FR-HFT-004; TBD-003 |
| Owner decision needed / discussion options | Choose and name the metric origin, and whether first/full metrics share it. No numeric elapsed assertion until decided. |

<a id="gap-hft-004"></a>

## [GAP-HFT-004](gap-register.md#gap-hft-004) — Queue observation for non-resting orders

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | HFT; missing observable business behavior |
| Proposed priority | MEDIUM |
| Exact Business source | [Source §23 own active orders and queue; §29 own queue measures](../08-market-data.md#source-section-23) |
| Related approved/proposed decisions | [OQ-037](../decisions.md#oq-037) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | A fully immediate Market Order or fully aggressive Limit never rests, yet a queue-position metric is requested. |
| Already known | Same-price earlier active resting Orders define queue ahead; no guaranteed fill (proposed). |
| Not defined | Whether queue metrics apply to an incoming/non-resting or terminal Order, and which instant/history is reported, are absent. |
| Why it matters | Zero queue and unavailable queue convey different research meaning. |
| Impacted objects / BS / FR / TBD | Order Book, Order, Observation; [BS-HFT-004](../scenarios/algo-hft.md#bs-hft-004); FR-MKT-003, FR-HFT-002 |
| Owner decision needed / discussion options | Define eligibility and observation instant for queue measures. Do not invent a zero rank for a Market Order. |

<a id="gap-act-001"></a>

## [GAP-ACT-001](gap-register.md#gap-act-001) — Participant and trading-account lifecycle

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Actors; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §4 and §32 OQ-043](../01-scope-actors.md#source-section-4) |
| Related approved/proposed decisions | [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043); consult canonical status, no new approval |
| Concrete ambiguous scenario | A participant is added, removed or changes account during a run containing an active Order. |
| Already known | Accounts own capital/orders; account identity governs STP (approved); per-participant capital isolation is proposed. |
| Not defined | Creation authority, membership timing, account reassignment/removal, identity immutability and their effects on active Orders are absent. |
| Why it matters | Ownership attribution, STP and own-account cancellation can change. |
| Impacted objects / BS / FR / TBD | Participant, Trading Account, Order; [BS-EXP-001](../scenarios/experiment.md#bs-exp-001), [BS-HFT-007](../scenarios/algo-hft.md#bs-hft-007); FR-ACT-003; TBD-004 |
| Owner decision needed / discussion options | Define business membership/account actions and restrictions. Authentication and registration mechanisms are separate technical concerns. |

<a id="gap-act-002"></a>

## [GAP-ACT-002](gap-register.md#gap-act-002) — Scenario-hosted strategy versus separate strategy capital

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Actors; source tension / possible policy conflict |
| Proposed priority | HIGH |
| Exact Business source | [Source §27 scenario accounts may host strategies; §4/§32 OQ-043 separate accounts](../11-administration.md#source-section-27) |
| Related approved/proposed decisions | [OQ-036](../decisions.md#oq-036) [OQ-040](../decisions.md#oq-040) [OQ-043](../decisions.md#oq-043) [BR-ACT-002](../01-scope-actors.md#br-act-002); consult canonical status, no new approval |
| Concrete ambiguous scenario | A scenario account has seed Orders and later runs a separately identified strategy. Does the strategy use that balance pool or a separate one? |
| Already known | Scenario accounts may host experimental strategies under ordinary rules (proposed); each strategy/scenario participant has separate capital (proposed). |
| Not defined | Whether the host is the same participant or a second participant, and capital attribution, are not reconciled. |
| Why it matters | Changes capital sharing, STP identity, queue ownership and reported strategy profit. |
| Impacted objects / BS / FR / TBD | Strategy Participant, Participant, Account; [BS-HFT-007](../scenarios/algo-hft.md#bs-hft-007); FR-ACT-003, FR-SIM-004, FR-ALGO-001; TBD-004 |
| Owner decision needed / discussion options | Clarify intended participant relationship. Record this source tension explicitly; do not choose account sharing or invent a transfer. |

<a id="gap-adm-001"></a>

## [GAP-ADM-001](gap-register.md#gap-adm-001) — Who declares a run invalid for research

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Administration; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §16 and §32 OQ-045](../07-trade-settlement-fees.md#source-section-16) |
| Related approved/proposed decisions | [OQ-035](../decisions.md#oq-035) [OQ-042](../decisions.md#oq-042) [OQ-045](../decisions.md#oq-045) [BR-TRD-001](../07-trade-settlement-fees.md#br-trd-001); consult canonical status, no new approval |
| Concrete ambiguous scenario | Two reviewers disagree whether a flawed run is invalid; one seeks to remove or reverse the label. |
| Already known | Preserve invalid history, label research validity, end and use a separate run; no Trade corrections (proposed). |
| Not defined | Authorized classifier, grounds/evidence, timing and reversibility of validity labels are absent. |
| Why it matters | Research inclusion may change while financial facts must remain explainable. |
| Impacted objects / BS / FR / TBD | Experiment, Trade; [BS-EXP-006](../scenarios/experiment.md#bs-exp-006); FR-ADM-004; TBD-004/TBD-005 |
| Owner decision needed / discussion options | Define validity decision authority and label behavior. Do not create an INVALID trading lifecycle state. |

<a id="gap-adm-002"></a>

## [GAP-ADM-002](gap-register.md#gap-adm-002) — Administrative visibility over private histories

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Administration; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §4 admin responsibility; §23 participant privacy](../08-market-data.md#source-section-23) |
| Related approved/proposed decisions | [OQ-037](../decisions.md#oq-037) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043); consult canonical status, no new approval |
| Concrete ambiguous scenario | An administrator needs participant details to investigate an invalid run. |
| Already known | Participants see own details; other-account identities/individual histories are private in participant observations (proposed). |
| Not defined | Administrative business observation entitlement is absent. |
| Why it matters | Overbroad access can violate intended privacy; insufficient access can block research review. |
| Impacted objects / BS / FR / TBD | Participant, Trading Account, Observation; [BS-EXP-006](../scenarios/experiment.md#bs-exp-006); FR-ADM-004, FR-MKT-004; TBD-004 |
| Owner decision needed / discussion options | Specify admin research visibility and permitted uses; do not infer unrestricted access or a technical role model. |

<a id="gap-ord-001"></a>

## [GAP-ORD-001](gap-register.md#gap-ord-001) — Repeated submission: new intent or same request

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Orders; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §6 Order meaning; §14 repeated cancellation only](../04-orders.md#source-section-6) |
| Related approved/proposed decisions | [OQ-005](../decisions.md#oq-005) [OQ-010](../decisions.md#oq-010) [OQ-021](../decisions.md#oq-021) [OQ-031](../decisions.md#oq-031); consult canonical status, no new approval |
| Concrete ambiguous scenario | After uncertainty about acceptance, a participant repeats the same quantity/price submission. |
| Already known | Independent new Orders require funding and fresh acceptance priority (approved priority; proposed funding); repeated cancellation has no duplicate release (proposed). |
| Not defined | How to distinguish repeated observation/retry of one submission from a second trading intent, and its business result, is absent. |
| Why it matters | Two accepted Orders can lock twice the assets, execute twice or receive different priority. |
| Impacted objects / BS / FR / TBD | Order, Reservation, Trade; [BS-ORD-018](../scenarios/orders.md#bs-ord-018); FR-ORD-001, FR-BAL-003, FR-MAT-002 |
| Owner decision needed / discussion options | Choose externally observable repeated-submission behavior and how intent is distinguished; no transport/idempotency mechanism is selected. |

<a id="gap-bal-001"></a>

## [GAP-BAL-001](gap-register.md#gap-bal-001) — Invalid or partially prepared funding configuration

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Balances; missing observable business behavior |
| Proposed priority | MEDIUM |
| Exact Business source | [Source §5 setup funding; §25 seeded basis; §32 OQ-042/OQ-046](../decisions.md#oq-046) |
| Related approved/proposed decisions | [OQ-003](../decisions.md#oq-003) [OQ-016](../decisions.md#oq-016) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-033](../decisions.md#oq-033) [OQ-042](../decisions.md#oq-042) [OQ-046](../decisions.md#oq-046); consult canonical status, no new approval |
| Concrete ambiguous scenario | One of several setup allocations is off-step or lacks a seeded acquisition reference after other entries were prepared. |
| Already known | Funding grids, explicit amounts/costs/rates and setup-only funding are proposed; no implicit defaults. |
| Not defined | Whole-scenario validation/readiness, permissible allocation bounds and retain-versus-undo behavior for partial invalid preparation are absent. |
| Why it matters | A run might begin with materially different capital or missing basis. |
| Impacted objects / BS / FR / TBD | Scenario, Balance, Position; [BS-EXP-001](../scenarios/experiment.md#bs-exp-001); FR-ADM-003, FR-PF-003; TBD-001 |
| Owner decision needed / discussion options | Define invalid preparation outcome/readiness and allowed setup funding bounds. The per-order maximum is not automatically a setup balance maximum. |

<a id="gap-mkt-001"></a>

## [GAP-MKT-001](gap-register.md#gap-mkt-001) — Observation meaning across changing business state

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Market data; missing observable business behavior |
| Proposed priority | HIGH |
| Exact Business source | [Source §9 aggregated depth; §23 observations; §29 delay](../08-market-data.md#source-section-23) |
| Related approved/proposed decisions | [OQ-037](../decisions.md#oq-037) [OQ-041](../decisions.md#oq-041); consult canonical status, no new approval |
| Concrete ambiguous scenario | Depth and queue ahead are observed before a fill, while Best Ask in the same purported observation is after it. |
| Already known | Full aggregate depth, quotes, own queue and anonymous Trades are proposed; delayed observations may be old. |
| Not defined | Which business instant each observation represents and whether a grouped observation represents a consistent business state are absent. |
| Why it matters | Slippage references, queue research and strategy decisions may refer to incompatible facts. |
| Impacted objects / BS / FR / TBD | Market Data Observation, Order Book; [BS-MKT-008](../scenarios/market-data.md#bs-mkt-008), [BS-HFT-001](../scenarios/algo-hft.md#bs-hft-001); FR-MKT-002, FR-MKT-003, FR-HFT-001; TBD-002 |
| Owner decision needed / discussion options | Define business observation content/time meaning. Delivery protocol, refresh frequency and technical atomicity are separate implementation/NFR choices. |

<a id="gap-ord-002"></a>

## [GAP-ORD-002](gap-register.md#gap-ord-002) — Quantity facts for an invalid rejected request

Status: OPEN

| Review field | Detail |
|--------------|--------|
| Domain / classification | Orders; source tension / possible policy conflict |
| Proposed priority | HIGH |
| Exact Business source | [Source §6 Original quantity; §6 invalid numeric requests; §13 REJECTED; §30 ORD-INV-001/002](../invariants.md#ord-inv-001) |
| Related approved/proposed decisions | [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-019](../decisions.md#oq-019) [OQ-020](../decisions.md#oq-020) [OQ-025](../decisions.md#oq-025) [OQ-027](../decisions.md#oq-027) [OQ-028](../decisions.md#oq-028) [ORD-INV-001](../invariants.md#ord-inv-001) [ORD-INV-002](../invariants.md#ord-inv-002); consult canonical status, no new approval |
| Concrete ambiguous scenario | A request with original quantity -1 BTC or no interpretable Base quantity is REJECTED. If its original quantity remains -1 and filled is zero, the stated invariant 0 ≤ filled ≤ original cannot hold. |
| Already known | Invalid requests reject before acceptance/execution; REJECTED is proposed terminal; Original quantity and invariant meanings are preserved. |
| Not defined | Whether invalid rejected requests have the same quantity facts as accepted Orders, and which objects the every-order invariant covers, is not reconciled. |
| Why it matters | A developer could silently alter submitted quantity, fabricate a valid original amount, or violate the declared quantity/history invariant. |
| Impacted objects / BS / FR / TBD | Order / rejected request history; [BS-ORD-008](../scenarios/orders.md#bs-ord-008), [BS-ORD-019](../scenarios/orders.md#bs-ord-019); FR-ORD-012, FR-ORD-016; Requirements §6.3 lifecycle and quantity accounting |
| Owner decision needed / discussion options | Clarify quantity meaning and invariant applicability for malformed/negative rejected intents; discuss separate raw submitted facts versus valid trading quantities or an explicit invariant scope. Do not silently normalize invalid input or change preserved invariant. |

## Items deliberately not classified as new business gaps

| Item | Classification / handling |
|------|---------------------------|
| Missing Business PDF link | Documentation artifact discrepancy; see migration report. No trading policy is chosen. |
| Poor navigation or missing diagram | Missing explanation, addressed by V2 structure; does not need a new OQ. |
| Scenario amount/date/rates/strategy decisions | Explicit configuration inputs within source constraints; absence of a universal default is deliberate. Setup validity consequences are [GAP-BAL-001](gap-register.md#gap-bal-001). |
| Numeric throughput/availability/recovery targets (Requirements TBD-006) | Requirements/NFR review, outside this Business expansion; no performance values invented. |
| Sequence generation, financial execution mechanisms, persistence, delivery transport | Architecture/implementation concerns, outside authorized phase. Business arrival tie or observation meaning is reviewed independently. |
| Button wording, visual empty-value representation, chart style | UI presentation, not a business gap where the source already says unavailable or zero. |
| Adaptive strategy algorithm | Strategy research design, not a missing exchange rule; ordinary strategy participation remains proposed. |
| Deferred advanced orders, expiry or historical backtesting | Existing scope decisions, not missing trigger/expiry policy to fill in. |
