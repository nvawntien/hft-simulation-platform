# Commit and Draft PR publication record

**DRAFT — REQUIRES TEAM / OWNER REVIEW.**

## Authorization boundary

The owner explicitly approved the two original local commits and subsequently approved one metadata-only commit, a normal push of `review/business-v2-draft`, and one Draft PR into `main`. The repository instruction to obtain explicit authorization is satisfied for those actions. No force push, push to main, merge, Ready for Review transition, Business decision approval or publication of the five Issues is authorized.

Work is performed in `/tmp/hft-business-v2-github`. The original workspace remains on main, with all 47 untracked files unchanged. Credentials and the origin URL are not modified. Authentication failure or branch divergence requires stopping and reporting the condition.

## Existing commits — preserve these hashes

| Commit | Hash | Recorded contents |
|--------|------|-------------------|
| Record existing documentation baseline | `f97b8d4cb72fe722bf6e8bf57fd032dda025f5ce` | Five files: AGENTS, original Business EN/VI, existing Requirements EN/VI; byte-preserved |
| Prepare modular Business V2 review draft | `f54bfdd4e11ebc93a0efe4e2221fadf250273091` | 44 paths: two Business adapters and 42 Business/review additions; Requirements and AGENTS unchanged |

The original Business bytes in commit 1 equal the two frozen history files in commit 2. Eight copied adapter/scenario files had one extra final blank line removed before commit 2; the original untracked files were left unchanged. The PR against LICENSE-only main introduces 47 paths; later metadata changes reuse existing paths.

These are baseline and complete V2 commits, not separately reconstructed Phase A/B commits. The approved route remains a single PR with two human review rounds.

## Metadata-only follow-up

Commit title: **Update GitHub review publication metadata**.

Only these six files may differ from commit 2:

- `docs/business/review/github/README.md`
- `docs/business/review/github/commit-plan.md`
- `docs/business/review/github/pr-combined.md`
- `docs/business/review/github/pr-phase-a.md`
- `docs/business/review/github/pr-phase-b.md`
- `docs/business/review/github/validation-evidence.md`

This follow-up records existing commits, current authorization, verified remote/worktree state, validation evidence and the exact PR body. The five Issue drafts, all Business rules, scenarios, diagrams, canonical registers, historical sources, gap/coverage files, Requirements and AGENTS remain byte-identical to commit 2. No original commit is amended or rebased. The metadata commit's own hash is obtained from Git after creation; it cannot be embedded in its own contents.

## Verified checkpoint before metadata commit

On 2026-10-08, GitHub CLI authentication and `git fetch origin` over SSH succeeded. `main` and refreshed `origin/main` were both `4d64533a25592a62e597e701dfdfd84daf5724ad`. That base is the direct parent of commit 1 and an ancestor of the review branch. The review worktree was clean with commit 2 as HEAD. The remote review branch and a matching PR were absent at this checkpoint.

Origin remains `git@github.com:nvawntien/hft-simulation-platform.git`. GitHub reports repository `nvawntien/hft-simulation-platform`, default branch main, and push permission for the authenticated account. No credential or remote configuration was changed.

Before publication, verify the metadata diff contains only the six named files, both old commits remain in the ancestry with their original hashes, all other tracked blobs equal commit 2, and all 47 original untracked file hashes still match. Rerun source/ID/status/link/Markdown checks, Mermaid parsing and `git diff --cached --check`. Fetch and check remote heads again; stop on divergence rather than force or rebase.

## Authorized branch and Draft PR publication

Run from the review worktree after the metadata commit and successful checks:

```sh
git push -u origin review/business-v2-draft:refs/heads/review/business-v2-draft
gh pr create --repo nvawntien/hft-simulation-platform --base main --head review/business-v2-draft --draft --title "docs(business): modularize Business V2 and expose behavioral gaps" --body-file docs/business/review/github/pr-combined.md
```

Use the body file exactly. No reviewer is assigned automatically: the collaborator list currently contains only the PR author `nvawntien`; team usernames remain unavailable. Check for an existing PR before creating one and verify the created PR is OPEN and Draft, has the expected base/head and head hash, and contains the two review rounds and documented limitations. The live GitHub PR and final execution report supply the publication URL and result after this checkpoint; this document does not invent them in advance.

## Review commands

These commands read the isolated branch without checking it out over the original untracked workspace:

```sh
git -C /tmp/hft-business-v2-github log --oneline main..review/business-v2-draft
git -C /tmp/hft-business-v2-github diff 4d64533 f97b8d4 -- AGENTS.md docs
git -C /tmp/hft-business-v2-github diff f97b8d4 f54bfdd -- docs
git -C /tmp/hft-business-v2-github diff f54bfdd review/business-v2-draft -- docs/business/review/github
git -C /tmp/hft-business-v2-github diff f97b8d4 review/business-v2-draft -- AGENTS.md docs/REQUIREMENTS.md docs/REQUIREMENTS.vi.md
```

The last command must produce no content difference. Round 1 covers source fidelity, preserved IDs, approval boundaries and history. Round 2 covers scenarios, tables, Mermaid, gaps and semantic consistency. [Combined PR body](pr-combined.md) contains the human review checklists.

## Actions still requiring separate confirmation

The five [Issue drafts](README.md#five-proposed-domain-review-issues) remain local, without Issue numbers or assignees. They are not posted by this authorization. After reviewing the Draft PR, the owner may separately confirm their exact bodies and publication; then use the real PR URL and supplied team usernames. No Issue creation command is part of the current execution.

The [Phase A](pr-phase-a.md) and [Phase B](pr-phase-b.md) bodies are retained historical planning alternatives. No phase branches exist and reconstructing/publishing that split is not authorized. No merge or Ready for Review command is prepared.
