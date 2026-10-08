# Business diagram catalogue

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](../README.md) · [Scenario index](../scenarios/README.md)

Mermaid is reviewable source. Each BS points to the specific diagram and states which branch it uses. A scenario's complete inherited status can be more restrictive than a diagram's narrow core. OPEN nodes are questions, not states/events adopted by the project. Participants below are business actors/objects, not software components.

## Catalogue

| Diagram | Type | Review scope |
|---------|------|--------------|
| [DIA-ORD](#dia-ord) | stateDiagram-v2 | Order observable lifecycle; PROVISIONAL |
| [DIA-VALID](#dia-valid) | flowchart TD | Request validation and funding gate; PROVISIONAL |
| [DIA-MATCH](#dia-match) | flowchart TD | Eligible selection and type remainder; PROVISIONAL |
| [DIA-STP](#dia-stp) | flowchart TD | Account-level prevention core; APPROVED-DERIVED |
| [DIA-CANCEL](#dia-cancel) | flowchart TD | Participant cancellation result; PROVISIONAL |
| [DIA-PRECEDENCE](#dia-precedence) | sequenceDiagram | Fill / cancel precedence after order is known; PROVISIONAL |
| [DIA-RESERVE](#dia-reserve) | flowchart TD | Reservation lifecycle as financial effects; PROVISIONAL |
| [DIA-SETTLE](#dia-settle) | sequenceDiagram | Per-Trade ownership and fee conservation; PROVISIONAL |
| [DIA-ROLES](#dia-roles) | sequenceDiagram | One Limit Order changes execution role; APPROVED-DERIVED |
| [DIA-FUND](#dia-fund) | sequenceDiagram | Funding versus ordinary scenario liquidity; PROVISIONAL |
| [DIA-EXP](#dia-exp) | flowchart TD | Conditional end and separate run; PROVISIONAL with OPEN control nodes |
| [DIA-DATA](#dia-data) | flowchart TD | Trade versus non-Trade observation effects; PROVISIONAL |
| [DIA-CANDLE](#dia-candle) | sequenceDiagram | Half-open minute boundary; PROVISIONAL |
| [DIA-PF](#dia-pf) | flowchart TD | Indicative position valuation; PROVISIONAL |
| [DIA-LATENCY](#dia-latency) | sequenceDiagram | Observation / decision / action distinction; PROVISIONAL |
| [DIA-OBS](#dia-obs) | flowchart TD | Observation meaning needs a business decision; OPEN / BLOCKED |
| [DIA-METRICS](#dia-metrics) | flowchart TD | Fill metrics and unresolved elapsed origin; PROVISIONAL with OPEN timing node |

<a id="dia-ord"></a>

## [DIA-ORD](README.md#dia-ord) — Order observable lifecycle

Status: PROVISIONAL

Trace: [OQ-023](../decisions.md#oq-023) [OQ-025](../decisions.md#oq-025) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035)

```mermaid
stateDiagram-v2
[*] --> REJECTED: Validation or funding fails before acceptance
    [*] --> OPEN: Accepted Limit with no immediate fill
    [*] --> PARTIALLY_FILLED: Accepted Limit with partial fill and resting remainder
    [*] --> FILLED: Accepted Order fully executes
    [*] --> CANCELLED: Accepted Market ends unfilled remainder
    OPEN --> PARTIALLY_FILLED: Later partial execution
    OPEN --> FILLED: Later full execution
    PARTIALLY_FILLED --> PARTIALLY_FILLED: Further partial execution
    PARTIALLY_FILLED --> FILLED: Remaining quantity executes
    OPEN --> CANCELLED: Effective user cancel or STP or experiment end
    PARTIALLY_FILLED --> CANCELLED: Effective user cancel or STP or experiment end
    FILLED --> [*]
    CANCELLED --> [*]
    REJECTED --> [*]
```

Acceptance is an event, not a NEW state. Terminal exits mean lifecycle completion, not deletion of historical facts. End trigger/timing remains [GAP-EXP-001](../review/gap-register.md#gap-exp-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004); the diagram assumes end already effective. Earlier fills survive cancellation.

<a id="dia-valid"></a>

## [DIA-VALID](README.md#dia-valid) — Request validation and funding gate

Status: PROVISIONAL

Trace: [OQ-001](../decisions.md#oq-001) [OQ-004](../decisions.md#oq-004) [OQ-008](../decisions.md#oq-008) [OQ-017](../decisions.md#oq-017) [OQ-018](../decisions.md#oq-018) [OQ-019](../decisions.md#oq-019) [OQ-020](../decisions.md#oq-020) [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-027](../decisions.md#oq-027)

```mermaid
flowchart TD
A[Submitted trading intent] --> B{All applicable input and full funding gates pass?}
    B -->|No| R[REJECTED before acceptance, Trade or STP]
    B -->|Yes| C[Accepted Order enters immediate matching]
    R --> N[No rounding, resizing or self-resting cancellation]
```

This combines gates without choosing which invalid reason is evaluated or reported first. Experiment time eligibility and repeated-submission identity are [GAP-EXP-004](../review/gap-register.md#gap-exp-004)/[GAP-ORD-001](../review/gap-register.md#gap-ord-001); valid input alone does not resolve them.

<a id="dia-match"></a>

## [DIA-MATCH](README.md#dia-match) — Eligible selection and type remainder

Status: PROVISIONAL

Trace: [OQ-007](../decisions.md#oq-007) [OQ-008](../decisions.md#oq-008) [OQ-009](../decisions.md#oq-009) [OQ-010](../decisions.md#oq-010) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031)

```mermaid
flowchart TD
A[Accepted funded incoming Order] --> B{Positive unfilled quantity and eligible opposing interest?}
    B -->|Yes| C[Choose best price then earlier accepted sequence]
    C --> D{Same trading account?}
    D -->|Yes| E[Cancel resting active remainder by STP; no Trade]
    E --> B
    D -->|No| F[One Trade: smaller active quantity at resting price]
    F --> B
    B -->|No| G{Unfilled quantity remains?}
    G -->|No| H[FILLED]
    G -->|Yes| I{Limit or Market?}
    I -->|Limit| J[GTC remainder rests with original sequence]
    I -->|Market| K[CANCELLED with MARKET_REMAINDER; never rests]
```

Eligibility includes symbol, side and both unit-price boundaries. [OQ-031](../decisions.md#oq-031) pair size/one-Trade granularity and [OQ-027](../decisions.md#oq-027) state labels make the whole diagram provisional. Reservation/settlement effects use [DIA-RESERVE](README.md#dia-reserve)/[DIA-SETTLE](README.md#dia-settle), not an invented diagram component.

<a id="dia-stp"></a>

## [DIA-STP](README.md#dia-stp) — Account-level prevention core

Status: APPROVED-DERIVED

Trace: [OQ-007](../decisions.md#oq-007) [OQ-011](../decisions.md#oq-011) [OQ-012](../decisions.md#oq-012) [TRD-INV-004](../invariants.md#trd-inv-004)

```mermaid
flowchart TD
A[Next eligible resting match] --> B{Same trading account as incoming?}
    B -->|Yes| C[Cancel resting active remainder; distinguish STP]
    C --> D[No Trade, fee, volume or Last Price change for prevention]
    D --> E[Continue incoming against other eligible interest]
    B -->|No| F[Ordinary eligible matching]
    E --> G{Another eligible match?}
    G -->|Yes| A
    G -->|No| H[Limit remainder rests; Market remainder ends]
```

Approved prevention effect only. It does not assign proposed five-state labels, exact fee amounts, immediate settlement or reservation-release timing. Earlier legitimate fills remain facts. Different accounts are not grouped by human owner.

<a id="dia-cancel"></a>

## [DIA-CANCEL](README.md#dia-cancel) — Participant cancellation result

Status: PROVISIONAL

Trace: [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [ORD-INV-004](../invariants.md#ord-inv-004) [BAL-INV-003](../invariants.md#bal-inv-003)

```mermaid
flowchart TD
A[Cancellation request in known effective order] --> B{Own account target?}
    B -->|No| X[Cannot cancel target]
    B -->|Yes| C{Target outcome?}
    C -->|Active Limit remainder| D[End only active remainder: CANCELLED with USER_REQUEST]
    C -->|FILLED| E[Too late or already filled; no change]
    C -->|CANCELLED| F[Already ended; no change]
    C -->|REJECTED or nonexistent| G[No active Order to cancel]
    D --> H[Release unused reservation; preserve all earlier fills and fees]
```

An outstanding request is not effectiveness and no CANCEL_PENDING is added. Equal scheduled arrival and end boundary are not decided by this graph; see [GAP-MAT-001](../review/gap-register.md#gap-mat-001)/[GAP-EXP-004](../review/gap-register.md#gap-exp-004).

<a id="dia-precedence"></a>

## [DIA-PRECEDENCE](README.md#dia-precedence) — Fill / cancel precedence after order is known

Status: PROVISIONAL

Trace: [OQ-023](../decisions.md#oq-023) [OQ-026](../decisions.md#oq-026) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031)

```mermaid
sequenceDiagram
participant T as Order owning participant
    participant P as Exchange / Trading Platform
    participant C as Counterparty participant
    Note over T,C: Effective action order already known and scheduled ties remain OPEN
    alt Counterparty action before effective cancel
        C->>P: Eligible incoming Order
        P->>P: Complete all immediate fills and STP for that Order
        T->>P: Cancel becomes effective next
        P-->>T: End only active remainder or report already filled
    else Cancel before counterparty action
        T->>P: Own cancel becomes effective
        P->>P: End remainder and release unused reservation
        C->>P: Later incoming Order
        P-->>C: Cancelled remainder is no longer eligible
    end
    Note over T,C: Prior executed assets and fees stand with no mid-outcome interruption
```

Displayed timestamps can be equal; arrows show authoritative business order rather than physical network timing. Experiment end follows the same proposed precedence principle only when its effective order is known.

<a id="dia-reserve"></a>

## [DIA-RESERVE](README.md#dia-reserve) — Reservation lifecycle as financial effects

Status: PROVISIONAL

Trace: [OQ-021](../decisions.md#oq-021) [OQ-022](../decisions.md#oq-022) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-027](../decisions.md#oq-027) [OQ-046](../decisions.md#oq-046) [BAL-INV-002](../invariants.md#bal-inv-002)

```mermaid
flowchart TD
A{Full available funding sufficient?} -->|No| R[Reject before trading and STP]
    A -->|Yes| B[Commit required assets without changing ownership]
    B --> C{Actual fill or remainder ends?}
    C -->|Fill| D[Pay actual Trade obligations once]
    D --> E[Recompute active remainder requirement and release surplus]
    E --> F{Active remainder remains?}
    F -->|Yes| C
    F -->|No| G[Release all unused reservation]
    C -->|Remainder ends| G
    G --> H[No refund of earlier executed assets or fees]
```

Nodes are effects, not new Reservation states. The no-event condition keeps the existing commitment. Repeated cancellation/reporting does not create another release.

<a id="dia-settle"></a>

## [DIA-SETTLE](README.md#dia-settle) — Per-Trade ownership and fee conservation

Status: PROVISIONAL

Trace: [OQ-014](../decisions.md#oq-014) [OQ-015](../decisions.md#oq-015) [OQ-016](../decisions.md#oq-016) [OQ-023](../decisions.md#oq-023) [OQ-024](../decisions.md#oq-024) [OQ-046](../decisions.md#oq-046) [STL-INV-002](../invariants.md#stl-inv-002) [STL-INV-003](../invariants.md#stl-inv-003)

```mermaid
sequenceDiagram
participant B as Buyer account
    participant P as Exchange / Trading Platform
    participant S as Seller account
    participant F as Fee Treasury
    Note over B,F: One actual Trade with arrows describing ownership effects
    P-->>B: Receive full executed Base and pay notional plus buyer fee
    P-->>S: Give executed Base and receive notional minus seller fee
    P-->>F: Receive buyer fee plus seller fee
    Note over B,S: Received Base and net USDT available immediately with remainder reserve recalculated
    Note over B,F: Same Trade reported again has no second financial effect
```

No unsettled stage or failure state is added. All effects describe one coherent settled Trade; the diagram does not prescribe execution mechanisms or intermediate observable imbalance.

<a id="dia-roles"></a>

## [DIA-ROLES](README.md#dia-roles) — One Limit Order changes execution role

Status: APPROVED-DERIVED

Trace: [OQ-007](../decisions.md#oq-007) [OQ-009](../decisions.md#oq-009) [OQ-012](../decisions.md#oq-012) [OQ-013](../decisions.md#oq-013) [OQ-014](../decisions.md#oq-014)

```mermaid
sequenceDiagram
participant A as Account A BUY Limit
    participant P as Exchange / Trading Platform
    participant B as External sellers
    Note over P,B: Existing resting SELL: 1 BTC at 86000 USDT/BTC
    Note over A,B: Fixture supplies actual first execution of 1 BTC
    A->>P: Funded BUY Limit: 2 BTC at 90000 USDT/BTC
    P-->>A: Execute 1 BTC at 86000 with BUY as Taker
    Note over A,P: Remaining BUY 1 BTC rests at 90000
    B->>P: Later eligible SELL against resting BUY
    P-->>A: Execute at 90000 with same BUY now Maker
```

Role assignment and prices only; exact fees, five-state outcomes and settlement mechanics remain provisional in the composed financial scenario.

<a id="dia-fund"></a>

## [DIA-FUND](README.md#dia-fund) — Funding versus ordinary scenario liquidity

Status: PROVISIONAL

Trace: [OQ-003](../decisions.md#oq-003) [OQ-035](../decisions.md#oq-035) [OQ-036](../decisions.md#oq-036) [OQ-042](../decisions.md#oq-042) [OQ-043](../decisions.md#oq-043) [OQ-046](../decisions.md#oq-046) [SIM-INV-001](../invariants.md#sim-inv-001)

```mermaid
sequenceDiagram
participant A as Administrator
    participant S as Scenario account
    participant P as Exchange / Trading Platform
    A->>S: Explicit simulated setup capital
    Note over S,P: Funding establishes capital and the book is still empty
    Note over A,P: Start authorization/readiness is OPEN: GAP-EXP-001
    S->>P: While running: declared ordinary funded Limit
    P-->>S: Ordinary matching / STP / resting outcome
    Note over S,P: Finite inventory with no forced fill or priority privilege
```

Setup-only and while-running submission depend on proposed decisions. The funding prohibition itself is approved [OQ-003](../decisions.md#oq-003); the diagram does not promise a resting outcome when existing interest crosses the scheduled Limit.

<a id="dia-exp"></a>

## [DIA-EXP](README.md#dia-exp) — Conditional end and separate run

Status: PROVISIONAL with OPEN control nodes

Trace: [OQ-023](../decisions.md#oq-023) [OQ-027](../decisions.md#oq-027) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-039](../decisions.md#oq-039) [OQ-042](../decisions.md#oq-042) [OQ-045](../decisions.md#oq-045)

```mermaid
flowchart TD
A[Declared scenario inputs and funding] --> B{OPEN: start authority and readiness GAP-EXP-001}
    B --> C[Assume experiment is running]
    C --> D{OPEN: end trigger, duration and boundary GAP-EXP-002 / GAP-EXP-004}
    D --> E[Assume end already effective in authoritative order]
    E --> F[End all active remainders; release unused reserve]
    F --> G[Preserve balances, Trades and fees; label final partial candle if any]
    G --> H[Reset uses separate funding and history; no asset transfer]
```

OPEN diamonds are unresolved policy, not confirmed transitions. The source does not define a general pause/resume feature, required duration, manual end or automatic end; running/ended labels here are prose conditions, not new enum states.

<a id="dia-data"></a>

## [DIA-DATA](README.md#dia-data) — Trade versus non-Trade observation effects

Status: PROVISIONAL

Trace: [OQ-003](../decisions.md#oq-003) [OQ-011](../decisions.md#oq-011) [OQ-037](../decisions.md#oq-037) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039) [OQ-044](../decisions.md#oq-044)

```mermaid
flowchart TD
A[Business event] --> B{Actual Trade?}
    B -->|Yes| C[Latest execution updates Last Price; count Trade once in Base and gross Quote volume]
    C --> D[Assign by virtual UTC half-open minute and summarize actual Trades]
    B -->|No| E[No Trade volume or Last Price update]
    E --> F[Order / cancellation / STP may change depth; funding changes capital only]
    D --> G{Quote sides at supplied observation instant?}
    F --> G
    G -->|Neither| H[Best Bid, Best Ask and Spread unavailable]
    G -->|Only one| I[Present-side quote known; absent quote and Spread unavailable]
    G -->|Both| J[Best Bid and Best Ask known; Spread is Ask minus Bid]
```

No first Trade means Last Price stays unavailable. Quote branches apply to supplied book facts at a known observation instant; quotes can exist before the first Trade and a historical Last Price can exist after the book becomes empty. This is derived data content, not a chosen observation delivery or sampling policy ([GAP-MKT-001](../review/gap-register.md#gap-mkt-001)/[GAP-HFT-002](../review/gap-register.md#gap-hft-002)).

<a id="dia-candle"></a>

## [DIA-CANDLE](README.md#dia-candle) — Half-open minute boundary

Status: PROVISIONAL

Trace: [OQ-035](../decisions.md#oq-035) [OQ-038](../decisions.md#oq-038) [OQ-039](../decisions.md#oq-039)

```mermaid
sequenceDiagram
participant T as Actual Trade history
    participant A as Minute starting 10:00:00 UTC
    participant B as Minute starting 10:01:00 UTC
    T->>A: Trade at 10:00:59 UTC
    T->>B: Trade at exactly 10:01:00 UTC
    Note over A,B: Same-time first/last use authoritative execution order
    Note over A,B: Empty interval: unavailable OHLC and zero volumes
    Note over T,B: Effective end inside minute retains labelled partial final interval
```

Times illustrate virtual grouping, not experiment-duration presets. A Trade exactly at experiment end is not authorized by this candle diagram; end eligibility remains [GAP-EXP-004](../review/gap-register.md#gap-exp-004).

<a id="dia-pf"></a>

## [DIA-PF](README.md#dia-pf) — Indicative position valuation

Status: PROVISIONAL

Trace: [OQ-033](../decisions.md#oq-033) [OQ-034](../decisions.md#oq-034) [OQ-044](../decisions.md#oq-044) [OQ-046](../decisions.md#oq-046)

```mermaid
flowchart TD
A[Known balances, assigned basis and realized PnL] --> B{Nonzero Base holding to value?}
    B -->|Yes| C{Actual experiment Last Price exists?}
    C -->|No| D[Holding valuation and unrealized PnL unavailable; total equity unavailable]
    C -->|Yes| E[Use Last Price; do not deduct future sale fee]
    E --> F{Age greater than 60 virtual seconds?}
    F -->|Yes| G[Retain price; flag stale with age]
    F -->|No| H[Retain indicative price without stale flag]
    B -->|No| I[No missing Base price required for that zero holding; average cost unavailable]
```

Known balances/realized PnL stay available when valuation is missing. Exactly 60 is not greater than 60. Setup acquisition reference is not a valuation fallback. Clock progression after end remains OPEN.

<a id="dia-latency"></a>

## [DIA-LATENCY](README.md#dia-latency) — Observation / decision / action distinction

Status: PROVISIONAL

Trace: [OQ-010](../decisions.md#oq-010) [OQ-031](../decisions.md#oq-031) [OQ-035](../decisions.md#oq-035) [OQ-041](../decisions.md#oq-041)

```mermaid
sequenceDiagram
participant M as Market business facts
    participant S as Strategy participant
    participant P as Exchange / Trading Platform
    Note over M,S: Observation delay affects facts seen with delivery details OPEN GAP-HFT-002
    M-->>S: Assume a particular older quote has already been observed
    S->>S: Decision uses observed opposing quote as reference
    Note over S,P: Fixed action delay on virtual time
    S->>P: Order / cancellation reaches authoritative ordering
    P->>P: Evaluate actual eligible interest and existing priority
    P-->>S: Actual fills / final effective cancel outcome
    Note over S,P: Equal scheduled ties and end boundary remain OPEN
```

A fixture can supply known observation and effective action order. It cannot infer delivery semantics, clock precision, identical-arrival tie policy or timing metric origin.

<a id="dia-obs"></a>

## [DIA-OBS](README.md#dia-obs) — Observation meaning needs a business decision

Status: OPEN / BLOCKED

Trace: [OQ-037](../decisions.md#oq-037) [OQ-041](../decisions.md#oq-041) [GAP-MKT-001](../review/gap-register.md#gap-mkt-001) [GAP-HFT-002](../review/gap-register.md#gap-hft-002)

```mermaid
flowchart TD
A[Market and own-account facts at different virtual times] --> B{OPEN: which business instant / information scope?}
    B --> C[Pending owner decision: GAP-MKT-001]
    B --> D[Pending owner decision: GAP-HFT-002]
    C --> E[Do not assert a coherent or delivered observation without policy]
    D --> E
```

This diagram records a gap, not a fictional delivered-observation lifecycle. No coalescing, ordering, sampling, private-update delay or post-end delivery alternative is selected.

<a id="dia-metrics"></a>

## [DIA-METRICS](README.md#dia-metrics) — Fill metrics and unresolved elapsed origin

Status: PROVISIONAL with OPEN timing node

Trace: [OQ-041](../decisions.md#oq-041) [OQ-046](../decisions.md#oq-046) [GAP-HFT-003](../review/gap-register.md#gap-hft-003)

```mermaid
flowchart TD
A[Order with positive original quantity] --> B{Any actual fills?}
    B -->|No| C[Ratio zero; price, unit costs, slippage and fill times unavailable]
    B -->|Yes| D[Actual ratio, weighted execution price and fees]
    D --> E{Decision observed opposing quote known?}
    E -->|No| F[Slippage unavailable]
    E -->|Yes| G[Apply side-specific adverse slippage formula]
    D --> H{First / full fill achieved?}
    H -->|Relevant event unachieved| I[That elapsed metric unavailable]
    H -->|Relevant event achieved| J[OPEN metric origin: GAP-HFT-003]
```

With partial fills, first fill may be achieved while full fill is unachieved. This diagram does not label an achieved elapsed time zero or choose its start event.
