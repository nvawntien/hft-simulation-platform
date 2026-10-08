# Validation evidence for GitHub review preparation

**DRAFT — REQUIRES TEAM / OWNER REVIEW. NOT PUBLISHED.**

## Fresh inspection results

| Check | Current result |
|-------|----------------|
| Git base | `4d64533a25592a62e597e701dfdfd84daf5724ad`; only LICENSE is tracked |
| Existing uncommitted work | 36 untracked files; no tracked/staged changes at inspection |
| Source fidelity | All 35 numbered English source sections and exact 26 BR rows reconstructed after removing only generated ID links/anchors; moved §1 Vietnamese link target normalized for comparison |
| Historical recovery | English/Vietnamese frozen source hashes match the recorded originals |
| Requirements preservation | Both current hashes match their recorded pre-V2 values; files remain unedited |
| Canonical entries | 46 OQ, 26 BR, 19 invariants; one explicit canonical anchor per entry |
| Approval boundaries | 14 OQ APPROVED, 32 PROPOSED — OWNER REVIEW REQUIRED; canonical rows unchanged; historical EN/VI OQ status and BR status/dependencies agree |
| Scenarios | 68: four narrow APPROVED-DERIVED, 55 PROVISIONAL, nine OPEN / BLOCKED; no approved-derived trace uses proposed OQ/BR |
| Gaps | 18 OPEN: four BLOCKER, eleven HIGH, three MEDIUM |
| Original adapter headings | All 35 exact numbered English and Vietnamese headings retained |
| Internal links before new bundle | 5,537 relative file/anchor references checked across Business, Requirements and AGENTS; only the two known legacy PDF failures below |
| Tables / whitespace | 196 Business tables checked for columns; new/untracked document whitespace/fences checked directly rather than relying on git diff |
| Mermaid structure | 17 closed Mermaid blocks: ten flowcharts, six sequence diagrams, one state diagram; parser/rendering not freshly rerun |
| Naive marker-only Phase A preview | Seven dangling README links/anchors; preview exists only in temporary analysis, not the working tree or a branch |

Fresh source fidelity preserves all original formulas, numeric constraints, examples, meanings and approval bases as text. Semantic correctness and complete behavioral coverage are not proven by these automated checks. The original migration report records prior 88 decimal assertions and Mermaid parsing; its temporary scripts/checkpoint are no longer present, so those executions are not claimed as rerun in this preparation.

## Known link discrepancies

Both unchanged Requirements editions point to absent `docs/HFT-Simulation-Platform-Business-Specification-V1-Vietnamese.pdf`. No PDF is available here and prior inspection cannot be verified. This is a pre-existing artifact discrepancy; it is reported, not silently repaired or converted into business policy.

The marker-only Phase A preview fails on: scenario index (referenced twice), diagram catalogue, gap register, coverage matrix, migration-report language-review anchor, and migration-report phase-review-boundary anchor. The README also discusses current Phase B status/reading resources. A link-clean A therefore requires reconstruction and its own review.

## Preserved source hash identities

| Artifact | SHA-256 |
|----------|---------|
| Original English Business | `ada66d2983f2ecfffea5c1a98042147bef534a8e3869c213a6e8de5316494c70` |
| Original Vietnamese Business | `0ff13f2c3f225d579087c7c2fc25aea1d0b55f3d85ecb5da938488a222985f77` |
| Unchanged English Requirements | `399efdcee1684c9042070a00faa964ace485d8b2f88b83efa2d1f8b51334ce51` |
| Unchanged Vietnamese Requirements | `0178b38403d8781607c78b3af3622912b3b95426aa8a8b090c5488ff97918ae7` |

## Preservation inventory

All paths below existed before this task. The final preparation check compares every current file against this manifest. Eleven additional review-draft files are the only intended working-tree additions. Current original files, branch, HEAD and index are retained; no stash/reset/checkout or commit is used to force separation. The temporary analysis snapshot is an additional convenience, not the sole preserved source and not promised as durable storage.

