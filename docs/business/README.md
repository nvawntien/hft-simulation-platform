# Business Specification V2 — working draft

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

This specification helps six student developers identify observable trading behavior and challenge missing decisions before implementation. It covers simulated funded spot trading and research experiments. The authorized phase is business documentation only; architecture and implementation remain outside this task.

**Đọc trước:** Đây là bản làm việc để nhóm review. Nội dung được chia module để tìm đúng quy tắc, kết quả và quyết định còn thiếu. Một ví dụ tính đúng hoặc sơ đồ hợp lý không làm chính sách được duyệt.

## Authority, status and language

- English domain modules, [decision register](decisions.md), [invariants](invariants.md) and [glossary](glossary.md) together are the canonical Business source. Each OQ, BR and invariant has one canonical ID entry. Narrative and scenarios apply or explain those entries; they do not create competing policy definitions.
- [OQ-001](decisions.md#oq-001) through [OQ-014](decisions.md#oq-014) remain **APPROVED**. [OQ-015](decisions.md#oq-015) through [OQ-046](decisions.md#oq-046) remain **PROPOSED — OWNER REVIEW REQUIRED**. Existing BR statuses and invariant bases remain unchanged. No new OQ or business approval is allocated.
- **APPROVED-DERIVED** means the scenario's complete specified outcome uses only approved decisions and supporting domain definitions. It is a derivation label, not a new owner decision. **PROVISIONAL** means at least one normative dependency is proposed. **OPEN / BLOCKED** means a material result cannot yet be specified. A known conditional subflow can be provisional while the complete boundary scenario is blocked.
- [Scenarios](scenarios/README.md), diagrams, decision tables and review analyses are Phase B explanatory material. Their status follows the exact source dependencies. On disagreement with source, report a conflict and obtain a decision; do not implement the new interpretation.
- [BUSINESS.md](../BUSINESS.md) and [BUSINESS.vi.md](../BUSINESS.vi.md) are compatibility entry points, with all original numbered headings. They contain navigation rather than independent editable rules. [Frozen sources](history/README.md) are historical evidence, not the V2 authority.
- English is canonical. Vietnamese navigation and review guidance are reading aids. The historical Vietnamese edition remains available in full; it does not translate new V2 analyses. Checked translation areas and remaining limitations are recorded in [migration evidence](review/migration-report.md#language-review). Full V2 semantic translation parity is **not claimed**.
- Requirements are downstream. [REQUIREMENTS.md](../REQUIREMENTS.md) and [REQUIREMENTS.vi.md](../REQUIREMENTS.vi.md) can reveal coverage gaps but cannot authorize Business policy. Neither was modified in this task.

## Domain navigation

| Business domain | Specification | Read for |
|-----------------|---------------|----------|
| Scope / actors | [01-scope-actors.md](01-scope-actors.md) | Trading boundaries, participant ownership |
| Experiment / Scenario | [02-experiment.md](02-experiment.md) | Preparation, clock, effective end, reset |
| Markets / Assets | [03-markets-assets.md](03-markets-assets.md) | Units, symbols, numeric grids |
| Order | [04-orders.md](04-orders.md) | Submission, lifecycle, GTC, cancellation |
| Matching / STP | [05-matching-stp.md](05-matching-stp.md) | Price, priority, pair eligibility, self-interest |
| Balance / Reservation | [06-balance-reservation.md](06-balance-reservation.md) | Funding, locks, partial release |
| Trade / Settlement / Fee | [07-trade-settlement-fees.md](07-trade-settlement-fees.md) | Ownership transfer, roles, treasury, rounding |
| Order Book / Market Data | [08-market-data.md](08-market-data.md) | Depth, observations, Last Price, candles |
| Portfolio / PnL | [09-portfolio-pnl.md](09-portfolio-pnl.md) | Basis, realized/unrealized, stale valuation |
| Algo / HFT | [10-algo-hft.md](10-algo-hft.md) | Synthetic strategies, queue, latency, slippage |
| Administration | [11-administration.md](11-administration.md) | Setup authority, ordinary liquidity, invalid runs |

## Review resources

| Resource | Purpose |
|----------|---------|
| [Decisions](decisions.md) | Canonical 46 OQ entries, unchanged selected policies/statuses |
| [Invariants](invariants.md) | Canonical 19 consistency statements and their approval bases |
| [Glossary](glossary.md) | Stable English trading terminology for students |
| [Scenario index](scenarios/README.md) | Stable BS IDs, complete outcomes, dependencies and status |
| [Diagram catalogue](diagrams/README.md) | Reusable Mermaid source with scope/status labels |
| [Gap register](review/gap-register.md) | Missing business choices, conflicts, material impact and owner questions |
| [Coverage matrix](review/coverage-matrix.md) | Objects, transitions and meaningful outcome coverage |
| [Review guide](review/review-guide.md) | Six-person review process and valid suggestions |
| [Migration evidence](review/migration-report.md) | Section map, phase boundaries, source hashes, discrepancies and validation limits |

## Find the business reading for a task

1. Identify the object/action, for example cancelling a partially filled Limit.
2. Read its domain module, the exact linked OQs/BRs and applicable invariants. For that example include Orders, Balance/Reservation and Matching/ordering.
3. Read its BS scenario and shared diagram. Inspect the financial/time alternatives, not just the happy path.
4. Check the coverage matrix and related OPEN gaps. Proposed behavior awaits owner review; an unspecified branch is not delegated to a developer's judgment.
5. Follow unchanged Requirements section 11 from OQ/BR to impacted FRs, local criteria, AS scenarios and UCs. Use the section migration map below when Requirements says “BUSINESS.md section N”.

**Cách đọc khi nhận task:** tìm đối tượng và hành động → đọc module → đọc OQ/BR/invariant → đọc scenario/diagram → kiểm tra GAP → đối chiếu FR. Nếu thiếu kết quả nghiệp vụ, mở comment hoặc GAP; không tự chọn bằng kinh nghiệm cá nhân hay AI.

Search from the repository root:

```sh
rg -n 'OQ-031|BR-ORD-007|ORD-INV-004|BS-ORD-012' docs/business --glob '!**/history/**'
rg -n '^## BS-|^Status:' docs/business/scenarios
rg -n '^## GAP-|^Status: OPEN' docs/business/review/gap-register.md
```

The history folder intentionally repeats historical IDs. For canonical definitions, use the direct links below rather than search hit counts.

## Propose, decide, propagate

A proposed business change identifies current source, a concrete ambiguous scenario, financial/time effects and exact owner question. An explicit owner/team outcome is recorded with actual date and reviewer/authority; a merge, translation, AI response or example does not constitute approval. The owner determines whether any new decision ID is allocated. Preserve the original OQ recommendations while their gaps are reviewed.

After an authorized decision, update the canonical entry and affected narrative/scenarios/diagrams/invariants, then reconcile downstream Requirements and translation aids in a separately authorized change. Recheck inherited statuses: any unapproved dependency keeps the complete scenario provisional. This migration does not perform that Requirements reconciliation or authorize implementation.

## Phase A / Phase B review boundary

Every domain has **Phase A — migrated source**, containing unchanged source prose and canonical BR rows, followed by **Phase B — behavioral analysis**. Decision and invariant registers preserve their source tables. New BS/GAP IDs, diagrams, object analysis and coverage are Phase B only. [Migration evidence](review/migration-report.md#phase-review-boundary) records a reproducible Phase A checkpoint so the two reviews can be separated without claiming a commit that was not made.

## Original numbered section migration map

| Original English section | Canonical V2 location |
|--------------------------|-----------------------|
| 1. Purpose of This Document | [review/migration-report.md](review/migration-report.md#source-section-1) |
| 2. Product Domain Overview | [01-scope-actors.md](01-scope-actors.md#source-section-2) |
| 3. Product Boundary | [01-scope-actors.md](01-scope-actors.md#source-section-3) |
| 4. Business Actors | [01-scope-actors.md](01-scope-actors.md#source-section-4) |
| 5. Assets and Trading Symbols | [03-markets-assets.md](03-markets-assets.md#source-section-5) |
| 6. Orders | [04-orders.md](04-orders.md#source-section-6) |
| 7. Limit Order Business Behavior | [04-orders.md](04-orders.md#source-section-7) |
| 8. Market Order Business Behavior | [04-orders.md](04-orders.md#source-section-8) |
| 9. Order Book | [08-market-data.md](08-market-data.md#source-section-9) |
| 10. Matching Rules | [05-matching-stp.md](05-matching-stp.md#source-section-10) |
| 11. Partial Fills | [04-orders.md](04-orders.md#source-section-11) |
| 12. Balance and Asset Reservation | [06-balance-reservation.md](06-balance-reservation.md#source-section-12) |
| 13. Order Lifecycle | [04-orders.md](04-orders.md#source-section-13) |
| 14. Order Cancellation | [04-orders.md](04-orders.md#source-section-14) |
| 15. Time In Force | [04-orders.md](04-orders.md#source-section-15) |
| 16. Trade | [07-trade-settlement-fees.md](07-trade-settlement-fees.md#source-section-16) |
| 17. Execution Price | [05-matching-stp.md](05-matching-stp.md#source-section-17) |
| 18. Trade Settlement | [07-trade-settlement-fees.md](07-trade-settlement-fees.md#source-section-18) |
| 19. Market Price Concepts | [08-market-data.md](08-market-data.md#source-section-19) |
| 20. Liquidity | [10-algo-hft.md](10-algo-hft.md#source-section-20) |
| 21. Slippage | [10-algo-hft.md](10-algo-hft.md#source-section-21) |
| 22. Market Impact | [10-algo-hft.md](10-algo-hft.md#source-section-22) |
| 23. Market Data | [08-market-data.md](08-market-data.md#source-section-23) |
| 24. Candlesticks / OHLCV | [08-market-data.md](08-market-data.md#source-section-24) |
| 25. Portfolio | [09-portfolio-pnl.md](09-portfolio-pnl.md#source-section-25) |
| 26. Fees | [07-trade-settlement-fees.md](07-trade-settlement-fees.md#source-section-26) |
| 27. Market Maker | [11-administration.md](11-administration.md#source-section-27) |
| 28. Algorithmic Trading | [10-algo-hft.md](10-algo-hft.md#source-section-28) |
| 29. HFT | [10-algo-hft.md](10-algo-hft.md#source-section-29) |
| 30. Business Invariants | [invariants.md](invariants.md#source-section-30) |
| 31. Business Rules and Approval Status | [README.md](README.md#canonical-rule-index) |
| 32. Business Decision Register | [decisions.md](decisions.md#source-section-32) |
| 33. Glossary | [glossary.md](glossary.md#source-section-33) |
| 34. Team Knowledge Checklist | [review/review-guide.md](review/review-guide.md#source-section-34) |
| 35. Review Log | [review/migration-report.md](review/migration-report.md#source-section-35) |

<a id="canonical-rule-index"></a>

## Canonical rule index

Descriptions and statuses live in the linked rows. This index carries no duplicate rule definition.

| BR ID | Canonical entry |
|-------|-----------------|
| [BR-ORD-001](05-matching-stp.md#br-ord-001) | [BR-ORD-001](05-matching-stp.md#br-ord-001) |
| [BR-ORD-002](04-orders.md#br-ord-002) | [BR-ORD-002](04-orders.md#br-ord-002) |
| [BR-ORD-003](04-orders.md#br-ord-003) | [BR-ORD-003](04-orders.md#br-ord-003) |
| [BR-BAL-001](06-balance-reservation.md#br-bal-001) | [BR-BAL-001](06-balance-reservation.md#br-bal-001) |
| [BR-BAL-002](06-balance-reservation.md#br-bal-002) | [BR-BAL-002](06-balance-reservation.md#br-bal-002) |
| [BR-BAL-003](06-balance-reservation.md#br-bal-003) | [BR-BAL-003](06-balance-reservation.md#br-bal-003) |
| [BR-BAL-004](06-balance-reservation.md#br-bal-004) | [BR-BAL-004](06-balance-reservation.md#br-bal-004) |
| [BR-TRD-001](07-trade-settlement-fees.md#br-trd-001) | [BR-TRD-001](07-trade-settlement-fees.md#br-trd-001) |
| [BR-STL-001](07-trade-settlement-fees.md#br-stl-001) | [BR-STL-001](07-trade-settlement-fees.md#br-stl-001) |
| [BR-MKT-001](08-market-data.md#br-mkt-001) | [BR-MKT-001](08-market-data.md#br-mkt-001) |
| [BR-MKT-002](08-market-data.md#br-mkt-002) | [BR-MKT-002](08-market-data.md#br-mkt-002) |
| [BR-PF-001](09-portfolio-pnl.md#br-pf-001) | [BR-PF-001](09-portfolio-pnl.md#br-pf-001) |
| [BR-ACT-001](01-scope-actors.md#br-act-001) | [BR-ACT-001](01-scope-actors.md#br-act-001) |
| [BR-ORD-004](04-orders.md#br-ord-004) | [BR-ORD-004](04-orders.md#br-ord-004) |
| [BR-ORD-005](05-matching-stp.md#br-ord-005) | [BR-ORD-005](05-matching-stp.md#br-ord-005) |
| [BR-ORD-006](05-matching-stp.md#br-ord-006) | [BR-ORD-006](05-matching-stp.md#br-ord-006) |
| [BR-FEE-001](07-trade-settlement-fees.md#br-fee-001) | [BR-FEE-001](07-trade-settlement-fees.md#br-fee-001) |
| [BR-FEE-002](07-trade-settlement-fees.md#br-fee-002) | [BR-FEE-002](07-trade-settlement-fees.md#br-fee-002) |
| [BR-MKT-003](03-markets-assets.md#br-mkt-003) | [BR-MKT-003](03-markets-assets.md#br-mkt-003) |
| [BR-SIM-001](11-administration.md#br-sim-001) | [BR-SIM-001](11-administration.md#br-sim-001) |
| [BR-PREC-001](03-markets-assets.md#br-prec-001) | [BR-PREC-001](03-markets-assets.md#br-prec-001) |
| [BR-ORD-007](04-orders.md#br-ord-007) | [BR-ORD-007](04-orders.md#br-ord-007) |
| [BR-SIM-002](02-experiment.md#br-sim-002) | [BR-SIM-002](02-experiment.md#br-sim-002) |
| [BR-ACT-002](01-scope-actors.md#br-act-002) | [BR-ACT-002](01-scope-actors.md#br-act-002) |
| [BR-MKT-004](08-market-data.md#br-mkt-004) | [BR-MKT-004](08-market-data.md#br-mkt-004) |
| [BR-ALGO-001](10-algo-hft.md#br-algo-001) | [BR-ALGO-001](10-algo-hft.md#br-algo-001) |
