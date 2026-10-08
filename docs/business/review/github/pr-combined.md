This PR records the existing documentation baseline and organizes Business V2 into domain modules, canonical registers, source-traced scenarios and a review bundle. The six-person team can review migrated source separately from behavioral analysis and unresolved policy. The complete draft stays in one PR because a marker-only Phase A extraction required unsafe reconstruction and left seven dangling links in its disposable preview.

**DRAFT — REQUIRES TEAM / OWNER REVIEW. Business V2 is not approved.** Publishing, reviewing or merging documentation does not approve business policy. OQ-001 through OQ-014 remain APPROVED; OQ-015 through OQ-046 remain PROPOSED — OWNER REVIEW REQUIRED. All 46 OQ, 26 BR and 19 invariant IDs are preserved. All 18 gaps remain OPEN.

### Commits and impacted files

1. **Record existing documentation baseline** — `f97b8d4cb72fe722bf6e8bf57fd032dda025f5ce`: add `AGENTS.md`, original English/Vietnamese Business at `docs/BUSINESS.md` / `docs/BUSINESS.vi.md`, and existing `docs/REQUIREMENTS.md` / `docs/REQUIREMENTS.vi.md`, byte-for-byte. This records provenance; no requirement wording or status changes.
2. **Prepare modular Business V2 review draft** — `f54bfdd4e11ebc93a0efe4e2221fadf250273091`: update the two Business entry points and add 42 files under `docs/business/`, including 31 V2 files and eleven GitHub review-preparation files. Requirements and AGENTS have no changes in this commit. Eight copied adapter/scenario files lose only one extra final blank line; the original workspace is untouched.
3. **Update GitHub review publication metadata** — separate follow-up touching only six metadata files under `docs/business/review/github/`. The two earlier commits retain their hashes. Business rules, scenarios, diagrams, histories, Requirements, approval statuses and the 18 OPEN gaps are unchanged from commit 2.

Domains cover scope/actors, experiments, markets/assets, orders, matching/STP, balance/reservation, settlement/fees, market data, portfolio/PnL, Algo/HFT and administration. Supporting files include navigation, decisions/invariants/glossary, eight scenario/index files, the diagram catalogue, gap/coverage/review documents, exact historical sources and local PR/Issue drafts. No application, Architecture or infrastructure decision is introduced.

### Round 1 — Source fidelity, preserved IDs, approval boundaries and historical sources

Compare all 35 numbered original sections and canonical rule/register rows against `docs/business/history/BUSINESS.md` and the original Business in commit 1. Check Vietnamese history, exact financial formulas/numeric constraints/examples, source dependencies and original headings/links. Review the English canonical / partial Vietnamese editorial policy without assuming translation parity.

- [ ] Baseline Business, Requirements and AGENTS bytes match their designated original sources; both history files equal the Business originals in commit 1.
- [ ] All 46 OQ, 26 BR and 19 invariant IDs, original meanings, formulas, numbers and approval bases are preserved.
- [ ] OQ-001–OQ-014 remain APPROVED and OQ-015–OQ-046 remain PROPOSED — OWNER REVIEW REQUIRED; no new approval is inferred.
- [ ] Each rule/decision has one canonical entry; entry points, numbered headings and relative source links remain navigable.
- [ ] Migrated source and new analysis are visibly distinguishable; historical review records are not current owner approval.
- [ ] Requirements/AGENTS have no content changes after baseline; both existing commit hashes remain unchanged.

### Round 2 — Scenarios, decision tables, Mermaid diagrams, business gaps and semantic consistency

Review domain Phase B analysis, 68 scenarios, 13 branch/transition tables, 17 Mermaid diagrams, the coverage matrix and 18 OPEN gaps. Four scenarios isolate narrow approved-derived behavior; 55 complete scenarios remain PROVISIONAL and nine are OPEN / BLOCKED. Any proposed dependency keeps a complete scenario provisional. Thirty-minute and one-hour probes do not establish supported defaults, clock progression or termination policy.

- [ ] Scenario preconditions, triggers, normal/alternative/invalid/no-action/time cases, effects and exact traces agree with source.
- [ ] Invalid, terminal, foreign-object, repeated, partial/full and time-boundary outcomes are covered or linked to an OPEN gap.
- [ ] Financial examples, reserves/releases, fee rounding, asset conservation, retained cost basis and exactly-once settlement reconcile.
- [ ] Decision-table branches, scenario outcomes and Mermaid states/arrows agree; inspect visual rendering on GitHub.
- [ ] Same-time ordering, experiment clock/end, latency/observation delivery and ownership ambiguities remain OPEN where no source selects a policy.
- [ ] Hosted strategy/account and malformed rejected-quantity source tensions remain visible and unresolved.
- [ ] Coverage describes meaningful actions/transitions, rather than claiming business completeness because IDs are listed.
- [ ] Record findings through PR comments. The five domain-review Issue drafts are not posted; their publication awaits separate owner confirmation.

### Validation and remaining limitations

Source reconstruction preserves all 35 sections and 26 BR rows; canonical checks retain 46 OQ and 19 invariants. Original/history/Requirements hashes, scenario approval inheritance, source headings, 5,559 relative file/anchor links, 203 Markdown tables, fence structure and whitespace are checked. All 17 Mermaid blocks pass syntax parsing with Mermaid 12.1.0 installed only under `/tmp`; GitHub layout and semantic correctness still need human review. No application/runtime test applies. The migration report's earlier 88 decimal assertions are historical evidence and are not claimed as rerun for this metadata update.

There are **18 OPEN gaps: 4 BLOCKER, 11 HIGH and 3 MEDIUM**. Owner decisions are still needed for proposed OQs and unresolved experiment lifecycle/clock/end/ties, observation/latency, account/strategy identity, administration, rejected-quantity and setup funding behavior. This PR does not resolve them.

Two pre-existing links in unchanged Requirements EN/VI point to the absent `docs/HFT-Simulation-Platform-Business-Specification-V1-Vietnamese.pdf`. They are reported and retained. English modules are canonical; V2 Vietnamese coverage is partial, and full sentence-by-sentence historical or V2 translation parity is not asserted. The preserved migration report describes its original preparation checkpoint; current commit/publication metadata is in `docs/business/review/github/`.

### Publication boundary

Head: `review/business-v2-draft`; base: `main`. Publish as **Draft** and keep that status. No merge, Ready for Review transition, Business decision approval, reviewer assignment or GitHub Issue publication is included. Reviewers remain proposed Member 1–6 roles until their GitHub usernames are supplied; current collaborator inspection returned only the PR author `nvawntien`.
