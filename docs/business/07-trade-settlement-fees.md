# Trades, settlement and fees

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-16"></a>

## 16. Trade

**ESTABLISHED DOMAIN CONCEPT.** A Trade is an executed exchange, not an order submission, reservation, funding grant, or prevented self-match. One BUY for `2 BTC` can produce two Trades against sells of `0.5` and `1.5 BTC`.

Its business meaning includes buyer, seller, symbol, Base quantity, execution price, occurrence time/order, participating orders, maker/taker roles, and each side's fee. These are conceptual facts, not a storage or interface definition. Public market observations omit counterparty identities under **proposed [OQ-037](decisions.md#oq-037)**.

**APPROVED — [OQ-009](decisions.md#oq-009), [OQ-014](decisions.md#oq-014):** every Trade uses its resting order's price and independently identifies the resting side as Maker and incoming side as Taker. Both sides agree on quantity and price, although their fees may differ.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-045](decisions.md#oq-045):** confirmed Trades cannot be edited, deleted, reversed, or exceptionally corrected in V1. If an experiment is invalid, preserve and label its history invalid for research, end it, and start a separate experiment. Administrative funding cannot disguise a Trade correction.

<a id="source-section-18"></a>

## 18. Trade Settlement

**ESTABLISHED DOMAIN CONCEPT.** Settlement fulfills each Trade's ownership exchange. **PROPOSED — OWNER REVIEW REQUIRED — [OQ-015](decisions.md#oq-015), [OQ-023](decisions.md#oq-023), [OQ-024](decisions.md#oq-024), [OQ-046](decisions.md#oq-046):** settle immediately per Trade. Received Base and net USDT proceeds become available immediately; there is no pending-settlement balance category.

The buyer gives notional plus buyer fee in USDT and receives the full Base quantity. The seller gives that Base quantity and receives notional minus seller fee. Both fees go to a dedicated, non-trading simulated **Fee Treasury** owned by the platform within the experiment; it starts at zero and is not strategy profit.

Complete example: Alice has `200,000 USDT`, Bob `3 BTC`, treasury `0`. Bob first rests two sells, `0.5 BTC` and `1.5 BTC`, both at `86,000`. Alice submits BUY LIMIT `2 BTC @ 90,000`. Example rates: Maker `0.1%`, Taker `0.2%`.

| Stage | Account | Available USDT | Locked USDT | Available BTC | Locked BTC |
|-------|---------|----------------|-------------|---------------|------------|
| Before orders | Alice | `200,000` | `0` | `0` | `0` |
| Before orders | Bob | `0` | `0` | `3` | `0` |
| After reservations | Alice | `19,640` | `180,360` | `0` | `0` |
| After reservations | Bob | `0` | `0` | `1` | `2` |
| After first Trade: `0.5 BTC` | Alice | `21,644` | `135,270` | `0.5` | `0` |
| After first Trade | Bob | `42,957` | `0` | `1` | `1.5` |
| After first Trade | Fee Treasury | `129` | `0` | `0` | `0` |
| After second Trade: `1.5 BTC` | Alice | `27,656` | `0` | `2` | `0` |
| After second Trade | Bob | `171,828` | `0` | `1` | `0` |
| After second Trade | Fee Treasury | `516` | `0` | `0` | `0` |

Trade 1 has notional `43,000`, buyer fee `86`, seller fee `43`. Trade 2 has notional `129,000`, buyer fee `258`, seller fee `129`. Final USDT conservation: `27,656 + 171,828 + 516 = 200,000`; BTC conservation: `2 + 1 = 3`. After only Trade 1, USDT also reconciles: `21,644 + 135,270 + 42,957 + 129 = 200,000`.

Partial execution settles only executed quantity. Reservation release is neither a transfer to the counterparty nor income. Each Trade settles once. Conserving asset units does not mean a portfolio's market valuation cannot change. Simulation Funding is separately identified when explaining experiment-wide totals.

<a id="source-section-26"></a>

## 26. Fees

**PROJECT V1 RULE — APPROVED — [OQ-013](decisions.md#oq-013), [OQ-014](decisions.md#oq-014).** V1 supports configurable Maker/Taker fees, including zero. Classification is **per Trade**: resting order = Maker; incoming order = Taker. Rates may be equal or different. Fees affect funding, reservations, settlement, balances, and reported performance.

Example: resting SELL `1 BTC @ 86,000`; incoming BUY LIMIT `2 BTC @ 90,000`. Trade 1 is `1 BTC @ 86,000`: BUY Taker, SELL Maker. The BUY's remaining `1 BTC` rests at `90,000`. A later incoming SELL executes against it at `90,000`: that same BUY is now Maker. A Limit Order is not inherently a maker for all its fills.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-015](decisions.md#oq-015), [OQ-016](decisions.md#oq-016):** both fees are in USDT and credited to the Fee Treasury. For each side of each Trade, fee = notional × its applicable rate, **rounded down to 8 USDT decimal places**. No minimum fee, rebates, discounts, separate fee token, or per-order fee aggregation. A tiny fee may round to zero; there is no hidden rounding charge. Each side loses exactly what the treasury receives.

Require explicit maker and taker rates at experiment setup, each from `0%` through `1%`, in increments of `0.01` percentage point (`0.0001` as a fraction). Rates remain fixed through the experiment, protecting existing reservations. Zero rates are allowed; there is no implicit fee default.

For notional `86,000`, maker `0.1%` gives fee `86`; taker `0.2%` gives `172 USDT`. If seller is Maker and buyer Taker, buyer pays `86,172`, seller receives `85,914`, treasury receives `258`. With Quote fees capped at `1%`, seller fees can always be paid from proceeds. Exact decimal conventions and reserve rounding are in sections 6, 12, and [OQ-046](decisions.md#oq-046).

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-trd-001"></a>BR-TRD-001 | Preserve Trades without editing, deletion, reversal, or correction in V1. | Preserve explainable experiment history. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-045](decisions.md#oq-045) |
| <a id="br-stl-001"></a>BR-STL-001 | Settle each Trade immediately once, including fees; unfilled quantities create no transfer. | Consistent usable ownership. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-024](decisions.md#oq-024), [OQ-046](decisions.md#oq-046) |
| <a id="br-fee-001"></a>BR-FEE-001 | Configure Maker/Taker rates, including zero; classify resting/incoming per Trade. | An order can change roles between Trades. | APPROVED | [OQ-013](decisions.md#oq-013), [OQ-014](decisions.md#oq-014) |
| <a id="br-fee-002"></a>BR-FEE-002 | Charge each side in USDT to Fee Treasury; floor each fee to 8 decimals, no minimum/rebate; fix bounded rates per experiment. | Exact, fundable fee obligations. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-015](decisions.md#oq-015), [OQ-016](decisions.md#oq-016) |

## Phase B — behavioral analysis

### Purpose, actors and business facts

Trade is actual exchange; settlement fulfills it; Fee is a per-side obligation of one execution; Fee Treasury receives the proposed USDT charges. Neither intent, funding nor prevention creates a Trade. Buyer/seller roles differ from Maker/Taker roles, which are assigned per Trade.

| Object | Creation / ownership / attributes | Lifecycle / preserved facts |
|--------|----------------------------------|-----------------------------|
| Trade | Eligible different-account exchange: buyers/sellers, Orders, symbol, Base quantity, resting price, occurrence time/order, maker/taker, fees | Pair boundary proposed [OQ-031](decisions.md#oq-031); settlement once; immutable confirmed facts under [OQ-045](decisions.md#oq-045); no pending/corrected/void states |
| Fee | Each side of actual Trade; notional, applicable role/rate, exact USDT rounded amount | Zero configured/rounded charge allowed; no Order placement/cancel/STP fee; no rebates/minimum under [OQ-016](decisions.md#oq-016) |
| Fee Treasury | Platform-owned, per-experiment, non-trading simulated recipient, initially zero ([OQ-015](decisions.md#oq-015)) | Each Trade adds both exact fees; no strategy PnL; old receipts retained on end/reset, not transferred to fresh run |

Canonical formulas/rates/rounding and complete two-Trade account ledger are above (§18/§26) and [OQ-015](decisions.md#oq-015)/[OQ-016](decisions.md#oq-016)/[OQ-046](decisions.md#oq-046). No second financial formula is adopted here. A reported Trade remains the same occurrence: [STL-INV-003](invariants.md#stl-inv-003)/[TRD-INV-003](invariants.md#trd-inv-003) prohibit duplicate transfer or duplicate filled quantity.

### Preconditions, flow and postconditions

Full obligation reservation precedes trading under [OQ-021](decisions.md#oq-021)/[OQ-022](decisions.md#oq-022). Approved [OQ-009](decisions.md#oq-009)/[OQ-014](decisions.md#oq-014) establish resting execution price and per-Trade roles. Proposed [OQ-024](decisions.md#oq-024) makes received Base and net USDT immediately available without an unsettled stage. Only actual executed quantity transfers. Fees arrive exactly where the charged account units leave; Trading conserves each asset across participants plus treasury, with funding separately accounted ([STL-INV-002](invariants.md#stl-inv-002)).

### Decision table — occurrence × financial consequences

| Initial business condition | Trigger / condition | Outcome | Resulting state / financial consequences | Basis | Status |
|----------------------------|---------------------|---------|------------------------------------------|-------|--------|
| Eligible funded pair | Actual Trade | Transfer Base / notional / both fees once | Filled totals accumulate; receipts available; remainder reserve recomputed | [OQ-015](decisions.md#oq-015)/[OQ-016](decisions.md#oq-016)/[OQ-023](decisions.md#oq-023)/[OQ-024](decisions.md#oq-024)/[OQ-031](decisions.md#oq-031)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Zero rates or tiny positive fee | Trade occurs; fee floors to zero | Trade still exists; side fee zero | No minimum/hidden charge; treasury receives exact zero for that side | [OQ-013](decisions.md#oq-013)/[OQ-016](decisions.md#oq-016)/[OQ-046](decisions.md#oq-046) | PROVISIONAL exact fee |
| Self-owned eligible pair | STP | No Trade for pair | No fee/volume/Last Price; cancellation changes reserve under proposal | [OQ-011](decisions.md#oq-011)/[OQ-023](decisions.md#oq-023) | PROVISIONAL complete effect; STP core APPROVED |
| No actual execution | Placement/reservation/user cancel | No settlement of unexecuted quantity | No Trade fee; unused reserve remains/releases as applicable | Source §16/§18; [OQ-023](decisions.md#oq-023)/[OQ-024](decisions.md#oq-024) | PROVISIONAL complete flow |
| Confirmed settled Trade | Observe/report same Trade | Same fact only | No second units/fee/filled count transfer | [STL-INV-003](invariants.md#stl-inv-003)/[TRD-INV-003](invariants.md#trd-inv-003); [OQ-024](decisions.md#oq-024) | PROVISIONAL |
| Confirmed Trade in invalid research run | Seek edit/reverse/delete/correction | Prohibited in V1 | Preserve fact and research label; separate fresh run | [OQ-045](decisions.md#oq-045) | PROVISIONAL; label authority [GAP-ADM-001](review/gap-register.md#gap-adm-001) |

### No action, boundaries and failures

Without a Trade there is no trading ownership transfer or fee. A cancelled partly filled Order keeps its prior fees and received assets. Partial fills can change roles when a Limit remainder later rests; reserve must cover that possibility. Fee flooring is per Trade/per side, never aggregated per Order. Rounding a display cannot change settled price, obligation or treasury units.

An underfunded request fails before a Trade/STP under [OQ-021](decisions.md#oq-021); the source does not invent a “failed settlement” normal business state or recovery/correction branch. Conditions violating conservation are invariant violations to report, not authority to manufacture a replacement Trade. Time ordering of occurrence and end is governed by source §14/[OQ-031](decisions.md#oq-031), with scheduled/end boundary decisions [GAP-MAT-001](review/gap-register.md#gap-mat-001)/[GAP-EXP-004](review/gap-register.md#gap-exp-004).

Review [BS-TRD-001](scenarios/balance-settlement.md#bs-trd-001)–[BS-TRD-004](scenarios/balance-settlement.md#bs-trd-004) and [BS-FEE-001](scenarios/balance-settlement.md#bs-fee-001)–[BS-FEE-002](scenarios/balance-settlement.md#bs-fee-002), [DIA-SETTLE](diagrams/README.md#dia-settle) and [DIA-ROLES](diagrams/README.md#dia-roles). Other modules derive data, positions and research outcomes from these same facts. Exactly-once is a business effect; this text selects no technical mechanism.
