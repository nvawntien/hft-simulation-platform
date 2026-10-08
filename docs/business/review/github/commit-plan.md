# Proposed commit and publication plan

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Authorization boundary

No Git mutation has been made. The user permits preparation but explicitly reserves push/PR/merge confirmation. The repository AGENTS.md also says: “do not commit or push without explicit authorization.” Therefore the following are concrete **proposed actions**, not executed commands. Stage only the named files; preserve the original working tree.

Tracking unchanged Requirements/AGENTS is a separate scope confirmation: main currently has only LICENSE, so those links cannot be usable in GitHub if their files remain untracked. Neither Requirements file is edited. If importing them is not authorized, stop publication and agree a baseline/reference alternative rather than publishing silently broken source links.

## Recommended combined-review branch

Head name: `review/business-v2-draft`. Local worktree proposed at `/tmp/hft-business-v2-github`. Starting point at inspection: `4d64533a25592a62e597e701dfdfd84daf5724ad`. Cached origin/main is not live remote verification.

### Commit 1 — Record existing documentation baseline

Exactly five files: original `docs/BUSINESS.md` and `docs/BUSINESS.vi.md` copied from verified history, existing `AGENTS.md`, existing `docs/REQUIREMENTS.md`, existing `docs/REQUIREMENTS.vi.md`. Preserve all bytes. This is baseline provenance, not a new requirements/policy decision.

### Commit 2 — Prepare modular Business V2 review draft

The two existing Business adapters; all 31 original V2 files under `docs/business/`; these eleven review-preparation Markdown files. This is 44 changed/added paths in commit 2. The final PR against LICENSE-only main contains 47 added paths. Requirements and AGENTS have zero content differences between the two commits.

### Local commands requiring commit authorization

First refresh the remote and inspect it. If it differs from the recorded baseline, stop and reassess before running the fixed-base worktree/import sequence.

```sh
git fetch origin
git log --oneline --decorate -5 origin/main
git diff --stat 4d64533a25592a62e597e701dfdfd84daf5724ad origin/main
```

After approving the baseline import and two local commits, use an isolated worktree. Existing-directory/branch errors require investigation, not deletion/reset.

```sh
git worktree add -b review/business-v2-draft /tmp/hft-business-v2-github 4d64533a25592a62e597e701dfdfd84daf5724ad
mkdir -p /tmp/hft-business-v2-github/docs
cp /home/vantien/Projects/cloud/hft-simulation-platform/AGENTS.md /tmp/hft-business-v2-github/AGENTS.md
cp /home/vantien/Projects/cloud/hft-simulation-platform/docs/business/history/BUSINESS.md /tmp/hft-business-v2-github/docs/BUSINESS.md
cp /home/vantien/Projects/cloud/hft-simulation-platform/docs/business/history/BUSINESS.vi.md /tmp/hft-business-v2-github/docs/BUSINESS.vi.md
cp /home/vantien/Projects/cloud/hft-simulation-platform/docs/REQUIREMENTS.md /tmp/hft-business-v2-github/docs/REQUIREMENTS.md
cp /home/vantien/Projects/cloud/hft-simulation-platform/docs/REQUIREMENTS.vi.md /tmp/hft-business-v2-github/docs/REQUIREMENTS.vi.md
git -C /tmp/hft-business-v2-github add -- AGENTS.md docs/BUSINESS.md docs/BUSINESS.vi.md docs/REQUIREMENTS.md docs/REQUIREMENTS.vi.md
git -C /tmp/hft-business-v2-github diff --cached --check
git -C /tmp/hft-business-v2-github diff --cached --stat
```

Verify the five staged file hashes against the preserved originals before creating commit 1; require the staged paths to match exactly. Then:

```sh
git -C /tmp/hft-business-v2-github commit -m "Record existing documentation baseline"
cp /home/vantien/Projects/cloud/hft-simulation-platform/docs/BUSINESS.md /tmp/hft-business-v2-github/docs/BUSINESS.md
cp /home/vantien/Projects/cloud/hft-simulation-platform/docs/BUSINESS.vi.md /tmp/hft-business-v2-github/docs/BUSINESS.vi.md
cp -R /home/vantien/Projects/cloud/hft-simulation-platform/docs/business /tmp/hft-business-v2-github/docs/business
git -C /tmp/hft-business-v2-github add -- docs/BUSINESS.md docs/BUSINESS.vi.md docs/business
git -C /tmp/hft-business-v2-github diff --cached --check
git -C /tmp/hft-business-v2-github diff --cached --stat
```

Before commit 2: rerun source/ID/status/link checks in the worktree; compare all copied V2/review files against the untouched source tree; verify Requirements and AGENTS have no staged differences. Do not use `git add .` or include unrelated work. Then:

```sh
git -C /tmp/hft-business-v2-github commit -m "Prepare modular Business V2 review draft"
git -C /tmp/hft-business-v2-github log --oneline -3
git -C /tmp/hft-business-v2-github status --short
```

## Separate confirmation for publishing the branch and PR

Only after reviewing the two commit SHAs/diffs and approving publication:

```sh
git -C /tmp/hft-business-v2-github push -u origin review/business-v2-draft
gh pr create --repo nvawntien/hft-simulation-platform --base main --head review/business-v2-draft --draft --title "Prepare modular Business V2 draft for domain review" --body-file docs/business/review/github/pr-combined.md
```

The PR body file includes its title/preparation state; review that exact text before publication. Confirm source links in the published head and then add the real PR URL to Issue bodies. The prepared body must be reconciled with the actual commit/publication state before posting; do not leave “not published” claims after publication. No reviewer handle is invented and none is assigned automatically.

## Separate confirmation for publishing the five Issues

Create only after confirming all five exact bodies and the real PR reference. Commands are run from the original repository root; no `--assignee` is supplied because usernames are unavailable.

```sh
gh issue create --repo nvawntien/hft-simulation-platform --title "Review experiment lifecycle, capital identity and administration" --body-file docs/business/review/github/issues/01-experiment-identity-administration.md
gh issue create --repo nvawntien/hft-simulation-platform --title "Review Order lifecycle, matching priority and STP outcomes" --body-file docs/business/review/github/issues/02-orders-matching-stp.md
gh issue create --repo nvawntien/hft-simulation-platform --title "Review reservation, settlement, fees and asset conservation" --body-file docs/business/review/github/issues/03-financial-correctness.md
gh issue create --repo nvawntien/hft-simulation-platform --title "Review Market Data, candles and Portfolio accounting" --body-file docs/business/review/github/issues/04-market-data-portfolio.md
gh issue create --repo nvawntien/hft-simulation-platform --title "Review Algo/HFT latency, queue and execution metrics" --body-file docs/business/review/github/issues/05-algo-hft-time-observations.md
```

No merge command is prepared or authorized. Publication of Issues is an external write and needs explicit confirmation too, even though the original request named push/PR/merge specifically.

## Optional reconstructed A/B route

If two actual A/B PRs are mandatory, separately authorize a byte-preserved baseline import and reconstruction in isolated worktrees. Proposed A head is `review/business-v2-phase-a` from that baseline; B head is `review/business-v2-phase-b` based on A. Rebuild only phase-specific navigation/metadata, verify A links without B, and prove A source fidelity plus B's exact V2 endpoint. The conditional PR bodies specify their scopes. No command sequence to publish an unverified reconstructed split is approved by this plan.