| Pre-existing uncommitted path | SHA-256 before preparation |
|-------------------------------|---------------------------|
| `AGENTS.md` | `d43164be31f6f2a5f28fcae055545bed706825d5b0765149a893cb4aca296c6e` |
| `docs/BUSINESS.md` | `17d80f9fc56537d9d4cd27f95c29049296f29c74144e1cc6af305717ed3aff0f` |
| `docs/BUSINESS.vi.md` | `f6f683cc8b17fd9d5a4ee57e196bda384715f7fe865ce81b9080927c84ab865b` |
| `docs/REQUIREMENTS.md` | `399efdcee1684c9042070a00faa964ace485d8b2f88b83efa2d1f8b51334ce51` |
| `docs/REQUIREMENTS.vi.md` | `0178b38403d8781607c78b3af3622912b3b95426aa8a8b090c5488ff97918ae7` |
| `docs/business/01-scope-actors.md` | `37390739c768734cd01800f55a46d3681ce7e101c526fac40de96bc56e6bd21a` |
| `docs/business/02-experiment.md` | `b745f6eb67223e01c0afb122d316c16146d4b882efecf2bc37cf4ae5499f770f` |
| `docs/business/03-markets-assets.md` | `766cfa0809aa663c3621ad98f7ba1a4981993fe11feedf01ae997b8ecf178459` |
| `docs/business/04-orders.md` | `e22c443a755175762fb91bc93d68261995ef81b75edc7e4e8f0a9ed27a0b9337` |
| `docs/business/05-matching-stp.md` | `73e42361efcbce8ca30114320ca6b90a3c127cb972dc3dc93143a18fffb6a919` |
| `docs/business/06-balance-reservation.md` | `d6ec9c48197ff497d1bd13394e0b91828b4def71f5b74c3636f426708c80ccc4` |
| `docs/business/07-trade-settlement-fees.md` | `e534084c1a8663ab67d2dca076d12645e041e4d943ba11565160da574eedb52e` |
| `docs/business/08-market-data.md` | `261d3a06491f1b880ad0365b58b99e3367ed6622df09bb288d3555140d4c2bd2` |
| `docs/business/09-portfolio-pnl.md` | `3169e56329d56f15b9846e37829de97d7d7212344ebc661cb634a823149f2ece` |
| `docs/business/10-algo-hft.md` | `f30b40d11cf56e0f489b7cb13621507d3268556b04b3a2c21280e9d1c4743798` |
| `docs/business/11-administration.md` | `652619161c52e8a204e3a349e26156af2208ded53e03b63031cb0661839c6c66` |
| `docs/business/README.md` | `aa7d36ea682c0d4018325e0eb0a5142b9d07dc401fe6d327f67435e55764b51d` |
| `docs/business/decisions.md` | `a1c4e34ecf83e1262734dfe0f1f1ec0ba02f7dbe486eca7dfddb5ee913d07140` |
| `docs/business/diagrams/README.md` | `1562d88727674de8fce4aab6a5e7f9c3609a6f74ed0e7da49c4c1f37a96e46ff` |
| `docs/business/glossary.md` | `e1b0c7afc0c41d00a265f20adb5ef9c55af58bbf4158545d31f4b5ac3a4118a5` |
| `docs/business/history/BUSINESS.md` | `ada66d2983f2ecfffea5c1a98042147bef534a8e3869c213a6e8de5316494c70` |
| `docs/business/history/BUSINESS.vi.md` | `0ff13f2c3f225d579087c7c2fc25aea1d0b55f3d85ecb5da938488a222985f77` |
| `docs/business/history/README.md` | `732307baa239601943699ccee47ef13c3f7992d4b4ce5dd12e35ab2eb7cc3ecb` |
| `docs/business/invariants.md` | `b4afeb60b7c64ff111c5d6fedc9e44e53c34dc9bd3d81fd0aedd0eb3ab2e68f8` |
| `docs/business/review/coverage-matrix.md` | `0c7e55405e86ec58f9fe08e256fc6a88be6854a47a7efc168de4e27d8aeac0e7` |
| `docs/business/review/gap-register.md` | `1ced6e10e9d768bc2358512f6b4e64fab77527cbecefa3cbb01ef73737202e10` |
| `docs/business/review/migration-report.md` | `bf28bb640942c1b72180b0da92e450c9666b5748f39ca3cf0f7b5cd0b3d5c543` |
| `docs/business/review/review-guide.md` | `8d7319edb7bd3e86e810d62b5e78fc82b03815b1b98f38819f3a1f29f26fbc7b` |
| `docs/business/scenarios/README.md` | `2b09d561fbd51c07bc9caaf853baece8cd076edfd2b74a33ae6d086f654953e3` |
| `docs/business/scenarios/algo-hft.md` | `76ecc65199eee806e9d20c811932154163340b7b8bb0801fed2ddd5c46b0ca8f` |
| `docs/business/scenarios/balance-settlement.md` | `ce0262d2595f515efed4266ecfaaa7d3f12c1cc0cb305a556d4bbb75bcacd8b3` |
| `docs/business/scenarios/experiment.md` | `5d1b6f7c0cedfa5c40d33e7423a26459e550c1d5e0ab06841e72f170a41fda0b` |
| `docs/business/scenarios/market-data.md` | `a76c0c9e7083b1032828e94238373f14787a2375faaff9590c8f4ceaee7f0675` |
| `docs/business/scenarios/matching-stp.md` | `e75955ca68c9bd27b66584cdae0c6cd7a1c4d5a7f4debadefeb7db12fb6d82e6` |
| `docs/business/scenarios/orders.md` | `14389081e4bc8e8c3b09a7097ce4fb5cd5cb873dcc4a55f1c48067383497d38d` |
| `docs/business/scenarios/portfolio.md` | `fd17eed2dd7c32af16812107f37086b2c971b0f1038ed798357d0272181889df` |

