# Markets and assets

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-5"></a>

## 5. Assets and Trading Symbols

**ESTABLISHED DOMAIN CONCEPT.** In a Trading Pair, the **Base Asset** is bought/sold and the **Quote Asset** pays for it. **Notional** is Base quantity multiplied by price, before fees.

| Approved symbol | Base | Quote | Example before fees |
|-----------------|------|-------|---------------------|
| BTC/USDT | BTC | USDT | BUY `0.25 BTC @ 80,000` spends `20,000 USDT`. |
| ETH/USDT | ETH | USDT | BUY `2 ETH @ 3,000` spends `6,000 USDT`. |
| SOL/USDT | SOL | USDT | SELL `10 SOL @ 150` receives `1,500 USDT`. |

**APPROVED — [OQ-001](decisions.md#oq-001), [OQ-002](decisions.md#oq-002):** these are the exact V1 markets and four simulated assets. Familiar symbols imply no real cryptocurrency rights. USDT is the common Quote; cross pairs and external venues/data are beyond V1.

**APPROVED — [OQ-003](decisions.md#oq-003):** normal accounts receive configurable initial USDT and may initially have zero BTC/ETH/SOL. Administrators can allocate approved simulated assets to dedicated liquidity/scenario accounts. This is **Simulation Funding**, not a Trade, volume, profit, or execution. Liquidity appears only when funded accounts submit ordinary orders.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-042](decisions.md#oq-042):** funding occurs only during experiment setup; normal accounts start with the configured USDT and zero Base assets. Dedicated scenario accounts may receive Base assets. Configuration states the amounts explicitly; no universal starting balance is assumed. Section 25 explains the non-profit cost basis of seeded assets.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-mkt-003"></a>BR-MKT-003 | Support exactly BTC/USDT, ETH/USDT, SOL/USDT using simulated BTC/ETH/SOL/USDT only. | Explicit market boundary. | APPROVED | [OQ-001](decisions.md#oq-001), [OQ-002](decisions.md#oq-002) |
| <a id="br-prec-001"></a>BR-PREC-001 | Enforce section 6's price/quantity bounds; all assets have 8-decimal precision; apply section 32's rounding rules. | Deterministic amounts without unexplained loss. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-017](decisions.md#oq-017), [OQ-018](decisions.md#oq-018), [OQ-019](decisions.md#oq-019), [OQ-020](decisions.md#oq-020), [OQ-046](decisions.md#oq-046) |

## Phase B — behavioral analysis

### Purpose and objects

The three markets determine compatible Base/Quote obligations. This is a fixed simulated asset boundary, not an external price feed or real cryptocurrency entitlement. Human, strategy and scenario accounts use identical units.

| Object | Attributes and meaning | Beginning / changes / end |
|--------|------------------------|---------------------------|
| Market | Symbol, Base, Quote, own book and Trade-derived observations | Exactly three supported symbols ([OQ-001](decisions.md#oq-001)); no independent listing/halt/session lifecycle is specified or added |
| Asset | Simulated unit owned by an account; BTC/ETH/SOL/USDT only | Funding establishes units ([OQ-003](decisions.md#oq-003)); settlement redistributes them; no real deposit/redemption |
| Numeric grid | Price ticks/range, Base quantity step/min/max and asset precision | Proposed [OQ-017](decisions.md#oq-017)–[OQ-020](decisions.md#oq-020)/[OQ-046](decisions.md#oq-046); no inference from a real venue |

Formal Market state transitions are not applicable to the specified fixed universe: the source defines no market creation, delisting, halting or expansion action. Trading follows the experiment's running boundary. Book empty/one-sided is a condition, not a new Market lifecycle state.

### Preconditions and validation

The authoritative market list is migrated above. Canonical Order bounds are [source §6](04-orders.md#source-section-6). Exact asset precision, setup allocation steps and rounding policies are canonical [OQ-046](decisions.md#oq-046). Original maximum **Order** quantity is not a maximum balance or a setup funding allocation cap; the latter remains [GAP-BAL-001](review/gap-register.md#gap-bal-001).

### Decision table — scope and numeric gates

| Initial request | Check / boundary | Outcome | State / financial effects | Basis | Status |
|-----------------|------------------|---------|---------------------------|-------|--------|
| Named supported market | Same symbol/opposite interest, valid funded Order | Eligible to reach ordinary matching | No scope exemption | [OQ-001](decisions.md#oq-001)/[OQ-004](decisions.md#oq-004)/[OQ-005](decisions.md#oq-005) | APPROVED scope gate |
| Unsupported symbol / real asset operation | Outside exact V1 universe | Outside V1; cannot authorize a Trade | Detailed request lifecycle follows Orders | [OQ-001](decisions.md#oq-001)/[OQ-002](decisions.md#oq-002) | APPROVED prohibition |
| Price at inclusive minimum/maximum | On tick; other gates pass | Price gate passes | Acceptance is not guaranteed by one gate | [OQ-017](decisions.md#oq-017) | PROVISIONAL |
| Zero/negative/off-step quantity; off-tick/out-of-range price; above maximum Order size | Any applicable numeric gate fails | Reject before trading; never round/resize | REJECTED under [OQ-027](decisions.md#oq-027); no Trade | [OQ-017](decisions.md#oq-017)–[OQ-020](decisions.md#oq-020)/[OQ-027](decisions.md#oq-027) | PROVISIONAL |
| One-step Order/fill/remainder | Positive valid step; other gates pass | No extra notional/fill/remainder minimum | Remainder follows type rule | [OQ-018](decisions.md#oq-018)/[OQ-019](decisions.md#oq-019) | PROVISIONAL |
| Setup funding exceeds an Order size bound | Allocation, not Order | UNDECIDED allocation bounds — [GAP-BAL-001](review/gap-register.md#gap-bal-001) | Do not apply a per-Order cap to capital | [OQ-046](decisions.md#oq-046) | OPEN |

### No action, boundaries and dependencies

Funding alone produces no market activity/price ([SIM-INV-001](invariants.md#sim-inv-001)). Time changes neither the fixed asset universe nor ownership by itself. Conservation concerns each asset's **units**, while indicative USDT valuation can change. Read [BS-ORD-008](scenarios/orders.md#bs-ord-008) for exact grid/boundary examples and [BS-EXP-001](scenarios/experiment.md#bs-exp-001) for setup. [DIA-VALID](diagrams/README.md#dia-valid) applies gates without claiming gate evaluation order or error-message precedence. This module depends on Orders for requests, Balance for funding and Market Data for observations; no separate Market lifecycle diagram is warranted.
