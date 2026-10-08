# Issue title

Review Order lifecycle, matching priority and STP outcomes

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Review boundaries

This is a documentation review, not implementation or owner approval. Business is policy authority; Requirements provide impact hints only and must not be edited in this Issue. No Architecture/technology/API/schema decisions are requested. Existing OQ-001–OQ-014 approvals and OQ-015–OQ-046 proposals remain unchanged. A merge or closed review Issue does not approve a policy.

Every finding records source file/section, exact OQ/BR/invariant/BS/GAP, a concrete counterexample or calculation, known/proposed/undefined layers, material effects and owner question. Alternatives are discussion options. The team may complete the review while owner questions remain OPEN, provided they are explicitly recorded and linked. No personal/AI judgment substitutes for an unresolved owner decision.

GitHub usernames are not available. Reviewer roles below are proposed, not assigned. Attach comments to the actual Business V2 PR head once it is published; repository paths below refer to that reviewed tree, not the current LICENSE-only main.

## Proposed reviewers

Member 2 (lead); Member 3 reviews price/sequence/STP; Member 4 reviews commitments; Member 6 reviews scheduled ties. Business Owner supplies any later policy decision.

## Explicit scope and reading

`docs/business/03-markets-assets.md`, `04-orders.md`, `05-matching-stp.md`; `scenarios/orders.md`, `scenarios/matching-stp.md`; DIA-ORD / DIA-VALID / DIA-MATCH / DIA-STP / DIA-CANCEL / DIA-PRECEDENCE.

Limit zero/partial/full immediate results; protected Market zero/partial/full/thin/outside-boundary results; grids and insufficient funding before STP; price and accepted-sequence priority; multiple levels/resting partials; own interest first/after external/multiple/without external continuation; every cancellation state, own/foreign/nonexistent/repeated/too-late action; independent cancel then new intent and no-action GTC.

Investigate retry versus new intent, invalid rejected quantity/invariant applicability, and equal scheduled arrival allocation. Do not replace accepted-sequence priority with client time, size, guessed tie order, or an invented normalized quantity. End eligibility is led by Experiment review.

## Source trace and primary gaps

OQ-001, OQ-004–OQ-012, OQ-017–OQ-023, OQ-025–OQ-032; BR-ORD-001–BR-ORD-007, BR-PREC-001; ORD-INV-001–ORD-INV-005, TRD-INV-004, BAL-INV-003. Primary gaps: GAP-ORD-001, GAP-ORD-002, GAP-MAT-001.

## Expected deliverables

1. Reviewed Order transition/cancellation/type-liquidity tables with concrete counterexamples or confirmations by source ID.
2. A prioritized list of findings for all three primary gaps, separating unchanged approved priority from unchosen scheduled tie allocation.
3. Numeric boundary review for BTC/ETH/SOL and price/protection limits; no additional notional/fill minima.
4. Matching/STP diagram annotations and financial handoff for cancellation/reservation effects.

## Acceptance criteria

- [ ] All cancellation states and foreign/nonexistent/repeated targets have explicit outcomes or a source-linked gap.
- [ ] Terminal outcomes do not reactivate Orders or release funds twice; executed history is retained.
- [ ] Market remainder never rests and protection remains an absolute inclusive unit-price boundary.
- [ ] Price then accepted sequence is preserved; unaccepted scheduled ties remain OPEN.
- [ ] STP cancels only eligible own resting remainder and creates no Trade/fee/volume/Last Price; legitimate previous fills stand.
- [ ] Negative/malformed rejected quantities are not silently normalized and ORD-INV-001/002 are not changed.
- [ ] Retry/new intent remains an explicit owner question and scenario statuses retain proposed dependencies.

## Closure evidence

Link the review findings/calculation sheet and the actual PR discussion; list remaining OPEN owner questions and cross-review acknowledgements. Record only reviewers/outcomes that actually occurred. Do not close based only on OQ keyword coverage or an AI-generated explanation.
