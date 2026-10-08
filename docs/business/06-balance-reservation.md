# Balances and reservations

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-12"></a>

## 12. Balance and Asset Reservation

**ESTABLISHED DOMAIN CONCEPT.** Available Balance can fund new commitments. Locked/Reserved Balance is still owned but committed. Reservation alone does not transfer ownership. With the proposed immediate settlement model, each asset's total is Available plus Locked.

**APPROVED — [OQ-005](decisions.md#oq-005), [OQ-013](decisions.md#oq-013):** trading is fully funded and fees must be included. **Assets reserved for one active order cannot simultaneously fund another active order**, including across all three markets sharing USDT.

### Recommended reservation policy

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-015](decisions.md#oq-015), [OQ-016](decisions.md#oq-016), [OQ-021](decisions.md#oq-021), [OQ-022](decisions.md#oq-022), [OQ-023](decisions.md#oq-023), [OQ-024](decisions.md#oq-024), [OQ-046](decisions.md#oq-046).** Fees are in USDT. Buyers pay notional plus fee; seller fees come from sale proceeds. Let `q` be the active Base quantity, `b` the buy boundary, and `rM`/`rT` the experiment's maker/taker rates as fractions. Round a required USDT reservation **up to 8 decimal places**:

| Order | Required reservation |
|-------|----------------------|
| BUY LIMIT | `q × limit price × (1 + max(rM, rT))`, rounded up. |
| MARKET BUY | `q × maximum execution price × (1 + rT)`, rounded up. |
| SELL LIMIT or MARKET SELL | `q` Base units; no extra USDT reservation because the fee is deducted from proceeds. |

Rates are nonnegative, at most `1%`, and fixed during an experiment (section 26). A Limit Buy therefore remains funded if it first takes and later makes, even when maker fees exceed taker fees. Full reservation is required before any trading or self-trade-prevention effects; insufficient available funds cause rejection, never automatic downsizing. A self-cancellation expected later cannot pre-fund the incoming order.

After every fill, pay its actual cost, recompute the requirement for active remaining quantity, and release surplus immediately. When an order ends, release all its unused reservation. Section 26's downward per-Trade fee rounding prevents extra fees from fragmenting an order beyond its conservative reservation.

### BUY examples

**Zero-fee scenario:** `100,000 USDT` initially; BUY LIMIT `1 BTC @ 80,000` reserves `80,000`, leaving `20,000` available. An immediate `0.4 BTC` fill against a resting Ask at `75,000` spends `30,000`. Keep `48,000` for the remaining `0.6 BTC`; release `2,000`. Now available is `22,000`, locked is `48,000`, total is `70,000 USDT`; BTC received is `0.4`. Cancelling the remainder makes all `70,000 USDT` available. Released surplus is unused funding, not profit.

**Fee-enabled example:** maker `0.1%`, taker `0.2%` are illustrative scenario settings. The same buy reserves `80,160`, leaving `19,840`. Its immediate `30,000 USDT` Trade costs an additional `60` taker fee. The remaining reserve is `48,096`; surplus release is `2,004`. Available becomes `21,844`, locked `48,096`, total `69,940 USDT`. These rates are not mandated defaults.

A Market Buy `2 BTC`, maximum `90,000`, taker `0.2%`, requires `180,360 USDT` even when visible Asks would cost less. The maximum is a unit-price boundary; the extra `360` funds fees, not permission to buy above `90,000`.

### SELL example

With `3 BTC`, SELL LIMIT `2 BTC @ 86,000` reserves `2`, leaving `1 BTC` available. A subsequent sale of `0.5 BTC` at the resting sell's `86,000` price leaves `1` available and `1.5 BTC` locked. At a maker rate of `0.1%`, gross proceeds `43,000` minus fee `43` give `42,957 USDT`. Cancelling the remainder leaves `2.5 BTC` available; neither the sold `0.5 BTC` nor its fee is restored.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-bal-001"></a>BR-BAL-001 | Trade only fully funded simulated spot assets; no credit, leverage, shorting, or derivatives. | Keep ownership obligations funded. | APPROVED | [OQ-002](decisions.md#oq-002), [OQ-005](decisions.md#oq-005) |
| <a id="br-bal-002"></a>BR-BAL-002 | Apply section 12's fee-inclusive buy reservations and Base-only sell reservations. | Fund protected quantity and maker/taker changes. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-015](decisions.md#oq-015), [OQ-016](decisions.md#oq-016), [OQ-022](decisions.md#oq-022), [OQ-046](decisions.md#oq-046) |
| <a id="br-bal-003"></a>BR-BAL-003 | Recompute reservation and release surplus after every Trade; release unused funds at termination. | Keep available funds aligned with obligations. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-023](decisions.md#oq-023), [OQ-024](decisions.md#oq-024) |
| <a id="br-bal-004"></a>BR-BAL-004 | Reject insufficient full funding before acceptance or STP; never silently resize. | Prevent unpayable commitments. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-021](decisions.md#oq-021) |

## Phase B — behavioral analysis

### Purpose, objects, ownership and attributes

The account commits assets before trading, then pays only actual exchanges and retains unused ownership. USDT availability is shared across all three symbols. A reservation is a commitment of that account's capital, not a separate owner or a Trade.

| Object | Meaningful attributes / beginning | Events, end and unchanged facts |
|--------|----------------------------------|----------------------------------|
| Balance | Account, asset, Available, Locked; under proposed immediate settlement Total = Available + Locked | Funding creates capital; reservation/release changes usability; settlement changes owned units; no separate pending-settlement category |
| Reservation | Owning account/Order, committed asset/amount, active quantity/boundary/fee obligations | Required before trading/STP under [OQ-021](decisions.md#oq-021)/[OQ-022](decisions.md#oq-022); fills consume/recompute, surplus releases, termination releases all unused; no independent named states |

“Committed”, “consumed” and “released” describe business effects, not new reservation enum states. Re-observation does not recreate funding or settlement. Old balances survive experiment end/reset in old history; transfers to a fresh run are prohibited by proposed [OQ-042](decisions.md#oq-042). Account identity lifecycle is [GAP-ACT-001](review/gap-register.md#gap-act-001).

### Preconditions and postconditions

Canonical formulas and worked BUY/SELL examples are in source §12 above; [OQ-046](decisions.md#oq-046) defines exact decimal rounding. Acceptance requires the **full** request obligation even when visible eligible liquidity is smaller or cheaper ([OQ-021](decisions.md#oq-021)). A later STP release cannot rescue currently insufficient Available funds. Rate stability and the larger maker/taker Limit rate cover an Order changing role ([OQ-014](decisions.md#oq-014)/[OQ-016](decisions.md#oq-016)/[OQ-022](decisions.md#oq-022)).

### Transition / decision table — funds × acceptance × STP

| Initial business situation | Trigger / condition | Outcome / reservation effect | Balance / Trade effect | Basis | Status |
|----------------------------|---------------------|------------------------------|------------------------|-------|--------|
| Valid new BUY/SELL | Available exactly equals required full reserve | Funding gate passes; commit required amount | Available committed amount zero; Total unchanged; other gates still apply | [OQ-021](decisions.md#oq-021)/[OQ-022](decisions.md#oq-022); [BAL-INV-002](invariants.md#bal-inv-002) | PROVISIONAL |
| Valid new request | Available below full reserve | Reject before acceptance/STP | Existing self-owned Order unchanged; no Trade, resize or new commitment | [OQ-021](decisions.md#oq-021)/[OQ-027](decisions.md#oq-027) | PROVISIONAL |
| Another active commitment uses assets | New request tries to reuse Locked funds | Cannot fund new obligation from them | Cross-market USDT and same-asset SELL exclusivity | [BAL-INV-001](invariants.md#bal-inv-001); [OQ-005](decisions.md#oq-005) | APPROVED principle |
| Active BUY reserve | Actual fill at price within boundary | Consume actual cost; recompute remainder and release surplus | Counterparty/treasury ownership changes only for Trade | [OQ-015](decisions.md#oq-015)/[OQ-022](decisions.md#oq-022)/[OQ-023](decisions.md#oq-023)/[OQ-024](decisions.md#oq-024)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Active SELL reserve | Actual partial sale | Transfer sold Base; retain Base for active remainder | Receive net USDT; no extra Quote lock | [OQ-015](decisions.md#oq-015)/[OQ-022](decisions.md#oq-022)/[OQ-024](decisions.md#oq-024) | PROVISIONAL |
| Active remainder | User cancel, STP, Market remainder or effective end | Release all unused commitment | No refund of already transferred units/fees | [OQ-023](decisions.md#oq-023); [BAL-INV-003](invariants.md#bal-inv-003) | PROVISIONAL |
| Ended commitment | Cancel/observe again | No additional release or settlement | All balance/Trade/fee facts unchanged | [OQ-031](decisions.md#oq-031); [STL-INV-003](invariants.md#stl-inv-003) | PROVISIONAL |
| Partially invalid setup allocations | Prepare/start | UNDECIDED — [GAP-BAL-001](review/gap-register.md#gap-bal-001) | Do not invent rollback/partial acceptance or missing costs | [OQ-042](decisions.md#oq-042)/[OQ-046](decisions.md#oq-046) | OPEN |

### No action and edge cases

Resting funded GTC retains its required reserve when no fill/cancel/end occurs. Releasing price-improvement surplus changes Available, not profit. Full fill releases remaining surplus, leaving no active commitment; an unfilled Market must also release its reserve. Tiny positive valid step remainders stay funded without an extra minimum. Canonical downward fee rounding and upward buy reserve rounding prevent fragmentation from adding unreserved fee obligations; exact pair boundaries remain proposed [OQ-031](decisions.md#oq-031).

[BS-BAL-001](scenarios/balance-settlement.md#bs-bal-001)–[BS-BAL-005](scenarios/balance-settlement.md#bs-bal-005) cover reserve/recompute/release/exclusivity and maker-rate dominance; [BS-TRD-001](scenarios/balance-settlement.md#bs-trd-001)–[BS-TRD-004](scenarios/balance-settlement.md#bs-trd-004) cover exact ownership; [BS-ORD-009](scenarios/orders.md#bs-ord-009) covers funding before STP. [DIA-RESERVE](diagrams/README.md#dia-reserve) and [DIA-SETTLE](diagrams/README.md#dia-settle) are shared diagrams. Read Orders for termination, Settlement/Fees for actual costs, and Experiment for effective end. No timeout, auto-unlock or technical financial mechanism is inferred.
