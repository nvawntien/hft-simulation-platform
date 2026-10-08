# Business invariants — canonical register

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md)

## Phase A — migrated source

<a id="source-section-30"></a>

## 30. Business Invariants

These distinguish general consistency from V1-specific policy. Proposed details retain their status even when expressed as requirements of the recommended model.

| ID | Invariant | Basis |
|----|-----------|-------|
| <a id="ord-inv-001"></a>ORD-INV-001 | `0 ≤ filled ≤ original` for every order; original quantity never changes in V1. | Domain concept; no-amendment detail proposed [OQ-028](decisions.md#oq-028). |
| <a id="ord-inv-002"></a>ORD-INV-002 | `original = filled + unexecuted`; active remaining is zero after termination. | Domain concept; terminology proposed [OQ-027](decisions.md#oq-027). |
| <a id="ord-inv-003"></a>ORD-INV-003 | Every execution respects both applicable boundaries, including Market protection. | APPROVED [OQ-008](decisions.md#oq-008), [OQ-009](decisions.md#oq-009). |
| <a id="ord-inv-004"></a>ORD-INV-004 | Effective cancellation prevents future execution of that remainder; prior fills stand. | Domain concept; precedence proposed [OQ-031](decisions.md#oq-031). |
| <a id="bal-inv-001"></a>BAL-INV-001 | Reserved assets cannot fund a second order, including across symbols. | Domain concept; funded model APPROVED [OQ-005](decisions.md#oq-005). |
| <a id="bal-inv-002"></a>BAL-INV-002 | Reservation/release alone changes availability, not total ownership. | Domain concept. |
| <a id="bal-inv-003"></a>BAL-INV-003 | Releasing an unused reservation cannot refund assets already exchanged or fees already paid. | Domain concept. |
| <a id="trd-inv-001"></a>TRD-INV-001 | A confirmed Trade remains immutable historical fact. | Domain concept; no corrections proposed [OQ-045](decisions.md#oq-045). |
| <a id="trd-inv-002"></a>TRD-INV-002 | Both sides agree on symbol, Base quantity, and resting-order execution price. | Domain concept; price APPROVED [OQ-009](decisions.md#oq-009). |
| <a id="trd-inv-003"></a>TRD-INV-003 | Each Trade contributes its quantity once to each participating order's filled total. | Domain concept. |
| <a id="stl-inv-001"></a>STL-INV-001 | Settlement gives consistent ownership to all parties, including the fee recipient. | Domain concept; USDT treasury proposed [OQ-015](decisions.md#oq-015), [OQ-024](decisions.md#oq-024). |
| <a id="stl-inv-002"></a>STL-INV-002 | Trading conserves each asset across participants plus Fee Treasury; Simulation Funding is accounted for separately. | Domain concept; proposed fee destination. |
| <a id="stl-inv-003"></a>STL-INV-003 | Each Trade's asset/fee transfers apply once, never again because it is reported again. | Domain concept. |
| <a id="mkt-inv-001"></a>MKT-INV-001 | Last Price reflects only the latest actual Trade in authoritative execution order. | Domain concept; initialization proposed [OQ-044](decisions.md#oq-044). |
| <a id="mkt-inv-002"></a>MKT-INV-002 | Market volume counts each Trade once, not once per counterparty. | Domain concept; windows proposed [OQ-038](decisions.md#oq-038). |
| <a id="ord-inv-005"></a>ORD-INV-005 | Equal-price priority follows platform acceptance sequence, never client time or larger quantity. | APPROVED [OQ-010](decisions.md#oq-010). |
| <a id="trd-inv-004"></a>TRD-INV-004 | No Trade occurs between the same trading account's orders; STP cancels the resting remainder and creates no fee/volume/Last Price. | APPROVED [OQ-011](decisions.md#oq-011). |
| <a id="bal-inv-004"></a>BAL-INV-004 | Accepted commitments cannot require unavailable assets; available and locked balances remain nonnegative. | APPROVED funded scope; reserve calculation proposed [OQ-021](decisions.md#oq-021), [OQ-022](decisions.md#oq-022). |
| <a id="sim-inv-001"></a>SIM-INV-001 | Funding is not execution, volume, or trading profit and cannot manufacture a market price. | APPROVED [OQ-003](decisions.md#oq-003). |
