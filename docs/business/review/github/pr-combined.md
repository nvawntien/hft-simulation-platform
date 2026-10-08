Business documentation is currently absent from the tracked repository, and the working V2 draft combines migrated source with new behavioral analysis. Record the existing documentation baseline unchanged, then publish the complete modular working draft so six developers can review source fidelity separately from scenario interpretations and unresolved business decisions.

**DRAFT — REQUIRES TEAM / OWNER REVIEW.** Documentation review/merge does not approve business policy. OQ-001 through OQ-014 remain APPROVED; OQ-015 through OQ-046 remain PROPOSED — OWNER REVIEW REQUIRED. Preserve all 26 BRs and 19 invariants.

### Proposed commits and impacted files

1. **Record existing documentation baseline**: `AGENTS.md`, original English/Vietnamese Business at `docs/BUSINESS.md` / `docs/BUSINESS.vi.md`, and existing `docs/REQUIREMENTS.md` / `docs/REQUIREMENTS.vi.md`. Business originals come from the verified frozen sources. Requirements and AGENTS are imported byte-for-byte; no requirement wording/status is edited. Tracking these existing files needs explicit authorization because current main contains only LICENSE.
2. **Prepare modular Business V2 review draft**: replace the two Business paths with the existing navigation adapters; add the 31 existing files under `docs/business/` and the eleven GitHub review-preparation files under `docs/business/review/github/`. Requirements and AGENTS have no changes in this commit.

Domain files are `01-scope-actors.md`, `02-experiment.md`, `03-markets-assets.md`, `04-orders.md`, `05-matching-stp.md`, `06-balance-reservation.md`, `07-trade-settlement-fees.md`, `08-market-data.md`, `09-portfolio-pnl.md`, `10-algo-hft.md`, and `11-administration.md`. Supporting files are Business README/registers/glossary, seven scenario groups plus scenario index, diagram catalogue, four existing review files, and three history files. The preparation index lists the new PR/Issue bodies and approval plan.

### Two review passes

**Pass A — migration:** compare all 35 original sections and canonical BR/register rows against `docs/business/history/BUSINESS.md`; check Vietnamese history, exact financial formulas/numbers, statuses, original headings and source links. Review the authority/language migration as an editorial change; do not infer new translation parity.

**Pass B — behavioral analysis:** inspect each domain's Phase B section, 68 BS cases, 17 Mermaid diagrams, 13 branch/transition tables, coverage matrix and 18 OPEN gaps. Require exact dependencies and clear approved/proposed/undefined layers. Four approved-derived cases are deliberately narrow; 55 complete scenarios are provisional and nine are blocked. Thirty-minute and one-hour examples do not authorize duration defaults or termination policies.

### Validation and limitations

Fresh verification preserves source/Requirements hashes, reconstructs all 35 source sections and 26 BR rows, checks 46 OQ and 19 invariant entries, canonical ID uniqueness, scenario approval inheritance, original adapter headings, Markdown tables/fences/whitespace and internal link targets. Two pre-existing PDF links remain broken in unchanged Requirements. Current Mermaid checking verifies fences/types, not a newly executed renderer/parser; prior parsing/arithmetic evidence remains in the migration report. No application/runtime checks apply.

The marker-only A/B preview had seven unresolved links; no unsafe split or phase-specific branch is included. Owner review is still needed for all proposed decisions and missing lifecycle, clock/end/tie, observation, identity, rejected-quantity and financial-policy boundaries.

### Human review checklist

- [ ] Commit 1 is an unchanged baseline import; compare original/Requirements hashes and confirm the tracking scope.
- [ ] All 35 numbered source sections, 46 OQs, 26 BRs and 19 invariants retain their meaning/status/basis.
- [ ] Every canonical definition has one stable entry; old paths/headings and internal source links are navigable.
- [ ] Source prose and Phase B interpretation are distinguishable; no proposed dependency is promoted to approval.
- [ ] Financial examples, rounding/residuals, conservation and one-time settlement reconcile.
- [ ] Invalid, terminal, foreign-object, repeated, no-action, partial/full and time-boundary cases are covered or GAP-linked.
- [ ] Diagrams and decision tables agree with scenario outcomes; check visual rendering on GitHub.
- [ ] Hosted strategy/account and rejected-quantity tensions remain visible and unresolved.
- [ ] English canonical / partial Vietnamese aid policy is accurately disclosed.
- [ ] Review findings are recorded through the five domain-review Issues; closing/merging documentation does not approve OQs.
- [ ] Requirements content, business policy and Architecture scope are unchanged by this PR.

### Publication state

Proposed head: `review/business-v2-draft`; base: `main` after confirming the remote base. This body is prepared locally; no branch/commit/PR/Issue is published and no merge is authorized. Actual reviewer handles, Issue links and PR number will be added only after supplied/created with explicit confirmation.