## How evidence was checked

Repository inspection used `git status --short --untracked-files=all`, `git ls-tree -r --name-only HEAD`, branch/log reads, all document text and SHA-256 comparison. Source checks use the README's 35-section map and canonical BR anchors, stripping generated link wrappers/anchors before exact text comparison. Status checks parse decision/rule table cells, rather than lifecycle terms such as REJECTED. Link checks resolve relative files, explicit ID anchors and generated Markdown heading anchors. Scenario/gap checks use canonical anchors and exact trace/status fields.

The disposable preview only removes Phase B-only files and cuts at explicit Phase B markers; its seven failures demonstrate why simply staging marker-based extraction is insufficient. It does not choose new policy or mutate live documents. Current cached origin/main is not proof of the live GitHub tip; publication needs a fetch/reassessment.

Final bundle link/table/whitespace/preservation checks and Git status are recorded after writing the draft bodies. Human review, actual reviewer assignment, local commits and all external publication remain outstanding.

## Final preparation verification

- All 36 pre-existing files match the preservation inventory; only eleven new Markdown review artifacts were added.
- All current relative file/anchor references pass except the two explicitly recorded legacy PDF references. After adding the bundle, 5,552 internal references and 202 Business/review tables were checked.
- Canonical counts and scenario approval inheritance remain unchanged; every Issue draft has explicit reviewers, scope, source trace, deliverables, acceptance and closure evidence.
- HEAD remains `4d64533a25592a62e597e701dfdfd84daf5724ad` on main; the index is empty and no other branch was created. `git diff --check` passes, supplemented by direct untracked Markdown checks.
- Existing linked BS/GAP headings no longer match the README's plain-ID heading search patterns. Corrected review queries are supplied in this bundle's README; the original source remains untouched. This is a navigation follow-up, not a new business gap.
