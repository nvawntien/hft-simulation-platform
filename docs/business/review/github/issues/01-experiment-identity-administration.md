# Issue title

Review experiment lifecycle, capital identity and administration

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Review boundaries

This is a documentation review, not implementation or owner approval. Business is policy authority; Requirements provide impact hints only and must not be edited in this Issue. No Architecture/technology/API/schema decisions are requested. Existing OQ-001–OQ-014 approvals and OQ-015–OQ-046 proposals remain unchanged. A merge or closed review Issue does not approve a policy.

Every finding records source file/section, exact OQ/BR/invariant/BS/GAP, a concrete counterexample or calculation, known/proposed/undefined layers, material effects and owner question. Alternatives are discussion options. The team may complete the review while owner questions remain OPEN, provided they are explicitly recorded and linked. No personal/AI judgment substitutes for an unresolved owner decision.

GitHub usernames are not available. Reviewer roles below are proposed, not assigned. Attach comments to the actual Business V2 PR head once it is published; repository paths below refer to that reviewed tree, not the current LICENSE-only main.

## Proposed reviewers

Member 1 (lead); Member 6 reviews time/pending observations; Member 4 reviews end balances. Business Owner supplies any later policy decision.

## Explicit scope and reading

`docs/business/01-scope-actors.md`, `02-experiment.md`, `11-administration.md`; `scenarios/experiment.md` and BS-HFT-007; DIA-EXP / DIA-FUND; gap/coverage registers.

Start/end authority, readiness, repeated/invalid controls, idle/stopped-clock behavior, natural versus manual end, exact end and pending actions/observations; separate-run reset/history; membership/account lifecycle; hosted strategy capital; research-validity classifier and admin visibility. Include empty/no-action runs, end with OPEN/PARTIALLY_FILLED/no-Trade history, and blocked 30-minute/one-hour probes.

Do not choose a duration/default, auto-end/manual-end support, pause/resume state, shared pool, account transfer or validity/visibility permission. GAP-MAT-001 belongs to the Orders/Matching review; GAP-HFT-002 belongs to HFT; GAP-BAL-001 belongs to Financial Correctness. Record cross-effects and hand off those owner questions.

## Source trace and primary gaps

OQ-003, OQ-035, OQ-036, OQ-042, OQ-043, OQ-045; BR-ACT-001, BR-ACT-002, BR-SIM-001, BR-SIM-002; SIM-INV-001. Primary gaps: GAP-EXP-001–GAP-EXP-005, GAP-ACT-001, GAP-ACT-002, GAP-ADM-001, GAP-ADM-002.

## Expected deliverables

1. A lifecycle/control review table with initial condition, actor/action, known result or exact OPEN gap, affected orders/balances/history/data and status.
2. Findings for each of the nine primary gaps, with explicit owner questions and discussion alternatives if useful.
3. End/reset scenario annotations explaining which effects are conditional on end already being effective.
4. A cross-review note from time and financial reviewers; identify impacted FR-SIM, FR-ACT and FR-ADM references without editing them.

## Acceptance criteria

- [ ] Every primary gap has a finding or explicit no-change-with-rationale entry.
- [ ] Thirty-minute/one-hour probes remain BLOCKED; no unsupported duration/end/control state is adopted.
- [ ] End releases unused commitments only; earlier Trades/fees/balances remain explained; separate reset transfers no old capital.
- [ ] Participant identity/capital, own-object authority and hosted-strategy ambiguity remain explicit.
- [ ] Invalid-research labelling/visibility/access questions remain OPEN unless a separately recorded owner decision exists.
- [ ] DIA-EXP/DIA-FUND, scenarios and tables agree; cross-domain time/funding findings are handed to their lead reviewers.

## Closure evidence

Link the review findings/calculation sheet and the actual PR discussion; list remaining OPEN owner questions and cross-review acknowledgements. Record only reviewers/outcomes that actually occurred. Do not close based only on OQ keyword coverage or an AI-generated explanation.
