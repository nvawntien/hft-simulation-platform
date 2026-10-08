# Migration inventory and review evidence

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](../README.md)

## Source inventory before editing

On 2026-10-08, `docs/` contained only BUSINESS.md, BUSINESS.vi.md, REQUIREMENTS.md and REQUIREMENTS.vi.md. No PDF, existing business modules, application, or Architecture file was present. All four documentation files and AGENTS.md were untracked at task start. Requirements remain byte-for-byte unchanged.

## Phase A — original introductory metadata

The following introductory source text is retained for provenance. Its V1 language/entry-point editing instructions and whole-document review label are historical; the current editorial authority and translation policy are in the V2 README. Trading meanings and decision statuses are preserved.

<a id="source-section-1"></a>

## 1. Purpose of This Document

**DRAFT — REQUIRES FINAL OWNER REVIEW**

This business/domain source of truth helps the six-person CS team understand and review one coherent V1 trading model. It explains trading without requiring prior financial expertise. Architecture, technology choices, and implementation are outside scope.

[Vietnamese edition](../history/BUSINESS.vi.md) expresses the same decisions, identifiers, examples, and approval boundaries. A translation is not a separate approval. Both editions must change together when decisions change.

| Label | Meaning |
|-------|---------|
| ESTABLISHED DOMAIN CONCEPT | Educational knowledge about trading; not every venue uses identical policies. |
| PROJECT V1 RULE — APPROVED | Explicit owner decisions [OQ-001](../decisions.md#oq-001) through [OQ-014](../decisions.md#oq-014). These are binding for V1. |
| PROJECT V1 RULE — PROPOSED — OWNER REVIEW REQUIRED | Concrete Codex recommendations [OQ-015](../decisions.md#oq-015) through [OQ-046](../decisions.md#oq-046). The narrative uses them to describe the recommended V1, but they are not owner-approved. |

Section 32 is the decision register. Historical OQ identifiers are retained for traceability even though each now has a selected decision. Section 31 distinguishes approved business rules from recommendations. There are no unresolved multiple-choice decisions in this revision.

Numbers use commas for thousands and periods for decimals: `90,000 USDT`, `0.4 BTC`. Prices are Quote units per Base unit. Examples assume adequate funding and different accounts unless stated otherwise. Examples marked **zero-fee scenario** use configured zero rates; V1 still supports fees. Amounts are simulated, not real-market quotations.

## Phase A — historical review record

<a id="source-section-35"></a>

## 35. Review Log

### Review Status

**DRAFT — REQUIRES FINAL OWNER REVIEW**

[OQ-001](../decisions.md#oq-001) through [OQ-014](../decisions.md#oq-014) remain APPROVED. [OQ-015](../decisions.md#oq-015) through [OQ-046](../decisions.md#oq-046) remain PROPOSED — OWNER REVIEW REQUIRED in both language editions. No final owner approval of those recommendations is asserted.

### Review Log

| Date | Reviewer | Section | Decision / Comment |
|------|----------|---------|--------------------|
| 2026-10-06 | Codex, incorporating owner instructions | 1–35; [OQ-001](../decisions.md#oq-001)–[OQ-014](../decisions.md#oq-014) | Incorporated the decisions explicitly approved by the owner in the task. No human reviewer name was supplied. |
| 2026-10-06 | Codex | 1–35; [OQ-015](../decisions.md#oq-015)–[OQ-046](../decisions.md#oq-046) | Selected concrete recommended V1 decisions requiring owner review; performed a document-wide consistency pass and aligned the English/Vietnamese editions. This is not final owner approval. |

## Phase B — migration findings and verification evidence

This is an analyst working draft, not a business-completeness certificate. All new analysis follows the current Business source; Requirements supplied impact/coverage clues only. No human review or new policy approval was performed in this task.

<a id="phase-review-boundary"></a>

### Phase review boundary

Phase A was checked before behavioral expansion: all 35 original numbered sections, all 26 BR rows, the 46 OQ rows and 19 invariant rows were retained. Both historical files were copied byte-for-byte before the entry points were replaced. The original §31 rule rows have been distributed by domain; the README maps each original section and each canonical BR location. Navigation wrappers, stable anchors and relative links are structural changes only. Original source §1/§35 statements are labelled historical metadata/review records rather than new review outcomes.

Phase B is the explicitly named section in each of the eleven domain modules and in this review report/guide. All scenario, diagram, gap and coverage files are new Phase B analysis. APPROVED-DERIVED is a narrow source-derivation label; every complete scenario involving a proposed rule remains PROVISIONAL. Supplied fixture times, observations and effective order do not decide how the unresolved clock/arrival/delivery policies work.

To review Phase A independently: use the frozen source, the README section/BR map, and just the migrated sections/canonical rows before each **Phase B** marker. Compare source prose after removing generated ID links/anchors; the only moved-source link target adjustment is the historical Vietnamese link in §1. The final fidelity check uses this reconstruction, not whole-file equality for expanded modules. The source hashes below identify the reproducible original checkpoint. No separate commit was made because committing was not authorized; phase labels and source evidence keep migration and analysis separately reviewable. A later authorized change can split them into two commits/PRs without treating analysis as source policy.

### Source preservation and unchanged downstream documents

| Original artifact | SHA-256 at inspection and final check | Current treatment |
|-------------------|--------------------------------------|-------------------|
| BUSINESS.md | `ada66d2983f2ecfffea5c1a98042147bef534a8e3869c213a6e8de5316494c70` | Exact frozen copy in history; original path now navigation adapter |
| BUSINESS.vi.md | `0ff13f2c3f225d579087c7c2fc25aea1d0b55f3d85ecb5da938488a222985f77` | Exact frozen copy in history; Vietnamese adapter/aids, not an independent rule source |
| REQUIREMENTS.md | `399efdcee1684c9042070a00faa964ace485d8b2f88b83efa2d1f8b51334ce51` | Unchanged; no downstream rules rewritten |
| REQUIREMENTS.vi.md | `0178b38403d8781607c78b3af3622912b3b95426aa8a8b090c5488ff97918ae7` | Unchanged; no translation or traceability rewrite |

### Created / modified file tree

Only Business documentation was changed. `history/` and this migration evidence are the two deliberate additions to the requested tree: recovery/provenance would otherwise be lost when the old paths become adapters. Mermaid stays in the shared diagram catalogue instead of a separate file per diagram. No file per OQ/BR/FR was created.

```text
docs/
├── BUSINESS.md                       # modified compatibility entry point
├── BUSINESS.vi.md                    # modified Vietnamese compatibility entry point
└── business/                         # 31 new Markdown files
    ├── README.md
    ├── 01-scope-actors.md
    ├── 02-experiment.md
    ├── 03-markets-assets.md
    ├── 04-orders.md
    ├── 05-matching-stp.md
    ├── 06-balance-reservation.md
    ├── 07-trade-settlement-fees.md
    ├── 08-market-data.md
    ├── 09-portfolio-pnl.md
    ├── 10-algo-hft.md
    ├── 11-administration.md
    ├── decisions.md
    ├── invariants.md
    ├── glossary.md
    ├── scenarios/
    │   ├── README.md
    │   ├── experiment.md
    │   ├── orders.md
    │   ├── matching-stp.md
    │   ├── balance-settlement.md
    │   ├── market-data.md
    │   ├── portfolio.md
    │   └── algo-hft.md
    ├── diagrams/
    │   └── README.md
    ├── review/
    │   ├── gap-register.md
    │   ├── coverage-matrix.md
    │   ├── review-guide.md
    │   └── migration-report.md
    └── history/
        ├── README.md
        ├── BUSINESS.md
        └── BUSINESS.vi.md
```

### Deliverable counts

| Item | Count / approval boundary |
|------|---------------------------|
| Original source sections | 35, all retained and mapped |
| Canonical OQs | 46: 14 APPROVED, 32 PROPOSED — OWNER REVIEW REQUIRED; unchanged |
| Canonical BRs | 26, exact original descriptions/rationales/statuses/dependencies retained |
| Canonical invariants | 19, exact original meanings and bases retained |
| Business scenarios | 68: 4 APPROVED-DERIVED narrow cores, 55 PROVISIONAL, 9 OPEN / BLOCKED |
| Scenario groups | Experiment 8; Orders 19; Matching/STP 9; Balance/Settlement/Fees 11; Market Data 8; Portfolio 5; Algo/HFT 8 |
| Mermaid diagrams | 17: 10 flowcharts, 6 sequence diagrams, 1 state diagram |
| Branch/transition tables added | 13: 10 decision tables, 2 combined transition/decision tables, 1 Order transition table |
| OPEN business gaps | 18: 4 BLOCKER, 11 HIGH, 3 MEDIUM, 0 LOW; priorities proposed |

### OPEN gaps by domain and proposed priority

| Domain | BLOCKER | HIGH | MEDIUM | LOW | Total |
|--------|---------|------|--------|-----|-------|
| Experiment | 3 | 1 | 1 | 0 | 5 |
| Matching | 1 | 0 | 0 | 0 | 1 |
| Algo / HFT | 0 | 3 | 1 | 0 | 4 |
| Actors / Trading Account | 0 | 2 | 0 | 0 | 2 |
| Administration | 0 | 2 | 0 | 0 | 2 |
| Orders | 0 | 2 | 0 | 0 | 2 |
| Balance / setup funding | 0 | 0 | 1 | 0 | 1 |
| Market Data Observation | 0 | 1 | 0 | 0 | 1 |
| Total | 4 | 11 | 3 | 0 | 18 |

No gap is allocated to Trade/Fees or Portfolio solely to multiply combinations. Their local formulas are specified in proposed source; their complete flows still inherit unresolved Experiment/identity/time controls. Missing explanation, existing exclusions, NFR targets, UI wording and technical mechanisms are explicitly distinguished from Business gaps in the register.

### Source decisions still requiring owner review

Every original [OQ-015](../decisions.md#oq-015) through [OQ-046](../decisions.md#oq-046) remains proposed. Review these canonical entries rather than adopting their derived scenarios automatically:

| Review topic | Existing proposed decisions |
|--------------|----------------------------|
| Fee asset/destination/rates and numeric correctness | [OQ-015](../decisions.md#oq-015), [OQ-016](../decisions.md#oq-016), [OQ-017](../decisions.md#oq-017), [OQ-018](../decisions.md#oq-018), [OQ-019](../decisions.md#oq-019), [OQ-020](../decisions.md#oq-020), [OQ-046](../decisions.md#oq-046) |
| Funding/reservation/release/settlement | [OQ-021](../decisions.md#oq-021), [OQ-022](../decisions.md#oq-022), [OQ-023](../decisions.md#oq-023), [OQ-024](../decisions.md#oq-024) |
| Observable Order lifecycle/change/persistence/precedence | [OQ-025](../decisions.md#oq-025), [OQ-026](../decisions.md#oq-026), [OQ-027](../decisions.md#oq-027), [OQ-028](../decisions.md#oq-028), [OQ-029](../decisions.md#oq-029), [OQ-030](../decisions.md#oq-030), [OQ-031](../decisions.md#oq-031), [OQ-032](../decisions.md#oq-032) |
| Portfolio accounting/valuation | [OQ-033](../decisions.md#oq-033), [OQ-034](../decisions.md#oq-034) |
| Experiment/scenario liquidity/public data/volume/candles | [OQ-035](../decisions.md#oq-035), [OQ-036](../decisions.md#oq-036), [OQ-037](../decisions.md#oq-037), [OQ-038](../decisions.md#oq-038), [OQ-039](../decisions.md#oq-039) |
| Algorithmic participation/latency research | [OQ-040](../decisions.md#oq-040), [OQ-041](../decisions.md#oq-041) |
| Administration/participant capital/price initialization/history | [OQ-042](../decisions.md#oq-042), [OQ-043](../decisions.md#oq-043), [OQ-044](../decisions.md#oq-044), [OQ-045](../decisions.md#oq-045) |

Gap questions elaborate missing boundaries or expose tensions; they do not replace these existing recommendations. No new OQ is allocated and no proposal is approved here.

### Discrepancies and unresolved source tensions

| Finding | Evidence and treatment |
|---------|------------------------|
| Referenced Business PDF absent | Both Requirements editions link to `HFT-Simulation-Platform-Business-Specification-V1-Vietnamese.pdf` and state it was inspected. No PDF is present in this checkout. Historical inspection cannot be verified here; the two existing missing-file links remain in unchanged Requirements and need a separately authorized artifact/link correction. |
| Strategy hosted by scenario account versus separate participant pool | Business §27/[OQ-036](../decisions.md#oq-036) allows a scenario account to host strategies; §4/[OQ-043](../decisions.md#oq-043) proposes separate account/capital per strategy/scenario participant. The relationship of host and participant is not explicit. [GAP-ACT-002](gap-register.md#gap-act-002) / [BS-HFT-007](../scenarios/algo-hft.md#bs-hft-007) preserve both statements and block the ambiguous capital/identity allocation. Requirements TBD-004 also recognizes this ambiguity. |
| Invalid rejected quantity versus every-Order invariant | Business §6 rejects invalid quantities; §13 proposes REJECTED; [ORD-INV-001](../invariants.md#ord-inv-001)/002 require unchanged original quantity and accounting for every Order. Negative/malformed submitted quantities have no defined compatible quantity facts/invariant scope. [GAP-ORD-002](gap-register.md#gap-ord-002) / [BS-ORD-019](../scenarios/orders.md#bs-ord-019) retain the original invariant and rejection rule; FR-ORD-016 downstream needs human reconciliation after a decision. |
| Historical editorial policy versus V2 canonical policy | The old source requested synchronized English/Vietnamese edits. Its full content remains frozen; current entry points declare English modules canonical and Vietnamese aids partial. This is a disclosed documentation-authority migration, not a trading decision or asserted new translation parity. |

No approval-status disagreement was found between the original English/Vietnamese decision registers. Requirements' introductory statement about no substantive conflict is historical; it must not suppress the documented TBDs or the tensions above. No document was silently selected as authority to resolve them.

<a id="language-review"></a>

### Language review

English modular Business is canonical. Original English and Vietnamese editions remain recoverable as exact snapshots. Vietnamese entry/navigation/review explanations aid the team; new V2 domain analyses, scenarios and gaps have **not** been fully translated. Do not describe the two entry points as identical compiled specifications.

Checks on historical editions covered all OQ/BR/invariant identifier sets and approval-status cells, numeric constraints/examples, and the source formulas for reserve, fees, settlement, weighted basis, virtual candle boundaries and stale-price conditions. Numeric-token multisets agreed in all 35 original numbered sections; OQ status cells and BR status/dependency cells also agreed. Manual comparison of those financial/time statements found no differing policy in the reviewed areas. Requirements identifier/traceability-table mappings were compared across English/Vietnamese as downstream evidence. Structural/numeric equality cannot prove full semantic translation parity; complete sentence-by-sentence historical parity and full V2 translation parity are not asserted.

### Validation results and practical limits

Structural verification reconstructs all original source sections and BR/register rows after removing generated anchors/ID links, and compares snapshot/Requirements hashes. It checks canonical ID uniqueness; OQ/BR status/dependency preservation; scenario fields/status inheritance/diagram links; every new relative Markdown file/anchor target; tables, fences and whitespace. Old numbered adapter headings remain intact, so existing Business section links and Requirements source ID navigation remain usable. The missing PDF links in unchanged Requirements are reported separately rather than counted as new link failures.

Financial verification passed 88 exact-decimal assertions for source price improvement, partial fills, reserves/releases, full two-Trade ledger, conservation, weighted cost, fee-inclusive PnL, liquidity/impact, average-price display and candle volumes. It also checks new rounding-residual, maker-greater-than-taker, tiny-fee/reserve, protected-full/partial and slippage fixtures. Arithmetic verification does not approve the formula's policy.

All 17 Mermaid blocks passed parsing with Mermaid 12.1.0 installed under `/tmp` for validation only. No dependency/tooling was introduced into the repository. This verifies Mermaid syntax; visual layout in GitHub and human agreement between every diagram and business text still need team review. No application/build/runtime test is applicable in the current documentation-only phase. `git diff --check` is supplemented by direct checks of untracked Markdown because the documentation was already untracked at task start.

These checks establish migration fidelity and structural consistency. Human policy decisions, semantic review and behavioral completeness remain outstanding. Review log entries above are original history; this evidence records analyst work and automated checks, never invented owner approvals.

### Behavior that could not safely be specified

Complete experiment controls/states/start/end authority; required duration/default/support bounds; natural/manual end; idle clock progress and stopped submissions; pause/resume availability; exact end inclusivity and pending actions/observations; scheduled-arrival tie allocation; virtual delay precision/limits; coherent observation/delivery/private-update meaning; achieved fill timing origin; queue metric applicability outside resting Orders; participant/account lifecycle and hosted strategy capital; validity classifier/admin visibility; invalid setup readiness/partial funding outcomes; repeated submission identity; malformed rejected quantity facts; historical access/retention/export obligations. These remain OPEN in the register. Thirty-minute and one-hour scenarios are blocked policy probes, not supported presets.

### Recommended six-person review order

All members read navigation, scope, decision statuses and invariants first. Then verify Phase A fidelity; resolve BLOCKER Experiment/end/tie questions; review Orders/Matching/STP; reconcile Reservation/Settlement/Fees; review Market Data/Portfolio; finally challenge HFT comparison, observation and ownership assumptions. The review guide assigns six primary responsibilities and required cross-reviews. Financial and time findings must be shared across groups before any complete flow can be accepted.

### Proposed PR title and description

Title: **Modularize Business V2 draft and expose behavioral gaps**

The monolithic Business documents made cross-domain outcomes and unresolved time/ownership policies difficult to review. Move authoritative English source into domain modules, preserve both original editions exactly, and retain the old paths/numbered headings as navigation adapters. Clearly separate migrated source from 68 source-traced scenarios, 17 Mermaid diagrams, 13 branch/transition tables and 18 OPEN gaps; preserve all 46 OQ, 26 BR and 19 invariant entries and their approval boundaries.

Validation: source/register reconstruction, original/Requirements hashes, internal ID/file/anchor links, scenario/status structure, table/fence/whitespace checks, exact-decimal financial examples and Mermaid parsing. Owner review remains required for [OQ-015](../decisions.md#oq-015)–[OQ-046](../decisions.md#oq-046) and documented time/identity/rejected-quantity gaps. Missing PDF links are reported; Requirements and Architecture are untouched. Overall status remains DRAFT — REQUIRES TEAM / OWNER REVIEW.

Suggested future authorized split: (A) **Migrate Business source into canonical domain modules**; (B) **Add source-traced scenarios, diagrams and open-gap review**. No commit, push, merge, PR publication or human approval was performed.
