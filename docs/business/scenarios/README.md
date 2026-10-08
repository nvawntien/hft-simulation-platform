# Business scenarios — navigation and status

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Main specification](../README.md) · [Diagram source](../diagrams/README.md) · [Coverage matrix](../review/coverage-matrix.md) · [Open gaps](../review/gap-register.md)

BS IDs identify stable business examples/behavioral cases; they are not new OQs, FRs or owner decisions. Keep an ID when clarifying the same behavior; do not reuse it for unrelated intent. Proposed dependencies keep the full normative outcome PROVISIONAL. OPEN / BLOCKED means the complete materially ambiguous result cannot safely be specified. APPROVED-DERIVED cases deliberately isolate an approved core from proposed state/funding/settlement refinements.

**Cách review scenario:** đọc điều kiện trước và phạm vi thật sự được kiểm chứng; đối chiếu [A] đã duyệt, [P] đề xuất và [OPEN] thiếu quyết định. Một fixture cho sẵn thứ tự có hiệu lực không giải quyết chính sách cho hai hành động đến cùng thời điểm. Các ví dụ được review trên tài liệu, chưa có kiểm thử ứng dụng đã chạy.

Each case includes actors/objects/trace, purpose, trigger, flows, invalid/no-action/time cases, postconditions/effects, a specific diagram link and gaps. Some narrow selection/role cases intentionally delegate exact accounting to related financial cases, keeping approved core scope honest. Coverage is tracked per action/transition, not by counting OQ mentions.

## Scenario catalogue

