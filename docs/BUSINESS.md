# HFT Simulation Platform — Business Specification V2 entry point

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Main modular specification](business/README.md) · [Original English source](business/history/BUSINESS.md) · [Vietnamese entry point](BUSINESS.vi.md)

This file is a compatibility index, not an independently editable copy of business rules. English modules are canonical. Original numbered headings below preserve existing links and section references.

## 1. Purpose of This Document

[Canonical location](business/review/migration-report.md#source-section-1).

## 2. Product Domain Overview

[Canonical location](business/01-scope-actors.md#source-section-2).

## 3. Product Boundary

[Canonical location](business/01-scope-actors.md#source-section-3).

## 4. Business Actors

[Canonical location](business/01-scope-actors.md#source-section-4).

## 5. Assets and Trading Symbols

[Canonical location](business/03-markets-assets.md#source-section-5).

## 6. Orders

[Canonical location](business/04-orders.md#source-section-6).

## 7. Limit Order Business Behavior

[Canonical location](business/04-orders.md#source-section-7).

## 8. Market Order Business Behavior

[Canonical location](business/04-orders.md#source-section-8).

## 9. Order Book

[Canonical location](business/08-market-data.md#source-section-9).

## 10. Matching Rules

[Canonical location](business/05-matching-stp.md#source-section-10).

## 11. Partial Fills

[Canonical location](business/04-orders.md#source-section-11).

## 12. Balance and Asset Reservation

[Canonical location](business/06-balance-reservation.md#source-section-12).

## 13. Order Lifecycle

[Canonical location](business/04-orders.md#source-section-13).

## 14. Order Cancellation

[Canonical location](business/04-orders.md#source-section-14).

## 15. Time In Force

[Canonical location](business/04-orders.md#source-section-15).

## 16. Trade

[Canonical location](business/07-trade-settlement-fees.md#source-section-16).

## 17. Execution Price

[Canonical location](business/05-matching-stp.md#source-section-17).

## 18. Trade Settlement

[Canonical location](business/07-trade-settlement-fees.md#source-section-18).

## 19. Market Price Concepts

[Canonical location](business/08-market-data.md#source-section-19).

## 20. Liquidity

[Canonical location](business/10-algo-hft.md#source-section-20).

## 21. Slippage

[Canonical location](business/10-algo-hft.md#source-section-21).

## 22. Market Impact

[Canonical location](business/10-algo-hft.md#source-section-22).

## 23. Market Data

[Canonical location](business/08-market-data.md#source-section-23).

## 24. Candlesticks / OHLCV

[Canonical location](business/08-market-data.md#source-section-24).

## 25. Portfolio

[Canonical location](business/09-portfolio-pnl.md#source-section-25).

## 26. Fees

[Canonical location](business/07-trade-settlement-fees.md#source-section-26).

## 27. Market Maker

[Canonical location](business/11-administration.md#source-section-27).

## 28. Algorithmic Trading

[Canonical location](business/10-algo-hft.md#source-section-28).

## 29. HFT

[Canonical location](business/10-algo-hft.md#source-section-29).

## 30. Business Invariants

[Canonical location](business/invariants.md#source-section-30).

[ORD-INV-001](business/invariants.md#ord-inv-001), [ORD-INV-002](business/invariants.md#ord-inv-002), [ORD-INV-003](business/invariants.md#ord-inv-003), [ORD-INV-004](business/invariants.md#ord-inv-004), [BAL-INV-001](business/invariants.md#bal-inv-001), [BAL-INV-002](business/invariants.md#bal-inv-002), [BAL-INV-003](business/invariants.md#bal-inv-003), [TRD-INV-001](business/invariants.md#trd-inv-001), [TRD-INV-002](business/invariants.md#trd-inv-002), [TRD-INV-003](business/invariants.md#trd-inv-003), [STL-INV-001](business/invariants.md#stl-inv-001), [STL-INV-002](business/invariants.md#stl-inv-002), [STL-INV-003](business/invariants.md#stl-inv-003), [MKT-INV-001](business/invariants.md#mkt-inv-001), [MKT-INV-002](business/invariants.md#mkt-inv-002), [ORD-INV-005](business/invariants.md#ord-inv-005), [TRD-INV-004](business/invariants.md#trd-inv-004), [BAL-INV-004](business/invariants.md#bal-inv-004), [SIM-INV-001](business/invariants.md#sim-inv-001)

## 31. Business Rules and Approval Status

[Canonical location](business/README.md#canonical-rule-index).

[BR-ORD-001](business/05-matching-stp.md#br-ord-001), [BR-ORD-002](business/04-orders.md#br-ord-002), [BR-ORD-003](business/04-orders.md#br-ord-003), [BR-BAL-001](business/06-balance-reservation.md#br-bal-001), [BR-BAL-002](business/06-balance-reservation.md#br-bal-002), [BR-BAL-003](business/06-balance-reservation.md#br-bal-003), [BR-BAL-004](business/06-balance-reservation.md#br-bal-004), [BR-TRD-001](business/07-trade-settlement-fees.md#br-trd-001), [BR-STL-001](business/07-trade-settlement-fees.md#br-stl-001), [BR-MKT-001](business/08-market-data.md#br-mkt-001), [BR-MKT-002](business/08-market-data.md#br-mkt-002), [BR-PF-001](business/09-portfolio-pnl.md#br-pf-001), [BR-ACT-001](business/01-scope-actors.md#br-act-001), [BR-ORD-004](business/04-orders.md#br-ord-004), [BR-ORD-005](business/05-matching-stp.md#br-ord-005), [BR-ORD-006](business/05-matching-stp.md#br-ord-006), [BR-FEE-001](business/07-trade-settlement-fees.md#br-fee-001), [BR-FEE-002](business/07-trade-settlement-fees.md#br-fee-002), [BR-MKT-003](business/03-markets-assets.md#br-mkt-003), [BR-SIM-001](business/11-administration.md#br-sim-001), [BR-PREC-001](business/03-markets-assets.md#br-prec-001), [BR-ORD-007](business/04-orders.md#br-ord-007), [BR-SIM-002](business/02-experiment.md#br-sim-002), [BR-ACT-002](business/01-scope-actors.md#br-act-002), [BR-MKT-004](business/08-market-data.md#br-mkt-004), [BR-ALGO-001](business/10-algo-hft.md#br-algo-001)

## 32. Business Decision Register

[Canonical location](business/decisions.md#source-section-32).

[OQ-001](business/decisions.md#oq-001), [OQ-002](business/decisions.md#oq-002), [OQ-003](business/decisions.md#oq-003), [OQ-004](business/decisions.md#oq-004), [OQ-005](business/decisions.md#oq-005), [OQ-006](business/decisions.md#oq-006), [OQ-007](business/decisions.md#oq-007), [OQ-008](business/decisions.md#oq-008), [OQ-009](business/decisions.md#oq-009), [OQ-010](business/decisions.md#oq-010), [OQ-011](business/decisions.md#oq-011), [OQ-012](business/decisions.md#oq-012), [OQ-013](business/decisions.md#oq-013), [OQ-014](business/decisions.md#oq-014), [OQ-015](business/decisions.md#oq-015), [OQ-016](business/decisions.md#oq-016), [OQ-017](business/decisions.md#oq-017), [OQ-018](business/decisions.md#oq-018), [OQ-019](business/decisions.md#oq-019), [OQ-020](business/decisions.md#oq-020), [OQ-021](business/decisions.md#oq-021), [OQ-022](business/decisions.md#oq-022), [OQ-023](business/decisions.md#oq-023), [OQ-024](business/decisions.md#oq-024), [OQ-025](business/decisions.md#oq-025), [OQ-026](business/decisions.md#oq-026), [OQ-027](business/decisions.md#oq-027), [OQ-028](business/decisions.md#oq-028), [OQ-029](business/decisions.md#oq-029), [OQ-030](business/decisions.md#oq-030), [OQ-031](business/decisions.md#oq-031), [OQ-032](business/decisions.md#oq-032), [OQ-033](business/decisions.md#oq-033), [OQ-034](business/decisions.md#oq-034), [OQ-035](business/decisions.md#oq-035), [OQ-036](business/decisions.md#oq-036), [OQ-037](business/decisions.md#oq-037), [OQ-038](business/decisions.md#oq-038), [OQ-039](business/decisions.md#oq-039), [OQ-040](business/decisions.md#oq-040), [OQ-041](business/decisions.md#oq-041), [OQ-042](business/decisions.md#oq-042), [OQ-043](business/decisions.md#oq-043), [OQ-044](business/decisions.md#oq-044), [OQ-045](business/decisions.md#oq-045), [OQ-046](business/decisions.md#oq-046)

## 33. Glossary

[Canonical location](business/glossary.md#source-section-33).

## 34. Team Knowledge Checklist

[Canonical location](business/review/review-guide.md#source-section-34).

## 35. Review Log

[Canonical location](business/review/migration-report.md#source-section-35).
