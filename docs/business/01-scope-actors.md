# Scope and actors

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-2"></a>

## 2. Product Domain Overview

**ESTABLISHED DOMAIN CONCEPT.** A trading platform connects buyers and sellers under defined rules:

**Trader → Order → Order Book → Matching → Trade → Settlement → Market Data / Portfolio**

This is a business flow, not a software architecture. An Order expresses intention; a Trade records actual execution. Rejection, cancellation, and lack of eligible counterparties can result in no Trade. A Market Order consumes resting liquidity and never becomes a resting order in V1.

Example: Bob's resting SELL offers `1 BTC @ 86,000`. Alice's incoming BUY LIMIT accepts up to `90,000`. They trade `1 BTC` for `86,000 USDT` before fees. Settlement transfers the assets; market history reflects the execution.

**APPROVED — [OQ-001](decisions.md#oq-001)–[OQ-014](decisions.md#oq-014):** V1 is a funded simulated spot exchange with three markets, protected Market Orders, GTC Limit Orders, deterministic matching, self-trade prevention, and configurable per-Trade Maker/Taker fees.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-040](decisions.md#oq-040), [OQ-041](decisions.md#oq-041):** V1 includes strategy participation and controlled queue-position, execution-quality, and simulated timing experiments. Historical-data backtesting is deferred.

<a id="source-section-3"></a>

## 3. Product Boundary

**PROJECT V1 RULE — APPROVED — [OQ-001](decisions.md#oq-001), [OQ-002](decisions.md#oq-002), [OQ-004](decisions.md#oq-004), [OQ-005](decisions.md#oq-005), [OQ-006](decisions.md#oq-006), [OQ-012](decisions.md#oq-012).** V1 supports only BTC/USDT, ETH/USDT, SOL/USDT; simulated BTC, ETH, SOL, USDT; funded spot trading; Limit and Market Orders; GTC for Limit; immediate-only Market execution.

Outside V1: real money, real cryptocurrency custody/ownership/redemption, bank transfers, blockchain deposits/withdrawals/settlement, real brokerage connectivity, external market-data integration, and real regulatory-compliance implementation. No production HFT claim is made.

Borrowing, leverage, margin, short selling, derivatives, perpetuals, futures, options, additional Quote Assets, cross pairs, Quote-budget Market Buy, explicit IOC/FOK, and advanced orders are deferred beyond V1. Advanced orders include Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged, and other conditional orders.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-028](decisions.md#oq-028), [OQ-029](decisions.md#oq-029), [OQ-030](decisions.md#oq-030), [OQ-040](decisions.md#oq-040), [OQ-045](decisions.md#oq-045):** amendments, combined cancel-and-replace, expiry, historical backtesting, and Trade corrections are also deferred. Separate cancellation followed by a new order remains available. Future extensibility does not authorize those features now.

<a id="source-section-4"></a>

## 4. Business Actors

These are business responsibilities, not technical access-control roles.

| Actor | Objective and activities | Decision basis |
|-------|--------------------------|----------------|
| Human Trader | Observe the market, place funded orders, cancel own active remainders, inspect outcomes. | Approved trading rules; recommended observations in [OQ-033](decisions.md#oq-033), [OQ-037](decisions.md#oq-037). |
| Algorithmic Strategy | Make automated trading decisions using market information and assigned capital. | Proposed [OQ-040](decisions.md#oq-040), [OQ-043](decisions.md#oq-043). |
| Exchange / Trading Platform | Apply matching rules, establish Trades, account for settlement, and describe market activity. | Approved matching rules; proposed settlement/data details. |
| Administrator | Configure experiments and allocate simulated funds; never manufacture Trades to satisfy orders. | Approved [OQ-003](decisions.md#oq-003); proposed experiment controls [OQ-042](decisions.md#oq-042). |
| Liquidity / Scenario Account | Supply funded trading interest through ordinary orders. | Approved [OQ-003](decisions.md#oq-003); proposed participation model [OQ-036](decisions.md#oq-036), [OQ-043](decisions.md#oq-043). |

**APPROVED — [OQ-003](decisions.md#oq-003), [OQ-005](decisions.md#oq-005), [OQ-010](decisions.md#oq-010), [OQ-011](decisions.md#oq-011):** funded accounts receive no priority, funding, or self-trade exception because an administrator created them.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-043](decisions.md#oq-043):** each manual participant, strategy, and liquidity scenario uses a separate trading account and balance pool within an experiment. No shared strategy capital or transfers between accounts in V1. The account-level self-trade rule does not group distinct accounts by human owner.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-act-001"></a>BR-ACT-001 | Administratively funded accounts use the ordinary funding, matching, and prevention rules. | No privileged forced counterparties. | APPROVED | [OQ-003](decisions.md#oq-003), [OQ-005](decisions.md#oq-005), [OQ-010](decisions.md#oq-010), [OQ-011](decisions.md#oq-011) |
| <a id="br-act-002"></a>BR-ACT-002 | Give each participant a separate funded account; seed initial depth with ordinary scenario Limit Orders. | Finite, accountable liquidity and capital. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-036](decisions.md#oq-036), [OQ-043](decisions.md#oq-043) |

## Phase B — behavioral analysis

### Purpose, actors and boundaries

This module attributes intent, capital and responsibility. It does not define registration or technical permissions. Read the migrated actor table before interpreting ownership. Approved Simulation Funding authority is distinct from proposed setup-only controls. [OQ-043](decisions.md#oq-043) narrows capital arrangements without changing approved account-level STP.

### Objects, attributes and ownership

| Object | Meaning / relevant attributes | Beginning / owner | Lifecycle and immutable facts |
|--------|-------------------------------|-------------------|-------------------------------|
| Participant | Human, strategy or scenario trading actor; trading-account association and assigned capital matter to its decisions | How membership is created/removed is [GAP-ACT-001](review/gap-register.md#gap-act-001) | No named participant states; reassignment during a run is undecided |
| Trading Account | Trading identity owning asset balances and Orders; account identity determines own cancellation and STP | Administrator may fund dedicated accounts ([OQ-003](decisions.md#oq-003)); exact creation authority/timing is [GAP-ACT-001](review/gap-register.md#gap-act-001) | Isolated pool per participant proposed in [OQ-043](decisions.md#oq-043); membership/identity lifecycle is undecided |
| Human Trader | Observes, submits, cancels own active interest and inspects results | Acts using its account | Intent is not execution; observations do not grant ownership |
| Strategy Participant | Automated participant using ordinary Orders and assigned capital | Proposed [OQ-040](decisions.md#oq-040)/[OQ-043](decisions.md#oq-043); see Algo/HFT | Hosting inside a scenario account conflicts with a separate identity interpretation: [GAP-ACT-002](review/gap-register.md#gap-act-002) |

No participant/account lifecycle diagram is asserted: the source defines responsibilities, not formal states or all creation/removal transitions. Experiment end preserves balances/history under proposed [OQ-035](decisions.md#oq-035)/[OQ-042](decisions.md#oq-042); it does not define account deletion or reassignment.

### Actions, preconditions and postconditions

An actor's funded account participates under ordinary market/type/funding/priority/STP rules ([OQ-003](decisions.md#oq-003)/[OQ-005](decisions.md#oq-005)/[OQ-010](decisions.md#oq-010)/[OQ-011](decisions.md#oq-011)). Funding can establish capital without Trade facts. A separate human owner does not change two distinct trading-account identities into a same-account match. The own-account cancellation outcome remains proposed source §14/[OQ-031](decisions.md#oq-031).

### Decision table — actor identity and authority

| Initial situation | Trigger / condition | Observable outcome | State / financial effect | Basis | Status |
|-------------------|---------------------|--------------------|--------------------------|-------|--------|
| Dedicated account funded by admin | Ordinary valid Order | Same matching/funding/STP principles as trader | No priority exemption | [BR-ACT-001](01-scope-actors.md#br-act-001) | APPROVED |
| Same account owns opposing eligible Orders | Match considered | Cancel resting remainder; incoming continues | No self-Trade/fee | [OQ-011](decisions.md#oq-011); [TRD-INV-004](invariants.md#trd-inv-004) | APPROVED |
| Distinct accounts share human owner | Eligible opposite interest | Human grouping does not invoke account-level STP | Ordinary matching remains applicable | [OQ-011](decisions.md#oq-011) | APPROVED |
| Participant references another account Order | Participant cancel | Cannot cancel that Order | Target stays unchanged by this request | Source §14; [OQ-031](decisions.md#oq-031) | PROVISIONAL |
| Scenario hosts separately identified strategy | Capital use | UNDECIDED — [GAP-ACT-002](review/gap-register.md#gap-act-002) | Pool attribution / STP identity undecided | [OQ-036](decisions.md#oq-036)/[OQ-040](decisions.md#oq-040)/[OQ-043](decisions.md#oq-043) | OPEN |
| Account identity changes with active Orders | Membership action | UNDECIDED — [GAP-ACT-001](review/gap-register.md#gap-act-001) | Ownership/lifecycle effects undecided | Source §4 | OPEN |

### No action, time, scenarios and dependencies

Without Orders, capital is not execution or liquidity ([OQ-003](decisions.md#oq-003)). No inactivity-driven participant state is sourced. Membership timing and end-history access remain [GAP-ACT-001](review/gap-register.md#gap-act-001)/[GAP-EXP-005](review/gap-register.md#gap-exp-005). Review [BS-EXP-001](scenarios/experiment.md#bs-exp-001), [BS-STP-005](scenarios/matching-stp.md#bs-stp-005) and [BS-HFT-007](scenarios/algo-hft.md#bs-hft-007). Read Markets, Orders, Balance and Administration together when a task crosses account ownership. Diagrams: [DIA-FUND](diagrams/README.md#dia-fund) and [DIA-STP](diagrams/README.md#dia-stp) explain existing effects; they do not fill the missing membership model.
