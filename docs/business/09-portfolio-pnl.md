# Portfolio and PnL

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-25"></a>

## 25. Portfolio

**ESTABLISHED DOMAIN CONCEPT.** A Portfolio describes holdings; a Position is the quantity held; cost basis assigns acquisition cost; Realized PnL concerns sold quantity; Unrealized PnL values quantity still held. Locked assets remain part of holdings.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-033](decisions.md#oq-033), [OQ-034](decisions.md#oq-034), [OQ-046](decisions.md#oq-046):** V1 reports available/locked/total balances, spot positions, weighted-average cost, realized and unrealized PnL per Base asset in USDT, fees, and total USDT equity when all nonzero holdings can be valued. Use **weighted-average cost only**; tax-lot/FIFO accounting is beyond V1.

On BUY, add notional plus buyer fee to the position's total cost basis. On SELL, remove the proportional basis `basis before sale × sold quantity / quantity before sale`, rounded down to 8 USDT decimals; a full sale removes all remaining basis. Realized PnL is net sale proceeds minus removed basis. This retains rounding residuals in the remaining position instead of losing them. Average cost is basis divided by quantity; it is unavailable for zero holdings.

In a zero-fee scenario, buy `1 BTC @ 80,000` and `1 @ 90,000`: total basis `170,000`, average `85,000`. Sell `0.5 @ 88,000`: realized PnL `1,500`; remaining basis `127,500`. At Last Price `87,000`, unrealized PnL is `130,500 − 127,500 = 3,000 USDT`.

If all three executions instead incur a taker fee of `0.2%`, basis becomes `170,340`, average `85,170`; selling `0.5` nets `43,912`, removes basis `42,585`, and realizes `1,327`. Remaining basis `127,755` yields unrealized PnL `2,745` at the same Last Price. Fee rates are scenario choices, not defaults.

Seeded Base assets receive an explicitly declared scenario acquisition-reference price, valid on the symbol's price grid; their initial basis is quantity × that price. Seeding changes funded capital, not trading PnL or Last Price. USDT allocation likewise is not profit. Portfolio performance separates funded capital, realized PnL, unrealized PnL, and fees; fees already included in cost/proceeds must not be subtracted twice.

Value holdings at the experiment's Last Price, without substituting a setup reference. Before a first Trade, valuation/unrealized PnL of a nonzero Base holding and total equity are unavailable; realized PnL and known balances remain available. A Last Price older than `60` experiment seconds is flagged stale but retained, with its age. These are indicative valuations, not guaranteed liquidation proceeds; no future selling fee is deducted from unrealized PnL.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-pf-001"></a>BR-PF-001 | Report balances, positions, fee-inclusive weighted-average cost, and realized/unrealized PnL. | Useful performance analysis with one cost method. | PROPOSED — OWNER REVIEW REQUIRED | [OQ-033](decisions.md#oq-033), [OQ-034](decisions.md#oq-034) |

## Phase B — behavioral analysis

### Purpose, objects and ownership

Portfolio explains account holdings and performance, using actual settled quantities plus separately labelled seeded capital. It does not promise liquidation value or financial profit from a synthetic strategy. Reporting does not create assets.

| Object / attribute | Meaning and creation | Changes / end / unchanged facts |
|--------------------|----------------------|---------------------------------|
| Portfolio Position | Account/Base asset, owned quantity including Locked, total assigned USDT basis, realized PnL, fees | Seeded quantity/reference or BUY creates holdings/basis; SELL removes quantity/basis; no formal Position state enum |
| Average cost | Basis per currently held quantity | Unavailable for zero quantity; rounded display doesn't remove residual basis |
| Marked value / unrealized PnL | Last Price-based indicative value and difference from retained basis | Price may change without account trading; missing price makes those measures unavailable for nonzero Base |
| USDT equity | Quote holdings plus valued nonzero Base holdings | Available only when all nonzero Base can be valued; funding separated from trading performance |

Canonical buy cost, proportional floor/full-liquidation exception and staleness formulas are §25/[OQ-033](decisions.md#oq-033)/[OQ-034](decisions.md#oq-034)/[OQ-046](decisions.md#oq-046). Positions need no invented OPEN/CLOSED lifecycle labels. Full liquidation removes **all** remaining basis, including retained rounding residual. Historical realized results remain explained by actual Trades; experiment end retains balances/history under [OQ-035](decisions.md#oq-035)/[OQ-042](decisions.md#oq-042).

### Preconditions and normal/alternative flows

BUY adds actual Base and fee-inclusive cost, SELL gives owned Base and realizes net proceeds against removed weighted basis. Seeded acquisition reference must be declared and on symbol grid; it establishes cost, never Last Price or an executable quote. Reserve/release alone changes Available/Locked without reducing held quantity or recognizing trading PnL ([BAL-INV-002](invariants.md#bal-inv-002)/[SIM-INV-001](invariants.md#sim-inv-001)). Fees already in cost/proceeds cannot be deducted again.

### Decision table — position quantity × valuation reference

| Initial position / price | Trigger / condition | Outcome | Financial / state effects | Basis | Status |
|--------------------------|---------------------|---------|---------------------------|-------|--------|
| Seeded nonzero Base; no Trade | Prepare/report | Declared basis, known balances; valuation/unrealized/equity unavailable | Funding not trading profit; reference not valuation substitute | [OQ-003](decisions.md#oq-003)/[OQ-033](decisions.md#oq-033)/[OQ-034](decisions.md#oq-034)/[OQ-042](decisions.md#oq-042)/[OQ-044](decisions.md#oq-044) | PROVISIONAL |
| Nonzero position | BUY Trade | Add full received quantity and actual fee-inclusive cost | Weighted basis changes; fees not double subtracted | [OQ-015](decisions.md#oq-015)/[OQ-024](decisions.md#oq-024)/[OQ-033](decisions.md#oq-033)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Nonzero position | Partial SELL | Remove proportional floored basis; net proceeds less removed cost is realized | Retain basis residual with remaining quantity | [OQ-015](decisions.md#oq-015)/[OQ-033](decisions.md#oq-033)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Nonzero position | SELL entire quantity | Remove all remaining basis | Zero holding; average cost unavailable; no leftover cost | [OQ-033](decisions.md#oq-033)/[OQ-046](decisions.md#oq-046) | PROVISIONAL |
| Nonzero holding, Last Price known | Virtual age = 60 seconds / greater than 60 | At 60 not stale; greater than 60 stale and retained with age | Valuation uses retained indicative price, no future sale fee | [OQ-034](decisions.md#oq-034) | PROVISIONAL derived boundary |
| Reserve/release with no Trade | Report holdings | Quantity/total ownership unchanged | No new realized PnL from unlocking | [BAL-INV-002](invariants.md#bal-inv-002); [OQ-033](decisions.md#oq-033) | PROVISIONAL complete reporting |
| Experiment ended | Inspect preserved results | Historical facts retained; detailed access/age progression undecided | No new capital transfer to replacement run | [OQ-035](decisions.md#oq-035)/[OQ-042](decisions.md#oq-042); [GAP-EXP-003](review/gap-register.md#gap-exp-003)/[GAP-EXP-005](review/gap-register.md#gap-exp-005) | PROVISIONAL facts; OPEN access/time |

### No action, examples and dependencies

An account doing nothing can have a changing indicative valuation if other accounts establish a different Last Price; its units, basis and realized PnL do not thereby change. With no market Trades, only virtual age changes if the clock progresses. Exactly 60 seconds is not “older than 60”; the smallest representable increment beyond it awaits [GAP-HFT-001](review/gap-register.md#gap-hft-001).

[BS-PF-001](scenarios/portfolio.md#bs-pf-001)–[BS-PF-005](scenarios/portfolio.md#bs-pf-005) cover original zero/fee-enabled calculations, seeded missing prices, rounding residual/full liquidation and no-action price age. [DIA-PF](diagrams/README.md#dia-pf) shows valuation branching, not a position enum. Read Reservation, Settlement/Fees, Market Data and Experiment. Tax-lot/FIFO and hypothetical future sale fee deductions are outside the proposed model.
