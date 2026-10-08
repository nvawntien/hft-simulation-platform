# Matching and self-trade prevention

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md) · [Decisions](decisions.md) · [Invariants](invariants.md)

## Phase A — migrated source

Canonical narrative from the English pre-V2 Business source. Original section numbers are retained for existing Requirements references. Approval follows each paragraph and the decision register; a domain title does not approve its content.

<a id="source-section-10"></a>

## 10. Matching Rules

**ESTABLISHED DOMAIN CONCEPT.** Matching selects compatible same-symbol, opposite-side interest. Price-Time is one common method; other markets use other allocations, as illustrated by [CME's matching explanation](https://www.cmegroup.com/education/articles-and-reports/overview-what-makes-ags-markets-work).

**PROJECT V1 RULE — APPROVED — [OQ-009](decisions.md#oq-009), [OQ-010](decisions.md#oq-010):**

1. Better price first: lowest Ask for a buy, highest Bid for a sell.
2. At equal prices, earlier **platform acceptance sequence** first.
3. Larger quantity never overtakes an earlier same-price order.
4. Each Trade executes at the matched resting order's price.

The platform establishes one authoritative deterministic acceptance sequence for all accepted orders. A, B, C might receive `#1001`, `#1002`, `#1003` despite identical displayed times. Client-provided timestamps never determine priority. A resting remainder keeps its order's original acceptance sequence. How this sequence is generated is outside this document.

Example A: earlier Ask A offers `1 BTC @ 86,000`; later Ask B offers `1 BTC @ 85,000`. Incoming BUY LIMIT `1 BTC @ 86,000` trades with B at `85,000` first.

Example B: Ask A `0.4 BTC @ 86,000`, sequence `#1001`; Ask B `2 BTC @ 86,000`, sequence `#1002`. Incoming BUY LIMIT `1 BTC @ 86,000` fills A's `0.4`, then B's `0.6`, leaving B with `1.4 BTC`. Bid priority is the reverse price direction: `85,500` precedes `85,000` for an incoming sell.

### Self-Trade Prevention

**APPROVED — [OQ-011](decisions.md#oq-011):** check trading-account identity when the next eligible resting order would match. If it belongs to the incoming order's account, **cancel that resting order's active remainder**, record SELF_TRADE_PREVENTION distinctly from user cancellation, create no Trade, and continue the incoming order against other eligible counterparties. Do not merely skip the self-owned order and leave it active.

Example: account A's incoming BUY encounters A's resting SELL `0.4 BTC @ 86,000`, then B's SELL `0.6 BTC @ 86,000`. A's resting remainder is cancelled; only the `0.6 BTC` exchange with B becomes a Trade. The prevented match creates **no Trade, volume, Last Price update, or fee**. Previous fills of the cancelled order remain valid. Cross-account ownership grouping is deferred beyond V1.

**PROPOSED — OWNER REVIEW REQUIRED — [OQ-031](decisions.md#oq-031):** for each eligible order pair, execute the smaller active remaining Base quantity as one Trade, then continue against the next eligible resting order if needed. This makes Trade boundaries, and therefore per-Trade fees, deterministic. The full immediate matching outcome of an incoming order, including prevention and fills, precedes the next effective order/cancel business action. This defines observable precedence, not a technical execution mechanism.

<a id="source-section-17"></a>

## 17. Execution Price

**PROJECT V1 RULE — APPROVED — [OQ-009](decisions.md#oq-009).** **Execution Price = price of the matched resting order.** The incoming Limit Price or Market protection price only determines eligibility.

| Resting interest | Incoming interest | Execution price |
|------------------|-------------------|-----------------|
| 10:00 BUY LIMIT `1 BTC @ 90,000` | 10:01 SELL LIMIT `1 BTC @ 86,000` | `90,000` — the resting buy's price. |
| 10:00 SELL LIMIT `1 BTC @ 86,000` | 10:01 BUY LIMIT `1 BTC @ 90,000` | `86,000` — the resting sell's price. |

The times illustrate arrival order; authoritative acceptance sequence determines actual priority. Compatible limits overlap between `86,000` and `90,000`, but V1 does not choose a midpoint or the incoming limit. The incoming participant may obtain price improvement. Multiple resting price levels produce separate executions at their respective prices.

A resting BUY at `90,000` can execute against a Market Sell with minimum `80,000`, at `90,000`. A resting SELL at `91,000` cannot execute against a Market Buy with maximum `90,000`. Fees are separate from the unit-price boundary.

## Canonical BR entries — migrated from source section 31

| ID | Rule | Rationale | Status | Decisions |
|----|------|-----------|--------|-----------|
| <a id="br-ord-001"></a>BR-ORD-001 | Better price, then earlier platform acceptance sequence; never client timestamps or quantity-based overtaking. | Deterministic priority. | APPROVED | [OQ-010](decisions.md#oq-010) |
| <a id="br-ord-005"></a>BR-ORD-005 | Execute at the matched resting order's price. | Explain price improvement consistently. | APPROVED | [OQ-009](decisions.md#oq-009) |
| <a id="br-ord-006"></a>BR-ORD-006 | Cancel the self-owned resting remainder, create no self-Trade, and continue the incoming order. | Account-level STP with distinct history. | APPROVED | [OQ-011](decisions.md#oq-011) |

## Phase B — behavioral analysis

### Purpose, objects and preconditions

The platform selects same-symbol opposite interest within both unit-price boundaries. The book consists of active resting Limits; Market never becomes resting interest. Selection compares better price before acceptance sequence. STP compares trading-account identity at the **next eligible** pair, so an out-of-protection self-owned Order is not cancelled just because it exists.

| Object / fact | Relevant attributes | Changes / immutable meaning |
|---------------|---------------------|-----------------------------|
| Order Book | Symbol, active Bids/Asks, price levels, individual ownership/sequence underneath aggregation | Limit rests, fills reduce interest, cancellation/STP/end remove remainder; no independent formal book states |
| Resting Order | Active quantity, limit, account, acceptance sequence | Original sequence survives partial fills ([OQ-010](decisions.md#oq-010)); original commitments stable under proposed [OQ-028](decisions.md#oq-028) |
| Incoming matching outcome | Selected counterparties, Trades, prevented pairs, type remainder | Proposed [OQ-031](decisions.md#oq-031) completes all immediate results before next effective action; not an Order state |

### Decision table — eligibility, identity and continuation

| Initial business state | Trigger / condition | Outcome | State / financial effects | Basis | Status |
|------------------------|---------------------|---------|---------------------------|-------|--------|
| Valid accepted funded incoming Order | Candidate different symbol/same side/outside boundary | Not eligible counterparty | No Trade or STP for that pair | [OQ-008](decisions.md#oq-008)/[OQ-009](decisions.md#oq-009)/[OQ-010](decisions.md#oq-010); source §10 | APPROVED eligibility |
| Eligible prices differ | Selection for BUY / SELL | Lowest Ask / highest Bid first | Better price dominates earlier worse price | [BR-ORD-001](05-matching-stp.md#br-ord-001) | APPROVED |
| Eligible price equal | Accepted sequences differ | Earlier authoritative sequence first | Quantity/client/display time cannot overtake | [OQ-010](decisions.md#oq-010); [ORD-INV-005](invariants.md#ord-inv-005) | APPROVED |
| Next eligible pair same account | Matching reaches it | Cancel resting active remainder, incoming continues | No Trade/fee/volume/Last Price for prevention; prior fills stand | [OQ-011](decisions.md#oq-011); [TRD-INV-004](invariants.md#trd-inv-004) | APPROVED STP core |
| Next eligible pair different accounts | Quantity execution | One Trade using smaller active quantity, resting price | Settle only actual Trade; continue if needed | [OQ-009](decisions.md#oq-009)/[OQ-031](decisions.md#oq-031)/[OQ-024](decisions.md#oq-024) | PROVISIONAL complete pair outcome |
| No next eligible interest | Immediate matching ends | Limit remainder rests; Market remainder ends | Named states/reserve releases are proposed | [OQ-007](decisions.md#oq-007)/[OQ-012](decisions.md#oq-012)/[OQ-023](decisions.md#oq-023)/[OQ-027](decisions.md#oq-027) | PROVISIONAL complete outcome |
| Same scheduled arrivals, no accepted sequence yet | Establish effective order | UNDECIDED — [GAP-MAT-001](review/gap-register.md#gap-mat-001) | Queue/Trade/fee consequences blocked | [OQ-010](decisions.md#oq-010)/[OQ-031](decisions.md#oq-031)/[OQ-041](decisions.md#oq-041) | OPEN |

### Time, no-action and financial boundaries

Known same-price accepted sequence resolves equal **displayed** times. It does not select an order for equal scheduled arrivals before acceptance. For already known effective order, earlier complete incoming outcome precedes next cancel/end under proposed [OQ-031](decisions.md#oq-031). No technical concurrency mechanism is selected.

Multiple own resting remainders are each cancelled when reached as eligible; external fills before or after STP remain legitimate. If no external liquidity remains, follow type persistence. A failed funding gate occurs before STP in the proposed full acceptance flow, so an expected self-cancellation cannot fund the incoming Order. Released STP reservation is unused owned capital, not trading income ([OQ-023](decisions.md#oq-023); [BAL-INV-002](invariants.md#bal-inv-002)).

No action creates no match and moves no queue position by time alone. Other fills/cancellations may alter count/quantity ahead without changing the surviving Order's sequence. End removes active interest under proposed [OQ-035](decisions.md#oq-035); it cannot reverse previously completed matches.

### Scenarios and dependencies

[BS-MAT-001](scenarios/matching-stp.md#bs-mat-001)–[BS-MAT-004](scenarios/matching-stp.md#bs-mat-004) cover price/sequence/multiple levels/tie gaps; [BS-STP-001](scenarios/matching-stp.md#bs-stp-001)–[BS-STP-005](scenarios/matching-stp.md#bs-stp-005) cover own interest first/later/multiple/prior fills/no external liquidity and distinct-account ownership. [DIA-MATCH](diagrams/README.md#dia-match) and [DIA-STP](diagrams/README.md#dia-stp) show branches; [DIA-PRECEDENCE](diagrams/README.md#dia-precedence) explains effective order. A scenario showing pair sizes, named states or exact settlement is PROVISIONAL because [OQ-031](decisions.md#oq-031)/[OQ-027](decisions.md#oq-027)/[OQ-024](decisions.md#oq-024) are not approved, even though its selection/STP substeps are approved. Read Orders, Reservation, Settlement, Data and Experiment for effects.