| Scenario | Group | Title | Complete status |
|----------|-------|-------|-----------------|
| [BS-ORD-001](orders.md#bs-ord-001) | orders | Limit with no immediate match | PROVISIONAL |
| [BS-ORD-002](orders.md#bs-ord-002) | orders | Limit partial immediate execution | PROVISIONAL |
| [BS-ORD-003](orders.md#bs-ord-003) | orders | Limit immediate full execution | PROVISIONAL |
| [BS-ORD-004](orders.md#bs-ord-004) | orders | Protected Market full execution across levels | PROVISIONAL |
| [BS-ORD-005](orders.md#bs-ord-005) | orders | Market partial because eligible liquidity is insufficient | PROVISIONAL |
| [BS-ORD-006](orders.md#bs-ord-006) | orders | Market stops at price protection | PROVISIONAL |
| [BS-ORD-007](orders.md#bs-ord-007) | orders | Valid Market with zero eligible liquidity | PROVISIONAL |
| [BS-ORD-008](orders.md#bs-ord-008) | orders | Invalid request and exact numeric boundaries | PROVISIONAL |
| [BS-ORD-009](orders.md#bs-ord-009) | orders | Insufficient funding before any STP | PROVISIONAL |
| [BS-ORD-010](orders.md#bs-ord-010) | orders | GTC with no later business actions | PROVISIONAL |
| [BS-ORD-011](orders.md#bs-ord-011) | orders | Cancel own OPEN remainder | PROVISIONAL |
| [BS-ORD-012](orders.md#bs-ord-012) | orders | Cancel own PARTIALLY_FILLED remainder | PROVISIONAL |
| [BS-ORD-013](orders.md#bs-ord-013) | orders | Cancel FILLED Order is too late | PROVISIONAL |
| [BS-ORD-014](orders.md#bs-ord-014) | orders | Cancel already CANCELLED Order | PROVISIONAL |
| [BS-ORD-015](orders.md#bs-ord-015) | orders | Cancel REJECTED or nonexistent target | PROVISIONAL |
| [BS-ORD-016](orders.md#bs-ord-016) | orders | Cancel another account Order | PROVISIONAL |
| [BS-ORD-017](orders.md#bs-ord-017) | orders | Known effective cancel/fill order and independent replacement | PROVISIONAL |
| [BS-ORD-018](orders.md#bs-ord-018) | orders | Repeated submission with uncertain prior acceptance | OPEN / BLOCKED |
| [BS-EXP-001](experiment.md#bs-exp-001) | experiment | Prepare explicit valid scenario inputs | PROVISIONAL |
| [BS-EXP-002](experiment.md#bs-exp-002) | experiment | Start and repeated/invalid experiment controls | OPEN / BLOCKED |
| [BS-EXP-003](experiment.md#bs-exp-003) | experiment | No participant action and empty running book | OPEN / BLOCKED |
| [BS-EXP-004](experiment.md#bs-exp-004) | experiment | Effective end with open/partly filled/no-Trade variants | PROVISIONAL |
| [BS-EXP-005](experiment.md#bs-exp-005) | experiment | Reset into separately funded experiment | PROVISIONAL |
| [BS-EXP-006](experiment.md#bs-exp-006) | experiment | Run already designated invalid for research | PROVISIONAL |
| [BS-EXP-007](experiment.md#bs-exp-007) | experiment | Finite ordinary scenario liquidity | PROVISIONAL |
| [BS-EXP-008](experiment.md#bs-exp-008) | experiment | 30-minute / one-hour end and boundary policy probes | OPEN / BLOCKED |
| [BS-MAT-001](matching-stp.md#bs-mat-001) | matching-stp | Better price dominates earlier worse price | APPROVED-DERIVED |
| [BS-MAT-002](matching-stp.md#bs-mat-002) | matching-stp | Same price: sequence before size or displayed time | PROVISIONAL |
| [BS-MAT-003](matching-stp.md#bs-mat-003) | matching-stp | Multiple levels and partial resting order | PROVISIONAL |
| [BS-MAT-004](matching-stp.md#bs-mat-004) | matching-stp | Identical scheduled arrivals without acceptance order | OPEN / BLOCKED |
| [BS-STP-001](matching-stp.md#bs-stp-001) | matching-stp | Own resting Order first; continue externally | PROVISIONAL |
| [BS-STP-002](matching-stp.md#bs-stp-002) | matching-stp | External fill before own interest and further external fill | PROVISIONAL |
| [BS-STP-003](matching-stp.md#bs-stp-003) | matching-stp | Multiple own resting remainders with earlier legitimate history | PROVISIONAL |
| [BS-STP-004](matching-stp.md#bs-stp-004) | matching-stp | STP leaves no external eligible liquidity | PROVISIONAL |
| [BS-STP-005](matching-stp.md#bs-stp-005) | matching-stp | Distinct accounts with common human owner | APPROVED-DERIVED |
| [BS-BAL-001](balance-settlement.md#bs-bal-001) | balance-settlement | BUY and SELL reserve full fee-supported obligations | PROVISIONAL |
| [BS-BAL-002](balance-settlement.md#bs-bal-002) | balance-settlement | Partial BUY/SELL settlement and immediate surplus release | PROVISIONAL |
| [BS-BAL-003](balance-settlement.md#bs-bal-003) | balance-settlement | Release all unused reserve on every sourced termination | PROVISIONAL |
| [BS-BAL-004](balance-settlement.md#bs-bal-004) | balance-settlement | Reserved USDT cannot fund another market Order | APPROVED-DERIVED |
| [BS-BAL-005](balance-settlement.md#bs-bal-005) | balance-settlement | Maker rate exceeds Taker; Limit changes role | PROVISIONAL |
| [BS-TRD-001](balance-settlement.md#bs-trd-001) | balance-settlement | Original two-Trade settlement ledger | PROVISIONAL |
| [BS-TRD-002](balance-settlement.md#bs-trd-002) | balance-settlement | Report the same settled Trade again | PROVISIONAL |
| [BS-TRD-003](balance-settlement.md#bs-trd-003) | balance-settlement | Conserve every asset including Fee Treasury | PROVISIONAL |
| [BS-TRD-004](balance-settlement.md#bs-trd-004) | balance-settlement | Confirmed Trade cannot be exceptionally corrected | PROVISIONAL |
| [BS-FEE-001](balance-settlement.md#bs-fee-001) | balance-settlement | Fee rounding and configured-rate boundaries | PROVISIONAL |
| [BS-FEE-002](balance-settlement.md#bs-fee-002) | balance-settlement | Same Limit takes now and makes later | APPROVED-DERIVED |
| [BS-MKT-001](market-data.md#bs-mkt-001) | market-data | Before first Trade; funding/reference cannot set Last Price | PROVISIONAL |
| [BS-MKT-002](market-data.md#bs-mkt-002) | market-data | Empty and one-sided quotes / Spread | PROVISIONAL |
| [BS-MKT-003](market-data.md#bs-mkt-003) | market-data | STP changes depth without execution data | PROVISIONAL |
| [BS-MKT-004](market-data.md#bs-mkt-004) | market-data | Source candle with Trades and once-counted gross volume | PROVISIONAL |
| [BS-MKT-005](market-data.md#bs-mkt-005) | market-data | Empty candle after an earlier Trade | PROVISIONAL |
| [BS-MKT-006](market-data.md#bs-mkt-006) | market-data | Trade exactly on minute boundary and execution-time ties | PROVISIONAL |
| [BS-MKT-007](market-data.md#bs-mkt-007) | market-data | Effective end inside minute retains partial final interval | PROVISIONAL |
| [BS-MKT-008](market-data.md#bs-mkt-008) | market-data | Changing book facts inside an alleged observation | OPEN / BLOCKED |
| [BS-PF-001](portfolio.md#bs-pf-001) | portfolio | Weighted cost and realized/unrealized PnL without fees | PROVISIONAL |
| [BS-PF-002](portfolio.md#bs-pf-002) | portfolio | Fee-inclusive weighted cost without double deduction | PROVISIONAL |
| [BS-PF-003](portfolio.md#bs-pf-003) | portfolio | Seeded Base with unavailable Last Price | PROVISIONAL |
| [BS-PF-004](portfolio.md#bs-pf-004) | portfolio | Retain proportional rounding residual; full sale removes all | PROVISIONAL |
| [BS-PF-005](portfolio.md#bs-pf-005) | portfolio | No own action, other Trades and 60-second price age | PROVISIONAL |
| [BS-HFT-001](algo-hft.md#bs-hft-001) | algo-hft | Observed quote is older than effective book | PROVISIONAL |
| [BS-HFT-002](algo-hft.md#bs-hft-002) | algo-hft | Cancellation action delay with known effective precedence | PROVISIONAL |
| [BS-HFT-003](algo-hft.md#bs-hft-003) | algo-hft | Zero delay and invalid delay boundaries | PROVISIONAL |
| [BS-HFT-004](algo-hft.md#bs-hft-004) | algo-hft | Own resting queue count and quantity ahead | PROVISIONAL |
| [BS-HFT-005](algo-hft.md#bs-hft-005) | algo-hft | Slippage, fees, partial/no-fill and missing reference metrics | PROVISIONAL |
| [BS-HFT-006](algo-hft.md#bs-hft-006) | algo-hft | Separate experiments with different delays or same supplied inputs | PROVISIONAL |
| [BS-HFT-007](algo-hft.md#bs-hft-007) | algo-hft | Scenario-hosted strategy capital and identity ambiguity | OPEN / BLOCKED |
| [BS-HFT-008](algo-hft.md#bs-hft-008) | algo-hft | Achieved time-to-first/full-fill has no chosen origin | OPEN / BLOCKED |
| [BS-ORD-019](orders.md#bs-ord-019) | orders | Rejected invalid quantity and every-order invariant scope | OPEN / BLOCKED |

## Shared verification assumptions and exclusions

- Fixture capital, rates, prices, quantities, dates and observed/effective times are explicit inputs; numeric constraints trace to source. No new universal default is selected.
- “Running”, “setup”, “end effective” and “no prior Trade” may be supplied conditions. These do not approve unspecified experiment control, duration, clock advancement, tie resolution or delayed delivery policies.
- Failed gates have source-supported effects; multiple-gate error wording and UI presentation are not specified here.
- No-action means no new relevant business event within the stated fixture. Other participants or scheduled scenario actions may still trade outside that fixture.
- Diagram arrows describe business interactions/effects and authoritative order, not software architecture or intermediate financial processing.
- Financial examples derive from exact source formulas and preserve fees, filled history, ownership and residuals. Source-proposed financial formulas remain unapproved even when the arithmetic checks pass.
