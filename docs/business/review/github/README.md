# GitHub human-review preparation

**DRAFT — REQUIRES TEAM / OWNER REVIEW.**

Updated on 2026-10-08 for `nvawntien/hft-simulation-platform`, at the metadata checkpoint before the authorized push and Draft PR creation. These files organize review and publication; they do not change business rules, resolve gaps, approve OQs, or record completed human reviews. All 47 untracked files in the original workspace remain byte-for-byte unchanged. Publication is authorized for one Draft PR; the five Issue drafts still require separate confirmation.

## Split assessment and recommendation

At the original split assessment, `main` contained only LICENSE and BUSINESS, Requirements and AGENTS were untracked. No separately committed Phase A or surviving pre-expansion checkpoint existed. The frozen Business files identify the original source, not a complete Phase A Git tree. The subsequently approved baseline and complete V2 commits below do not reconstruct that missing Phase A tree.

A disposable marker-only preview removed the eleven Phase B-only files and trimmed Phase B sections. It left **seven dangling links/anchors** in the Business README. Fourteen shared files need phase-specific treatment: eleven domain modules, README, review-guide and migration-report. README authority/status text, navigation, language review and phase evidence also need reconstruction, rather than merely removing the expansion folders. Publishing that preview would give reviewers a broken Phase A and lose important migration disclosures.

Under the request to avoid forcing a reconstructed mixed split, **no A/B branches were created**. Such reconstruction is outside the approved combined publication route. The original workspace was not trimmed, moved, stashed or overwritten. Eight copied entry-point/scenario files had one extra final blank line removed before commit 2; their business text is unchanged.

Approved publication route: one Business V2 Draft PR with two review rounds. Two existing commits record the original documentation baseline and complete V2; a third commit updates only GitHub review metadata. Review commit 1 as provenance; review commit 2 for source fidelity and then behavioral analysis; review commit 3 for publication metadata. Publication or merge of documentation does not approve business policy.

## Verified branch and commit checkpoint

| Branch | Current state / intended base |
|--------|-------------------------------|
| `main` / `origin/main` | SSH fetch verified `4d64533a25592a62e597e701dfdfd84daf5724ad`; original workspace remains on main |
| `review/business-v2-draft` | Existing branch in `/tmp/hft-business-v2-github`; main is its ancestor; worktree was clean before metadata edits |
| `review/business-v2-phase-a` | Unselected conditional planning alternative; not created |
| `review/business-v2-phase-b` | Unselected conditional planning alternative; not created |

Existing commits are `f97b8d4cb72fe722bf6e8bf57fd032dda025f5ce` (**Record existing documentation baseline**) and `f54bfdd4e11ebc93a0efe4e2221fadf250273091` (**Prepare modular Business V2 review draft**). They remain unchanged. At this checkpoint, the remote review branch and a matching PR were absent. The authorized next actions are the metadata commit, a normal push of only the review branch, and one Draft PR into main. GitHub CLI and SSH checks succeeded without changing credentials or the remote URL. See [commit and publication record](commit-plan.md); use the live GitHub PR for the later publication result.

## Review-ready draft bodies

| Artifact | Use |
|----------|-----|
| [Combined PR](pr-combined.md) | Approved Draft PR publication shape and two human review rounds |
| [Conditional Phase A PR](pr-phase-a.md) | Preserved planning alternative; not selected or authorized for publication |
| [Conditional Phase B PR](pr-phase-b.md) | Preserved planning alternative; not selected or authorized for publication |
| [Validation evidence](validation-evidence.md) | Source, ID, approval, link and preservation evidence with verification limits |
| [Commit / publication record](commit-plan.md) | Existing commits, authorized metadata update/publication and remaining boundaries |

The PR and Issue bodies use repository paths and stable IDs in code formatting, so they remain portable when pasted into GitHub. They contain no fabricated Issue/PR numbers or links to unpublished branches. The source links in this index are local to the bundle and resolve now.

## Five proposed domain-review Issues

GitHub collaborator inspection returned only `nvawntien`, the authenticated account that will author the PR. No usernames for the six team members have been supplied. Reviewers below are **proposed roles** from [the existing six-member guide](../review-guide.md), not assigned GitHub users. No review request or Issue assignment is part of this publication. The Business Owner is the policy decision authority, not an assumed GitHub account.

| Draft | Proposed reviewers | Primary gap ownership |
|-------|--------------------|-----------------------|
| [Experiment, identity and administration](issues/01-experiment-identity-administration.md) | Member 1 lead; Members 6 and 4 cross-review | Five Experiment, two Actor and two Administration gaps |
| [Orders, matching and STP](issues/02-orders-matching-stp.md) | Member 2 lead; Member 3 matching reviewer; Members 4 and 6 cross-review | Two Order gaps and scheduled tie gap |
| [Financial correctness](issues/03-financial-correctness.md) | Member 4 lead; Members 2 and 5 cross-review | Invalid setup allocation gap; financial review across source proposals |
| [Market data and portfolio](issues/04-market-data-portfolio.md) | Member 5 lead; Members 4 and 6 cross-review | Market observation meaning gap |
| [Algo/HFT research semantics](issues/05-algo-hft-time-observations.md) | Member 6 lead; Members 1 and 3 cross-review | Four HFT gaps |

Together these assign a primary review lane to all 18 OPEN gaps. Shared time/financial effects require cross-review; the lead of a shared gap records the owner question once. Closing an Issue means the documented review deliverables were completed, not that its OQs or remaining gaps are approved.

## Review order and publication gates

1. Review the byte-preserved baseline already recorded in commit 1; distinguish tracking existing files from changing their content.
2. **Round 1:** check source fidelity, canonical IDs, approval boundaries and both historical editions.
3. **Round 2:** review scenarios, decision tables, Mermaid diagrams, gaps and semantic consistency, starting with Experiment/end/tie BLOCKER questions. Use PR comments until Issue publication is separately approved.
4. Record findings as comments/review notes with OQ/BR/invariant/BS/GAP references. Do not alter policy during this review-preparation task.
5. Obtain explicit decisions before any later Business policy change; reconcile Requirements only in separately authorized work.
6. Keep the published PR in Draft. Do not merge or mark Ready for Review. Do not publish the five Issues without separate confirmation.

Existing known risks: 18 OPEN gaps; two missing PDF links in unchanged Requirements; incomplete V2 Vietnamese translation; unresolved source tensions around hosted strategy capital and invalid rejected quantities; human semantic review and GitHub diagram rendering remain outstanding. The seven broken links belong only to the unselected marker-only A preview. The [gap register](../gap-register.md) and [coverage matrix](../coverage-matrix.md) remain unchanged. The [migration report](../migration-report.md) is preserved provenance from the original V2 preparation; its statements that no commits/publication were performed describe that earlier checkpoint, not the current Git state.

One existing navigation detail also needs later editorial correction: the Business README's `^## BS-` / `^## GAP-` search examples omit the brackets now wrapping linked IDs. The status alternatives still match, but those expressions omit ID headings. Use these queries during review; the original README was preserved rather than silently edited:

```sh
rg -n '^## \[?BS-|^Status:' docs/business/scenarios
rg -n '^## \[?GAP-|^Status: OPEN' docs/business/review/gap-register.md
```
