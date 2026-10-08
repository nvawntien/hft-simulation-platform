# GitHub human-review preparation

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

Prepared on 2026-10-08 for `nvawntien/hft-simulation-platform`. These files organize review and publication; they do not change business rules, resolve gaps, approve OQs, or record completed human reviews. All 36 pre-existing uncommitted files were left byte-for-byte unchanged.

## Split assessment and recommendation

An A/B split is logically possible, but a clean pair of PRs cannot be obtained by branching and staging the existing files. `main` and cached `origin/main` point to `4d64533a25592a62e597e701dfdfd84daf5724ad`, whose tree contains only LICENSE. BUSINESS, Requirements and AGENTS are all untracked. No separately committed Phase A or surviving pre-expansion checkpoint exists. The frozen Business files identify the original source, not a complete Phase A Git tree.

A disposable marker-only preview removed the eleven Phase B-only files and trimmed Phase B sections. It left **seven dangling links/anchors** in the Business README. Fourteen shared files need phase-specific treatment: eleven domain modules, README, review-guide and migration-report. README authority/status text, navigation, language review and phase evidence also need reconstruction, rather than merely removing the expansion folders. Publishing that preview would give reviewers a broken Phase A and lose important migration disclosures.

Under the request to avoid forcing a reconstructed mixed split, **no A/B branches were created**. This is a constraint on the current preparation, not a claim that a carefully reconstructed split is impossible. Such reconstruction needs separate authorization and independent fidelity/link checks. The original draft was not trimmed, moved, stashed or overwritten.

Recommended alternative: one Business V2 draft PR, with two explicit review passes and two proposed commits: first record the original existing documentation baseline, then add the complete V2 and this review bundle. Review commit 1 as provenance; review commit 2 first for migrated-source fidelity, then for behavioral analysis. A merge of documentation is not approval of proposed business policy.

## Exact branch and commit state

| Branch | Current state / intended base |
|--------|-------------------------------|
| `main` | Only actual local branch; HEAD remains `4d64533a25592a62e597e701dfdfd84daf5724ad` |
| `review/business-v2-draft` | Proposed combined-review branch from the verified base; not created |
| `review/business-v2-phase-a` | Conditional future branch after baseline import and authorized reconstruction; not created |
| `review/business-v2-phase-b` | Conditional future branch based on Phase A; not created |

No branch, commit, staging, push, PR, Issue or merge was performed. Cached remote refs have not been refreshed; fetch and reassess before publication. See [commit plan and approval steps](commit-plan.md).

## Review-ready draft bodies

| Artifact | Use |
|----------|-----|
| [Combined PR](pr-combined.md) | Recommended publication shape; still awaiting branch/commit/publication approval |
| [Conditional Phase A PR](pr-phase-a.md) | Planning body only; blocked until baseline and safe reconstruction exist |
| [Conditional Phase B PR](pr-phase-b.md) | Planning body only; blocked until Phase A is verified |
| [Validation evidence](validation-evidence.md) | Fresh source, ID, approval, link and preservation checks; verification limits |
| [Commit / publication plan](commit-plan.md) | Exact proposed contents and ordered actions requiring confirmation |

The PR and Issue bodies use repository paths and stable IDs in code formatting, so they remain portable when pasted into GitHub. They contain no fabricated Issue/PR numbers or links to unpublished branches. The source links in this index are local to the bundle and resolve now.

## Five proposed domain-review Issues

No GitHub usernames are available. Reviewers below are **proposed roles** from [the existing six-member guide](../review-guide.md), not assigned GitHub users. Publication must omit `--assignee` and `--reviewer` until real accounts are supplied. The Business Owner is the policy decision authority, not an assumed GitHub account.

| Draft | Proposed reviewers | Primary gap ownership |
|-------|--------------------|-----------------------|
| [Experiment, identity and administration](issues/01-experiment-identity-administration.md) | Member 1 lead; Members 6 and 4 cross-review | Five Experiment, two Actor and two Administration gaps |
| [Orders, matching and STP](issues/02-orders-matching-stp.md) | Member 2 lead; Member 3 matching reviewer; Members 4 and 6 cross-review | Two Order gaps and scheduled tie gap |
| [Financial correctness](issues/03-financial-correctness.md) | Member 4 lead; Members 2 and 5 cross-review | Invalid setup allocation gap; financial review across source proposals |
| [Market data and portfolio](issues/04-market-data-portfolio.md) | Member 5 lead; Members 4 and 6 cross-review | Market observation meaning gap |
| [Algo/HFT research semantics](issues/05-algo-hft-time-observations.md) | Member 6 lead; Members 1 and 3 cross-review | Four HFT gaps |

Together these assign a primary review lane to all 18 OPEN gaps. Shared time/financial effects require cross-review; the lead of a shared gap records the owner question once. Closing an Issue means the documented review deliverables were completed, not that its OQs or remaining gaps are approved.

## Review order and publication gates

1. Establish the byte-preserved baseline in Git, if authorized; distinguish tracking existing files from changing their content.
2. Check migrated source and canonical registers against both historical editions.
3. Review the five domain Issues, starting with Experiment/end/tie BLOCKER questions.
4. Record findings as comments/review notes with OQ/BR/invariant/BS/GAP references. Do not alter policy during this review-preparation task.
5. Obtain explicit decisions before any later Business policy change; reconcile Requirements only in separately authorized work.
6. Publish only the branch/PR/Issues explicitly confirmed by the owner. No merge is authorized by this preparation.

Existing known risks: two missing PDF links in unchanged Requirements; incomplete V2 Vietnamese translation; unresolved source tensions around hosted strategy capital and invalid rejected quantities; seven broken links in the marker-only A preview; absent published source baseline; no actual reviewer handles or refreshed remote tip. The [gap register](../gap-register.md), [coverage matrix](../coverage-matrix.md) and [migration report](../migration-report.md) remain authoritative for analysis/provenance.

One existing navigation detail also needs later editorial correction: the Business README's `^## BS-` / `^## GAP-` search examples omit the brackets now wrapping linked IDs. The status alternatives still match, but those expressions omit ID headings. Use these queries during review; the original README was preserved rather than silently edited:

```sh
rg -n '^## \[?BS-|^Status:' docs/business/scenarios
rg -n '^## \[?GAP-|^Status: OPEN' docs/business/review/gap-register.md
```
