# Issue title

Review Algo/HFT latency, queue and execution metrics

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Review boundaries

This is a documentation review, not implementation or owner approval. Business is policy authority; Requirements provide impact hints only and must not be edited in this Issue. No Architecture/technology/API/schema decisions are requested. Existing OQ-001–OQ-014 approvals and OQ-015–OQ-046 proposals remain unchanged. A merge or closed review Issue does not approve a policy.

Every finding records source file/section, exact OQ/BR/invariant/BS/GAP, a concrete counterexample or calculation, known/proposed/undefined layers, material effects and owner question. Alternatives are discussion options. The team may complete the review while owner questions remain OPEN, provided they are explicitly recorded and linked. No personal/AI judgment substitutes for an unresolved owner decision.

GitHub usernames are not available. Reviewer roles below are proposed, not assigned. Attach comments to the actual Business V2 PR head once it is published; repository paths below refer to that reviewed tree, not the current LICENSE-only main.

## Proposed reviewers

Member 6 (lead); Member 3 cross-reviews priority/ties; Member 1 cross-reviews run controls/identity; Member 5 cross-reviews observations/valuation.

## Explicit scope and reading

`docs/business/10-algo-hft.md`; `scenarios/algo-hft.md`, BS-MAT-004 and BS-EXP-008; DIA-LATENCY / DIA-OBS / DIA-METRICS; related market-data/experiment modules.

Observed information versus actual effective book; fixed observation/action delays and zero delay; precision/limits and own-account delivery scope; effective delayed cancellation; own resting same-price queue count/quantity; partial/no-fill/missing-reference execution measures; side-specific adverse slippage and fee-inclusive unit costs; unachieved and achieved time-to-fill metrics; separately configured comparison/reproducibility.

Do not choose time units/maxima/delivery/sampling/coalescing, elapsed metric origin, non-resting queue values, unsupplied equal-arrival order or pending-end disposition. Hosted strategy/account ownership is led by Experiment/Identity review. No historical-backtest algorithm, production benchmark, infrastructure or profitability claim belongs here.

## Source trace and primary gaps

OQ-008, OQ-010, OQ-031, OQ-035, OQ-037, OQ-040, OQ-041, OQ-043, OQ-046; BR-ALGO-001, BR-ORD-001, BR-MKT-002; ORD-INV-005. Primary gaps: GAP-HFT-001–GAP-HFT-004.

## Expected deliverables

1. A time/observation/action/metric terminology review table showing source-supported facts, supplied fixtures and OPEN policies.
2. Findings and exact owner questions for each of the four HFT gaps, with representative cases and material research effects.
3. Queue/slippage/no-fill calculations using actual supplied observations and effective order, including zero delay.
4. A comparison checklist stating all inputs needed for reproducibility and cross-domain handoffs for ties/end/identity/coherence.

## Acceptance criteria

- [ ] Observation time, decision, action arrival, acceptance and execution are distinct; physical time is not substituted.
- [ ] Zero delay does not override established priority or guarantee fills/profit.
- [ ] No-fill/missing-reference/unachieved metrics are unavailable where source requires; ratio/actual fees are reported truthfully.
- [ ] Achieved first/full-fill elapsed origin remains OPEN; no arbitrary subtraction origin is chosen.
- [ ] Own resting queue ahead counts active earlier same-price interest; non-resting/terminal applicability stays GAP-linked.
- [ ] BUY/SELL adverse slippage uses the decision-observed opposing quote, independent of fees and mandatory absolute protection.
- [ ] Identical-input claims identify supplied observation/effective-order assumptions; changed delay/strategy is declared as changed input.
- [ ] All four HFT gaps are reviewed without picking policy; tie/end/identity/coherence findings go to their primary lead.

## Closure evidence

Link the review findings/calculation sheet and the actual PR discussion; list remaining OPEN owner questions and cross-review acknowledgements. Record only reviewers/outcomes that actually occurred. Do not close based only on OQ keyword coverage or an AI-generated explanation.
