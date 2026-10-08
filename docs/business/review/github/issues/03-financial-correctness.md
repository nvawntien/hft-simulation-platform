# Issue title

Review reservation, settlement, fees and asset conservation

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Review boundaries

This is a documentation review, not implementation or owner approval. Business is policy authority; Requirements provide impact hints only and must not be edited in this Issue. No Architecture/technology/API/schema decisions are requested. Existing OQ-001–OQ-014 approvals and OQ-015–OQ-046 proposals remain unchanged. A merge or closed review Issue does not approve a policy.

Every finding records source file/section, exact OQ/BR/invariant/BS/GAP, a concrete counterexample or calculation, known/proposed/undefined layers, material effects and owner question. Alternatives are discussion options. The team may complete the review while owner questions remain OPEN, provided they are explicitly recorded and linked. No personal/AI judgment substitutes for an unresolved owner decision.

GitHub usernames are not available. Reviewer roles below are proposed, not assigned. Attach comments to the actual Business V2 PR head once it is published; repository paths below refer to that reviewed tree, not the current LICENSE-only main.

## Proposed reviewers

Member 4 (lead); Member 2 cross-reviews acceptance/termination; Member 5 cross-reviews volume/PnL. Business Owner reviews financial policy proposals separately.

## Explicit scope and reading

`docs/business/06-balance-reservation.md`, `07-trade-settlement-fees.md`; `scenarios/balance-settlement.md`; BS-ORD-002/005/009/012; DIA-RESERVE / DIA-SETTLE / DIA-ROLES.

Full BUY Limit/Market and Base-only SELL commitments; exact sufficient/insufficient funding before acceptance/STP; partial settlement and per-Trade release; every termination/repeated report; maker/taker role change and maker rate above taker; per-side fee floor/reserve ceil boundaries; exact notional and asset conservation with Fee Treasury; immutable once-only Trade effects; invalid/partly prepared setup allocation outcomes.

Do not adopt proposed fee/rate/grid/settlement policies by approving arithmetic. Do not select setup funding bounds/readiness/rollback, introduce an unsettled stage, reverse a Trade or prescribe technical financial mechanisms.

## Source trace and primary gaps

OQ-005, OQ-013–OQ-024, OQ-031, OQ-033, OQ-042, OQ-045, OQ-046; BR-BAL-001–BR-BAL-004, BR-STL-001, BR-FEE-001/002, BR-TRD-001; BAL and STL invariants, TRD-INV-001/003, SIM-INV-001. Primary gap: GAP-BAL-001.

## Expected deliverables

1. An exact-decimal calculation sheet for canonical reserve/partial/final ledger and conservation examples, with units and source formulas.
2. Boundary calculations for tiny fees, conservative reserves, role changes and residual releases; explicitly distinguish per-Trade surplus from final termination release.
3. A review finding/owner question for GAP-BAL-001; reconcile readiness impact with Experiment review.
4. An accounting/Trade-count/fee cross-review note for Market Data and Portfolio.

## Acceptance criteria

- [ ] Available + Locked equals Total; reservation/release alone does not change ownership or recognize profit.
- [ ] Every Trade and fee transfer applies once; repeating a report/cancel causes no second effect.
- [ ] Buyer, seller and Fee Treasury conserve USDT; Base units reconcile independently.
- [ ] Full required funding is checked before STP and cannot rely on later self-release or a thinner book.
- [ ] Per-Trade floor and buy-reserve ceil/role maximum follow current proposed source; residuals are retained.
- [ ] The source two-Trade ledger is an accounting checkpoint within a complete incoming outcome, not an interruption opportunity.
- [ ] GAP-BAL-001 remains OPEN with concrete invalid/partial setup consequences and exact owner question.
- [ ] Financial proposals remain PROVISIONAL; no rates/defaults/settlement permissions are approved by this review.

## Closure evidence

Link the review findings/calculation sheet and the actual PR discussion; list remaining OPEN owner questions and cross-review acknowledgements. Record only reviewers/outcomes that actually occurred. Do not close based only on OQ keyword coverage or an AI-generated explanation.
