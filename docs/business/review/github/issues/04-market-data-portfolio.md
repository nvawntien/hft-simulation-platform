# Issue title

Review Market Data, candles and Portfolio accounting

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Review boundaries

This is a documentation review, not implementation or owner approval. Business is policy authority; Requirements provide impact hints only and must not be edited in this Issue. No Architecture/technology/API/schema decisions are requested. Existing OQ-001–OQ-014 approvals and OQ-015–OQ-046 proposals remain unchanged. A merge or closed review Issue does not approve a policy.

Every finding records source file/section, exact OQ/BR/invariant/BS/GAP, a concrete counterexample or calculation, known/proposed/undefined layers, material effects and owner question. Alternatives are discussion options. The team may complete the review while owner questions remain OPEN, provided they are explicitly recorded and linked. No personal/AI judgment substitutes for an unresolved owner decision.

GitHub usernames are not available. Reviewer roles below are proposed, not assigned. Attach comments to the actual Business V2 PR head once it is published; repository paths below refer to that reviewed tree, not the current LICENSE-only main.

## Proposed reviewers

Member 5 (lead); Member 4 verifies units/basis/fees; Member 6 reviews observation meaning; Member 1 reviews clock/end dependencies.

## Explicit scope and reading

`docs/business/08-market-data.md`, `09-portfolio-pnl.md`; `scenarios/market-data.md`, `scenarios/portfolio.md`; DIA-DATA / DIA-CANDLE / DIA-PF / DIA-OBS.

No Trade/Last Price yet; empty/one-sided/full book quotes and Spread; STP depth change without execution data; each Trade counted once in Base/gross Quote volume; one-minute half-open candles, empty/same-time/boundary/final partial intervals; weighted and fee-inclusive basis/realized/unrealized PnL; seeded costs versus valuation; zero holdings/full liquidation residual; no own action and stale-price age at/exceeding 60 virtual seconds.

Investigate which business instant observations represent. Delivery is led by HFT; idle clock and exact end are led by Experiment. Do not invent fallback prices, fake carry-forward candles, coherent/delivered snapshots, future liquidation fees or missing-value display/UI rules.

## Source trace and primary gaps

OQ-003, OQ-011, OQ-033–OQ-039, OQ-044, OQ-046; BR-MKT-001/002/004, BR-PF-001; MKT-INV-001/002, BAL-INV-002, SIM-INV-001. Primary gap: GAP-MKT-001.

## Expected deliverables

1. A market-data inclusion/exclusion and candle-boundary review table with source/BS IDs.
2. Exact-decimal reproduction of zero/fee-inclusive PnL and proportional/full-sale residual examples.
3. A GAP-MKT-001 finding showing a concrete conflicting-time observation and its research effects/owner question.
4. Diagram and no-action review notes distinguishing ownership/basis from changing indicative market value.

## Acceptance criteria

- [ ] Absent values remain unavailable, not invented zero/fallback executions; quote availability and Last Price are independent.
- [ ] Funding, STP, cancellations and fees do not inflate Trade volume or create Last Price.
- [ ] Each actual Trade contributes once; interval start is inclusive and end exclusive; same-time first/last use known execution order.
- [ ] Empty/final partial candle behavior is conditional on known virtual interval/end facts; clock progression is not assumed.
- [ ] Weighted fee-inclusive costs, net realized proceeds, retained/full-sale residuals and locked holdings reconcile.
- [ ] At age exactly 60 the older-than-60 condition is false; stale price stays retained with age above 60.
- [ ] GAP-MKT-001 stays OPEN; grouped observation coherence/delivery is not asserted without policy.
- [ ] Reporting changes no assets and no source-proposed accounting/valuation policy is promoted.

## Closure evidence

Link the review findings/calculation sheet and the actual PR discussion; list remaining OPEN owner questions and cross-review acknowledgements. Record only reviewers/outcomes that actually occurred. Do not close based only on OQ keyword coverage or an AI-generated explanation.
