# Repository Guidelines

## Project Structure & Current Scope

This university project is currently in business-domain analysis. It models simulated electronic trading for learning and research; it is not a real-money exchange or production HFT venue.

- `docs/BUSINESS.md`: draft domain specification, worked examples, invariants, proposed rules, open questions, glossary, and review log.
- `LICENSE`: repository license.
- `AGENTS.md`: contributor guidance.

There are no application modules, test directories, or application assets yet. Until the owner explicitly changes the phase, keep contributions within authorized documentation scope. Do not introduce application scaffolding, architecture, technology selections, infrastructure, or implementation tasks.

## Build, Test, and Development Commands

No build, test, or local-runtime commands are configured. Use repository inspection commands for documentation work:

- `git status --short --untracked-files=all`: identify changed and newly created files.
- `git diff --check`: check unstaged tracked changes for whitespace errors; use `git diff --cached --check` for staged changes. Neither checks untracked files.
- `git diff -- docs/BUSINESS.md`: review tracked specification edits; inspect new files directly.
- `rg -n '^## |BR-|OQ-|INV-' docs/BUSINESS.md`: inspect headings and decision references.

## Documentation Style & Naming

Write documentation in English using UTF-8 Markdown, descriptive headings, pipe tables, and blank lines between blocks. Keep explanations accessible to programming students unfamiliar with trading.

Preserve existing section numbering and identifiers such as `BR-ORD-001`, `OQ-009`, and `BAL-INV-001`. State units explicitly: `0.4 BTC`, `86,000 USDT/BTC`. No programming-language indentation standard, formatter, or linter has been selected.

## Validation & Decision Review

No automated test framework, coverage threshold, or test naming convention exists. For documentation changes, verify arithmetic, asset conservation, terminology, table formatting, links, and identifier references.

Separate established domain concepts, proposed project rules, and open questions. Examples do not approve requirements. Keep unresolved decisions pending; mark rules approved only after explicit owner/team approval. Record actual review outcomes in the review log, never fabricated approvals.

## Commit & Pull Request Guidelines

History currently contains only `Initial commit`, so no established commit convention can be inferred. Use concise imperative summaries, such as `Clarify partial-fill reservation examples`.

Keep changes focused. Pull requests should describe affected sections, rationale, validation performed, and unresolved decisions. Link relevant issues or decision IDs when available. Preserve unrelated work and do not commit or push without explicit authorization.
