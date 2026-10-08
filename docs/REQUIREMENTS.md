# HFT Simulation Platform — System Requirements Specification (V1)

**Status:** DRAFT — REQUIRES TEAM / OWNER REVIEW

**Business baseline:**

- OQ-001 → OQ-014: APPROVED.
- OQ-015 → OQ-046: PROPOSED — OWNER REVIEW REQUIRED.
- Requirements derived wholly or partially from proposed decisions remain **PROVISIONAL**.

**Source review date:** 2026-10-07.

## 1. Purpose

Translate the current [Business Specification](BUSINESS.md) into testable, technology-neutral V1 system requirements for the university simulation project. This document specifies observable business behavior and its verification, not a real-money exchange or production HFT venue. It does not approve business proposals or authorize Architecture / System Design or implementation work.

## 2. Scope

V1's approved core is fully funded simulated spot trading in BTC/USDT, ETH/USDT, and SOL/USDT, using Limit and protected Market Orders, Price-Time Priority, account-level Self-Trade Prevention, and configurable Maker/Taker fees. Proposed refinements cover precise funding/reservation, settlement, lifecycle, experiments, market observations, portfolio/PnL, strategies, and timing studies. Each refinement retains its own approval boundary.

Business actors are Human Trader, Algorithmic Strategy, Exchange / Trading Platform, Administrator, and Liquidity / Scenario Account. Their responsibilities are not a technical access-role design. A participant's Order expresses intent; only actual eligible matching creates a Trade. Funding, reservation, cancellation, and an unfilled request are not execution.

## 3. Business Baseline and Approval Status

The authority is [BUSINESS.md](BUSINESS.md), particularly section 31 (26 BR rules), section 32 (46 OQ decisions), and section 30 (19 invariants). Narrative sections provide definitions, worked examples, and details used below. The [Vietnamese edition](BUSINESS.vi.md) and [Vietnamese PDF](HFT-Simulation-Platform-Business-Specification-V1-Vietnamese.pdf) were inspected as supporting sources; no substantive approval or business-policy conflict was found. The English Markdown remains the primary source. A translation or PDF is not a separate approval.

The source contains concrete recommendations for every proposed OQ, not unanswered multiple-choice alternatives. Preserve those recommendations as provisional requirements. Scenario funds, rates, dates, and delays are explicit configurable inputs, not missing universal defaults. The limited unspecified details in section 14 remain TBD without replacing selected business recommendations.

OQ-032 repeats the advanced-order deferral already approved in OQ-004. It introduces no additional system requirement and does not reopen the approved exclusion; its impact is recorded explicitly in section 11.

## 4. Requirement Status Model

| Status | Meaning |
|--------|---------|
| APPROVED | The requirement expresses only approved OQ behavior and supporting domain definitions. It does not declare this whole requirements document reviewed or accepted. |
| PROVISIONAL | At least part of the requirement relies on a proposed OQ. The full requirement and its criteria await owner/team business review. |
| TBD | A detail lacks a business decision. This is a gap marker, not an approved behavior or an additional requirement approval state. |

Use cases and acceptance scenarios inherit PROVISIONAL if any included requirement is provisional. An approved subflow does not approve a composed provisional flow. Source BR references may cover several clauses; each requirement lists the exact OQs supporting its own clause. A change to a BR still triggers review of every requirement referencing it.

General invariants do not approve project-specific refinements: unchanged original quantity, named terminal states, treasury policy, and exceptional Trade-correction prohibitions retain their proposed dependencies. Requirements with such dependencies are not promoted by a general consistency principle.

| Requirement class | APPROVED | PROVISIONAL | Total |
|-------------------|----------|-------------|-------|
| Functional | 21 | 53 | 74 |
| Non-functional | 1 | 9 | 10 |
| All requirements | 22 | 62 | 84 |

There are 11 use cases and 31 acceptance scenarios. These counts exclude the individual local acceptance criteria and source invariant rows. No acceptance test execution is claimed: this is a reviewed documentation draft before implementation.

## 5. Terminology / Normative Language

**SHALL / SHALL NOT** state required/prohibited behavior under the item's status. **MAY** denotes permission, not a guarantee. For a PROVISIONAL item, SHALL defines the proposed behavior if accepted; it is not evidence of business approval. Test fixtures assume adequate funding, valid inputs, and different accounts unless the case explicitly tests an exception. Example prices are simulated; example fee rates are not defaults.

| Term | Meaning in this specification |
|------|-------------------------------|
| Base / Quote Asset | Asset bought/sold / asset paying for it; e.g. BTC / USDT. |
| Notional | Executed Base quantity × execution price, before fees, expressed in USDT. |
| Incoming / Resting Order | Order currently consuming eligible interest / already active standing Limit interest. |
| Original / Filled Quantity | Requested Base quantity / cumulative actual executed Base quantity. |
| Unexecuted / Active Remaining Quantity | Original minus filled / quantity still eligible to trade, zero after termination. |
| Platform acceptance sequence | Unique authoritative ordering of accepted orders; client time cannot determine priority. |
| Available / Locked Balance | Owned assets reusable for new commitments / owned assets reserved for existing commitments. |
| GTC | Good Till Cancelled; V1 Limit persistence, subject to defined ending rules. |
| STP | Self-Trade Prevention: cancel the eligible self-owned resting remainder; incoming continues. |
| Maker / Taker | Resting / incoming side of one particular Trade. |
| Simulation Funding | Explicit allocation of simulated capital, never Trade, volume, execution, or trading profit. |
| Fee Treasury | Proposed experiment-specific non-trading platform recipient of simulated USDT fees. |
| Last Price / Best Bid / Best Ask | Latest actual Trade price / highest active Bid / lowest active Ask. |
| PnL / Cost Basis | Profit and loss / assigned acquisition cost; proposed V1 accounting uses weighted average. |
| Experiment clock | Proposed authoritative virtual time for a bounded simulation, distinct from local client or real execution time. |
| `floor8(x)` / `ceil8(x)` | Round downward / upward to 8 decimal places in the stated asset units. |
| Unavailable | A value has no valid business basis; it is not silently represented as zero or substituted from a different concept. |

Prices are USDT per one Base unit; quantities identify BTC, ETH, or SOL explicitly. Rate fractions and percentages are distinguished: 0.01 percentage point = 0.0001 as a fractional rate. Derived display rounding never changes obligations. Invariant and OQ/BR identifiers retain their source meanings.

## 6. Functional Requirements

Each numbered criterion has the stable reference `<requirement ID>/AC-<number>`. For example, FR-MAT-002/AC-2 is the second criterion under FR-MAT-002. Section 9 adds composed scenarios; section 11 maps both verification forms. A criterion shares the requirement status.

### 6.1 Account / Participant

#### FR-ACT-001 — Simulated asset boundary

**Status:** APPROVED

**Requirement**

The system SHALL represent BTC, ETH, SOL, and USDT solely as simulated assets. It SHALL NOT provide real-money trading, real custody or cryptocurrency ownership, redemption, deposits, withdrawals, bank transfers, blockchain settlement, or external real-venue/data integration in V1.

**Business Traceability**

- OQ-002
- OQ-005
- BR-MKT-003
- BR-BAL-001
- BUSINESS.md sections 3, 5.

**Acceptance Criteria**

1. Given a balance in any of the four assets, it represents simulated units and creates no real asset claim or redemption right.
2. No V1 activity offers a real deposit, withdrawal, custody, blockchain settlement, or external venue/data operation.

#### FR-ACT-002 — Initial simulated capital

**Status:** APPROVED

**Requirement**

The system SHALL support configurable initial USDT for normal trader accounts and SHALL permit Base balances initially to be zero. It SHALL NOT impose a universal starting USDT amount.

**Business Traceability**

- OQ-003
- BR-SIM-001
- BUSINESS.md sections 5.

**Acceptance Criteria**

1. Given two declared initial-USDT configurations, each normal account receives the amount selected for its scenario.
2. An account may begin with USDT and zero BTC, ETH, and SOL; the approved rule alone does not require all funding to occur only at setup.

#### FR-ACT-003 — Participant capital isolation

**Status:** PROVISIONAL

**Requirement**

The system SHALL provide a separate trading account and balance pool for each manual participant, strategy, and liquidity scenario within an experiment. It SHALL NOT support shared strategy capital or transfers between accounts in V1.

**Business Traceability**

- OQ-043
- BR-ACT-002
- BUSINESS.md sections 4.

**Acceptance Criteria**

1. Given a manual participant, a strategy, and a liquidity scenario, each has a distinct balance pool; one cannot spend another pool.
2. An attempted account-to-account allocation of existing capital is unavailable in V1; Simulation Funding is governed separately by FR-ADM-003.

### 6.2 Simulation / Experiment

#### FR-SIM-001 — Bounded experiment and virtual clock

**Status:** PROVISIONAL

**Requirement**

The system SHALL run bounded simulation experiments with a declared virtual experiment clock beginning at 00:00:00 UTC on the scenario date. Trading SHALL be continuous while an experiment runs, without auctions or daily trading sessions; stopped experiment time SHALL NOT advance.

**Business Traceability**

- OQ-035
- BR-SIM-002
- BUSINESS.md sections 24, 29, 32.

**Acceptance Criteria**

1. Given a scenario date, its initial virtual time is that date at 00:00:00 UTC, independently of client local time.
2. While the experiment clock is stopped, experiment-time intervals do not advance; no daily session or auction interrupts a running experiment. Clock-control details remain TBD-002.

#### FR-SIM-002 — Experiment end

**Status:** PROVISIONAL

**Requirement**

The system SHALL make experiment end effective in authoritative business order, cancel all remaining active quantities with reason EXPERIMENT_ENDED, release unused reservations, and preserve resulting balances, historical Trades, fills, and fees. It SHALL NOT reverse prior Trades or label this termination EXPIRED.

**Business Traceability**

- OQ-023
- OQ-027
- OQ-030
- OQ-031
- OQ-035
- OQ-042
- OQ-045
- BR-BAL-003
- BR-ORD-003
- BR-ORD-007
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md sections 13–15, 32.

**Acceptance Criteria**

1. Given a partly filled resting Limit Order when end becomes effective, its active remainder becomes zero, state becomes CANCELLED, and reason is EXPERIMENT_ENDED.
2. Only unused reservation returns to availability; executed ownership transfers and fees remain in the preserved experiment history.

#### FR-SIM-003 — Separate reset and experiment history

**Status:** PROVISIONAL

**Requirement**

The system SHALL implement a scenario reset as ending/preserving the old experiment and starting a separate experiment with its own declared funding and history. It SHALL NOT transfer simulated assets between experiments.

**Business Traceability**

- OQ-035
- OQ-042
- OQ-045
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md sections 16, 28, 32.

**Acceptance Criteria**

1. After reset, old balances and Trades remain associated with the old experiment; new starting balances arise from separately identified Simulation Funding.
2. A fresh run does not erase or overwrite the earlier run, and does not inherit its assets by transfer.

#### FR-SIM-004 — Ordinary scenario liquidity

**Status:** PROVISIONAL

**Requirement**

The system SHALL establish initial scenario liquidity through predefined, funded scenario-account Limit Orders submitted in declared scenario action order while the experiment runs. Setup funding alone SHALL leave the book empty. Scenario inventory SHALL be finite, with no guaranteed always-on market maker, automatic inventory refill, or special matching priority.

**Business Traceability**

- OQ-003
- OQ-005
- OQ-010
- OQ-011
- OQ-035
- OQ-036
- OQ-043
- BR-ACT-001
- BR-ACT-002
- BR-SIM-001
- BUSINESS.md sections 20, 27, 32.

**Acceptance Criteria**

1. Given only setup allocations and no submitted orders, the book is empty; declared scenario Limit Orders create ordinary interest or actual matches when submitted.
2. Exhausting a scenario account inventory does not replenish it automatically; an empty or one-sided market remains a valid outcome.
3. Scenario accounts may continue with strategy participation under the ordinary funding, matching, and STP rules.

### 6.3 Order

#### FR-ORD-001 — Order types and Base quantity

**Status:** APPROVED

**Requirement**

The system SHALL support BUY and SELL Limit Orders and Market Orders only, with order quantity expressed in the symbol Base Asset. It SHALL NOT support Quote-budget Market Buy or Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged, or other advanced/conditional orders in V1.

**Business Traceability**

- OQ-004
- OQ-006
- BR-ORD-004
- BUSINESS.md sections 3, 6, 8.

**Acceptance Criteria**

1. A BTC/USDT Market Buy requests a BTC quantity, rather than an instruction to spend a USDT budget; Market Sell likewise specifies BTC.
2. An advanced/conditional order instruction is outside V1; no trigger behavior is introduced.

#### FR-ORD-002 — Limit price eligibility

**Status:** APPROVED

**Requirement**

The system SHALL treat a BUY Limit Price as the maximum and a SELL Limit Price as the minimum permitted unit execution price. An incoming Limit Order SHALL consume eligible resting opposite-side interest before any GTC remainder rests.

**Business Traceability**

- OQ-009
- OQ-012
- BR-ORD-005
- BR-ORD-002
- ORD-INV-003
- BUSINESS.md sections 7, 17.

**Acceptance Criteria**

1. A BUY LIMIT at 90,000 USDT/BTC can execute against an Ask at 86,000 USDT/BTC, but not 91,000 USDT/BTC.
2. A SELL LIMIT at 85,000 USDT/BTC can execute against a Bid at 87,000 USDT/BTC, but not 84,000 USDT/BTC.

#### FR-ORD-003 — Limit GTC remainder

**Status:** APPROVED

**Requirement**

The system SHALL use GTC for every Limit Order and SHALL keep an unfilled Limit remainder eligible as resting interest until it is filled, effectively cancelled, or ended by another defined V1 ending rule. It SHALL preserve earlier fills without guaranteeing a future fill.

**Business Traceability**

- OQ-007
- OQ-012
- BR-ORD-002
- BUSINESS.md sections 7, 11, 15.

**Acceptance Criteria**

1. Given a 10 BTC Limit Buy and only 4 BTC of immediately eligible interest, 4 BTC fills and the remaining 6 BTC rests at the buy limit.
2. Given no eligible immediate interest, the accepted Limit Order may rest with no fills.

#### FR-ORD-004 — Immediate-only Market execution

**Status:** APPROVED

**Requirement**

The system SHALL execute a Market Order only against immediately available eligible resting interest, preserve any completed partial fills, and terminate every unfilled remainder immediately. A Market remainder SHALL NOT rest on the book, and the trader SHALL NOT be required to specify IOC.

**Business Traceability**

- OQ-006
- OQ-007
- OQ-012
- BR-ORD-002
- BR-ORD-004
- BUSINESS.md sections 8, 11, 15.

**Acceptance Criteria**

1. A Market request for 5 BTC with only 3 BTC immediately eligible executes those 3 BTC and ends the remaining 2 BTC without reversing the fills.
2. With zero eligible liquidity, there are zero Trades and no execution; the entire remainder ends and never rests.

#### FR-ORD-005 — Absolute Market Buy protection

**Status:** APPROVED

**Requirement**

The system SHALL require an absolute maximum execution price for every Market Buy and SHALL NOT execute above it. A percentage slippage instruction SHALL NOT substitute for that price. The bound SHALL constrain unit execution price, with fees considered separately.

**Business Traceability**

- OQ-008
- BR-ORD-004
- ORD-INV-003
- BUSINESS.md sections 8, 17.

**Acceptance Criteria**

1. For a 2 BTC Market Buy capped at 86,000 USDT/BTC, with 1 BTC offered at 86,000 and further interest at 87,000 USDT/BTC, only the 1 BTC within the cap executes.
2. A missing maximum or a percentage-only instruction cannot authorize Market Buy execution; a price exactly at the maximum is eligible.

#### FR-ORD-006 — Absolute Market Sell protection

**Status:** APPROVED

**Requirement**

The system SHALL require an absolute minimum execution price for every Market Sell and SHALL NOT execute below it. A percentage instruction SHALL NOT replace the absolute minimum; protection SHALL NOT guarantee complete execution or execution exactly at the minimum.

**Business Traceability**

- OQ-008
- BR-ORD-004
- ORD-INV-003
- BUSINESS.md sections 8, 17.

**Acceptance Criteria**

1. A Market Sell with minimum 80,000 USDT/BTC may execute at resting Bids of 82,000 and 80,000, but never 79,000 USDT/BTC.
2. A missing minimum or a percentage-only instruction cannot authorize Market Sell execution; remaining quantity ends if no eligible Bid remains.

#### FR-ORD-007 — Separate Time In Force scope

**Status:** APPROVED

**Requirement**

The system SHALL keep Order Type distinct from Time In Force and SHALL NOT offer explicit IOC or FOK instructions in V1. Limit persistence SHALL be GTC and Market execution SHALL be intrinsically immediate-only.

**Business Traceability**

- OQ-012
- BR-ORD-002
- BUSINESS.md sections 15.

**Acceptance Criteria**

1. A valid protected Market request needs no separate IOC selection.
2. V1 does not offer a FOK instruction that demands complete execution or an IOC option for a Limit Order.

#### FR-ORD-008 — Price tick and bounds

**Status:** PROVISIONAL

**Requirement**

The system SHALL validate all Limit and Market protection prices on a 0.01 USDT-per-Base-unit grid within the inclusive range 0.01–1,000,000.00 USDT per Base unit for all three symbols. It SHALL reject off-grid or out-of-range prices before trading, without rounding them.

**Business Traceability**

- OQ-017
- BR-PREC-001
- BUSINESS.md sections 6.

**Acceptance Criteria**

1. Prices 0.01 and 1,000,000.00 satisfy the price-bound check; 0, 1,000,000.01, and 86,000.001 fail it.
2. The identical grid and range checks apply to a Limit Price, a Market Buy maximum, and a Market Sell minimum.

#### FR-ORD-009 — Quantity steps

**Status:** PROVISIONAL

**Requirement**

The system SHALL require positive order quantities in multiples of 0.000001 BTC, 0.00001 ETH, or 0.001 SOL for the respective symbols. It SHALL reject off-step requests without rounding them; fills and active remainders SHALL retain valid step multiples.

**Business Traceability**

- OQ-018
- BR-PREC-001
- BUSINESS.md sections 6.

**Acceptance Criteria**

1. 0.000001 BTC, 0.00001 ETH, and 0.001 SOL satisfy their respective step checks; 0.0000015 BTC, 0.000015 ETH, and 0.0015 SOL fail.
2. Zero or negative quantity cannot trade; quantity-step validation never silently changes requested quantity.

#### FR-ORD-010 — Minimum quantities without notional minimum

**Status:** PROVISIONAL

**Requirement**

The system SHALL set the minimum order quantity to one symbol quantity step, without a minimum notional or a separate minimum fill/remainder. It SHALL NOT terminate a positive valid step-multiple remainder merely for being small.

**Business Traceability**

- OQ-018
- OQ-019
- BR-PREC-001
- BUSINESS.md sections 6.

**Acceptance Criteria**

1. A one-step order passes the minimum-quantity check regardless of its small valid notional, subject to the other checks.
2. A one-step remainder is eligible under normal order rules and is not removed by an additional minimum-fill policy.

#### FR-ORD-011 — Maximum quantities without notional cap

**Status:** PROVISIONAL

**Requirement**

The system SHALL limit original quantity to at most 100 BTC, 1,000 ETH, or 100,000 SOL per order, respectively, without an additional notional cap.

**Business Traceability**

- OQ-020
- BR-PREC-001
- BUSINESS.md sections 6.

**Acceptance Criteria**

1. Quantities at each maximum pass the maximum-quantity check, subject to other validation and funding.
2. 100.000001 BTC, 1,000.00001 ETH, and 100,000.001 SOL exceed their respective maxima and fail before trading.

#### FR-ORD-012 — Five observable lifecycle states

**Status:** PROVISIONAL

**Requirement**

The system SHALL expose exactly OPEN, PARTIALLY_FILLED, FILLED, CANCELLED, and REJECTED as V1 order states with the meanings and transitions in the lifecycle table below. FILLED, CANCELLED, and REJECTED SHALL be terminal and SHALL never reactivate.

**Business Traceability**

- OQ-025
- OQ-026
- OQ-027
- BR-ORD-003
- BUSINESS.md sections 13.

**Acceptance Criteria**

1. An accepted Limit initially resolves to OPEN, PARTIALLY_FILLED, or FILLED; an accepted Market initially resolves to FILLED or CANCELLED.
2. OPEN may become PARTIALLY_FILLED, FILLED, or CANCELLED; PARTIALLY_FILLED may remain PARTIALLY_FILLED or become FILLED or CANCELLED.
3. A later fill or cancel attempt cannot make a terminal order active.

#### FR-ORD-013 — Acceptance without NEW state

**Status:** PROVISIONAL

**Requirement**

The system SHALL represent acceptance as a business event followed by the applicable initial outcome, and SHALL NOT expose NEW as a V1 order state.

**Business Traceability**

- OQ-025
- OQ-027
- BR-ORD-003
- BUSINESS.md sections 13.

**Acceptance Criteria**

1. An accepted unfilled resting Limit is observed as OPEN, rather than NEW.
2. An accepted fully executed incoming order is observed as FILLED; an invalid request is REJECTED without acceptance.

#### FR-ORD-014 — Cancellation request versus outcome

**Status:** PROVISIONAL

**Requirement**

The system SHALL record the effective cancellation outcome and SHALL NOT expose CANCEL_PENDING as an order state. An outstanding request SHALL NOT itself establish that cancellation was effective.

**Business Traceability**

- OQ-026
- OQ-031
- BR-ORD-003
- BR-ORD-007
- BUSINESS.md sections 13, 14.

**Acceptance Criteria**

1. An order with a cancellation request remains subject to authoritative order/fill precedence until a final cancellation outcome is effective.
2. A too-late cancellation reports the final outcome rather than asserting that the request reversed an earlier fill.

#### FR-ORD-015 — Termination reasons

**Status:** PROVISIONAL

**Requirement**

The system SHALL distinguish USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, and EXPERIMENT_ENDED from lifecycle states. Every valid incompletely filled Market Order, including zero-fill execution, SHALL end CANCELLED with MARKET_REMAINDER.

**Business Traceability**

- OQ-027
- OQ-035
- BR-ORD-003
- BR-SIM-002
- BUSINESS.md sections 13.

**Acceptance Criteria**

1. An incomplete Market ending because of liquidity or protection is CANCELLED/MARKET_REMAINDER, never finally PARTIALLY_FILLED.
2. A user cancellation, prevented self-match, and experiment end have their respective distinct reasons without introducing more order states.

#### FR-ORD-016 — Order quantity accounting

**Status:** PROVISIONAL

**Requirement**

The system SHALL maintain 0 ≤ Filled Quantity ≤ Original Quantity and Original Quantity = Filled Quantity + Unexecuted Quantity. Original Quantity SHALL remain unchanged. Active Remaining Quantity SHALL equal Unexecuted Quantity while active and zero in every terminal state; each actual Trade SHALL contribute once to filled quantity.

**Business Traceability**

- OQ-027
- OQ-028
- BR-ORD-003
- BR-ORD-007
- ORD-INV-001
- ORD-INV-002
- TRD-INV-003
- BUSINESS.md sections 6, 11, 13, 30.

**Acceptance Criteria**

1. For an original 10 BTC order with 4 BTC executed and its remainder cancelled, original is 10 BTC, filled 4 BTC, unexecuted 6 BTC, and active remaining 0 BTC.
2. Re-observing an existing Trade does not increase filled quantity again; a fully filled order has filled equal to original and no active remainder.

#### FR-ORD-017 — No amendment

**Status:** PROVISIONAL

**Requirement**

The system SHALL NOT amend an existing order original quantity, limit price, or Market protection boundary in V1.

**Business Traceability**

- OQ-028
- BR-ORD-007
- ORD-INV-001
- BUSINESS.md sections 6, 14, 32.

**Acceptance Criteria**

1. A request to change an existing order quantity or price cannot alter that order commitment or its priority.
2. Changing trading intent requires the separately defined cancel-then-new flow.

#### FR-ORD-018 — Separate cancellation then new order

**Status:** PROVISIONAL

**Requirement**

The system SHALL support changing intent only through effective cancellation of the old remainder followed by an independent new order, with fresh validation, funding checks, and acceptance priority. It SHALL NOT provide a combined cancel-and-replace operation or restore the old order when the new request fails.

**Business Traceability**

- OQ-010
- OQ-021
- OQ-028
- OQ-029
- OQ-031
- BR-ORD-007
- BR-BAL-004
- BR-ORD-001
- BUSINESS.md sections 14, 32.

**Acceptance Criteria**

1. After effective cancellation and a valid new submission at the same price, the new order has a new acceptance sequence and cannot inherit the old queue position.
2. If the new request is rejected, the old remainder stays ended and prior fills remain.

#### FR-ORD-019 — No automatic GTC expiry

**Status:** PROVISIONAL

**Requirement**

The system SHALL NOT apply automatic expiry, a maximum GTC lifetime within an experiment, or an EXPIRED state. Experiment end SHALL use CANCELLED/EXPERIMENT_ENDED.

**Business Traceability**

- OQ-012
- OQ-027
- OQ-030
- OQ-035
- BR-ORD-002
- BR-ORD-003
- BR-ORD-007
- BR-SIM-002
- BUSINESS.md sections 15, 32.

**Acceptance Criteria**

1. Advancing time alone does not expire an active GTC Limit during a running experiment.
2. Ending the experiment terminates its active GTC remainder by cancellation, without creating EXPIRED.

#### FR-ORD-020 — Effective cancellation of own remainder

**Status:** PROVISIONAL

**Requirement**

The system SHALL permit a participant to cancel only its own account active unfilled remainder. Effective cancellation SHALL prevent later execution of that remainder while preserving earlier fills and fees. Too-late/already-filled or already-ended outcomes SHALL make no further changes or duplicate releases; a nonexistent or rejected request SHALL have no active order to cancel.

**Business Traceability**

- OQ-023
- OQ-027
- OQ-031
- BR-ORD-003
- BR-ORD-007
- BR-BAL-003
- ORD-INV-004
- BAL-INV-003
- BUSINESS.md sections 14.

**Acceptance Criteria**

1. Cancelling a 0.6 BTC remainder after a valid 0.4 BTC fill preserves the 0.4 BTC purchase and releases only unused reservation.
2. After full fill, cancellation returns too late/already filled; after a prior cancellation, it returns already ended; neither refunds executed assets or fees.
3. An attempt to cancel another account order cannot end that order.

The following table is **PROVISIONAL**, part of FR-ORD-012 / FR-ORD-015 / FR-ORD-016, from BUSINESS.md section 13 and OQ-025, OQ-026, OQ-027:

| State | Meaning | Terminal? |
|-------|---------|-----------|
| OPEN | Accepted GTC Limit resting without fills. | No |
| PARTIALLY_FILLED | GTC Limit with fills and an active resting remainder. | No |
| FILLED | Filled Quantity equals Original Quantity. | Yes |
| CANCELLED | Unfilled portion ended; Filled Quantity may be zero or positive. | Yes |
| REJECTED | Validation/funding failed before acceptance, Trades, or STP effects. | Yes |

The lifecycle labels do not downgrade approved Market remainder termination or GTC behavior; they are a proposed representation of those outcomes.

### 6.4 Matching

#### FR-MAT-001 — Better price priority

**Status:** APPROVED

**Requirement**

The system SHALL match same-symbol, opposite-side eligible interest at better prices first: the lowest Ask for an incoming BUY and the highest Bid for an incoming SELL.

**Business Traceability**

- OQ-010
- BR-ORD-001
- BUSINESS.md sections 10.

**Acceptance Criteria**

1. A later Ask at 85,000 USDT/BTC is selected before an earlier Ask at 86,000 USDT/BTC by a BUY able to pay either price.
2. A SELL eligible at both prices selects a Bid at 85,500 before a Bid at 85,000 USDT/BTC; interest in another symbol is not a counterparty.

#### FR-MAT-002 — Same-price acceptance-sequence priority

**Status:** APPROVED

**Requirement**

The system SHALL prioritize equal-price resting orders by their earlier unique platform-authoritative acceptance sequence. Client timestamps, equal displayed times, and larger quantity SHALL NOT change that priority. A resting remainder SHALL retain its original order acceptance sequence.

**Business Traceability**

- OQ-010
- BR-ORD-001
- ORD-INV-005
- BUSINESS.md sections 10.

**Acceptance Criteria**

1. Given Ask A 0.4 BTC at sequence #1001 and Ask B 2 BTC at #1002, both at 86,000 USDT/BTC, a 1 BTC BUY fills A 0.4 BTC before B 0.6 BTC.
2. Changing B client timestamp to an earlier time, or giving A and B equal displayed times, does not move B ahead of A.
3. Increasing B size does not let it overtake A; a partially executed resting remainder keeps its original sequence.

#### FR-MAT-003 — Resting-order execution price

**Status:** APPROVED

**Requirement**

The system SHALL execute each Trade at the matched resting order price, within both applicable order boundaries. Incoming limits/protection SHALL determine eligibility rather than substitute an execution price. Both counterparties SHALL agree on symbol, Base quantity, and execution price.

**Business Traceability**

- OQ-008
- OQ-009
- BR-ORD-005
- BR-ORD-004
- ORD-INV-003
- TRD-INV-002
- BUSINESS.md sections 7, 16, 17.

**Acceptance Criteria**

1. A resting BUY at 90,000 USDT/BTC matched by an incoming SELL limit 86,000 executes at 90,000; reversing which side rests gives 86,000.
2. A Market Buy capped at 90,000 cannot match a resting Ask at 91,000 USDT/BTC; several eligible resting levels execute at their respective prices.

#### FR-MAT-004 — Cancel self-owned resting remainder

**Status:** APPROVED

**Requirement**

The system SHALL check trading-account identity at the next eligible resting match. If both orders belong to the same account, it SHALL cancel that resting order active remainder, distinguish SELF_TRADE_PREVENTION from user cancellation, preserve prior fills, and continue the incoming order. It SHALL NOT merely skip and leave the self-owned remainder active or group distinct accounts by human ownership.

**Business Traceability**

- OQ-011
- BR-ORD-006
- BR-ACT-001
- TRD-INV-004
- BUSINESS.md sections 10.

**Acceptance Criteria**

1. Given account A incoming BUY, A eligible resting SELL 0.4 BTC, then B eligible SELL 0.6 BTC, A resting remainder is cancelled and the incoming BUY continues to B.
2. Previous fills of the cancelled resting order remain; orders of two distinct accounts are not treated as self-owned solely because their human owner is the same.

#### FR-MAT-005 — No execution effects from STP

**Status:** APPROVED

**Requirement**

The system SHALL create no Trade, traded volume, Last Price update, or fee for a prevented self-match. STP SHALL be allowed to change book depth by removing the cancelled resting quantity.

**Business Traceability**

- OQ-011
- BR-ORD-006
- TRD-INV-004
- BUSINESS.md sections 10, 23.

**Acceptance Criteria**

1. When the only eligible match is self-owned, the prevented quantity adds zero Trades, zero Base/Quote traded volume, no Last Price change, and zero fees.
2. If the incoming order subsequently matches another account, only that actual Trade has execution effects.

#### FR-MAT-006 — Deterministic Trade quantity per eligible pair

**Status:** PROVISIONAL

**Requirement**

The system SHALL execute the smaller active remaining Base quantity of each eligible non-self order pair as one Trade before continuing matching as needed.

**Business Traceability**

- OQ-031
- BR-ORD-007
- BUSINESS.md sections 10, 32.

**Acceptance Criteria**

1. For eligible opposing active quantities of 0.4 BTC and 1 BTC, the pair creates one 0.4 BTC Trade, leaving 0.6 BTC on the larger order.
2. The system does not split that pair arbitrarily into smaller Trades that change per-Trade rounding outcomes.

#### FR-MAT-007 — Authoritative order, cancellation, and end precedence

**Status:** PROVISIONAL

**Requirement**

The system SHALL govern order, cancellation, and experiment-end effectiveness by authoritative business ordering. It SHALL complete each incoming order immediate outcome, including STP and all immediate fills, before the next action becomes effective. Client timestamps SHALL NOT override this precedence.

**Business Traceability**

- OQ-010
- OQ-031
- OQ-035
- BR-ORD-001
- BR-ORD-007
- BR-SIM-002
- BUSINESS.md sections 10, 14, 32.

**Acceptance Criteria**

1. If execution precedes effective cancellation, the fill stands and cancellation can stop only the remainder; if cancellation precedes a later order action, the cancelled remainder cannot execute.
2. A later cancellation/end action cannot intervene between the STP/fills belonging to one incoming order immediate outcome.

### 6.5 Balance / Reservation

#### FR-BAL-001 — Fully funded simulated spot

**Status:** APPROVED

**Requirement**

The system SHALL allow BUY commitments only with available Quote and SELL commitments only with available owned Base, including enabled fee obligations. It SHALL NOT provide borrowing, margin, leverage, short selling, futures, perpetuals, options, or other derivatives.

**Business Traceability**

- OQ-002
- OQ-005
- OQ-013
- BR-BAL-001
- BR-FEE-001
- BAL-INV-004
- BUSINESS.md sections 3, 12.

**Acceptance Criteria**

1. An account cannot sell Base it does not own or fund a BUY through credit.
2. Available and locked balances remain nonnegative; enabled fees cannot create an unfunded obligation.

#### FR-BAL-002 — Exclusive use of reserved assets

**Status:** APPROVED

**Requirement**

The system SHALL prevent assets already reserved for one commitment from funding another, including USDT commitments across BTC/USDT, ETH/USDT, and SOL/USDT.

**Business Traceability**

- OQ-005
- BR-BAL-001
- BAL-INV-001
- BUSINESS.md sections 12, 30.

**Acceptance Criteria**

1. Given USDT already committed to an active BTC order, an ETH or SOL order cannot reuse that committed amount.
2. Locked Base cannot simultaneously fund two independent SELL commitments.

#### FR-BAL-003 — Full funding before acceptance and STP

**Status:** PROVISIONAL

**Requirement**

The system SHALL validate funding for the entire requested protected quantity before acceptance, Trades, or STP effects. Insufficient available funding SHALL produce REJECTED without fills or silent resizing, and SHALL NOT rely on releasing a self-owned resting order later.

**Business Traceability**

- OQ-021
- OQ-022
- OQ-027
- BR-BAL-004
- BR-BAL-002
- BR-ORD-003
- BUSINESS.md sections 8, 12, 13.

**Acceptance Criteria**

1. Given a required reservation of 180,360 USDT and only 180,359 USDT available, the request is REJECTED even if current visible liquidity would cost less.
2. Given an otherwise eligible self-owned resting order, the underfunded incoming request creates no Trade and does not cancel the existing self-owned order.

#### FR-BAL-004 — Limit Buy reservation

**Status:** PROVISIONAL

**Requirement**

The system SHALL reserve `ceil8(q × limit × (1 + max(rM, rT)))` USDT for a Limit Buy, where q is active Base quantity and rM/rT are the configured maker/taker rate fractions. The reservation SHALL cover a later change from Taker to Maker.

**Business Traceability**

- OQ-013
- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-046
- BR-BAL-002
- BR-FEE-001
- BR-PREC-001
- BUSINESS.md sections 12.

**Acceptance Criteria**

1. For 1 BTC at 80,000 USDT/BTC, maker 0.1% and taker 0.2%, required reserve is 80,160 USDT.
2. If maker exceeds taker, the larger maker rate still determines the Limit Buy reserve; round the required amount upward to 8 USDT decimals.

#### FR-BAL-005 — Protected Market Buy reservation

**Status:** PROVISIONAL

**Requirement**

The system SHALL reserve `ceil8(q × maximum execution price × (1 + rT))` USDT for a Market Buy, using its full requested Base quantity and taker rate rather than apparent book cost.

**Business Traceability**

- OQ-008
- OQ-015
- OQ-016
- OQ-022
- OQ-046
- BR-BAL-002
- BR-PREC-001
- BUSINESS.md sections 12.

**Acceptance Criteria**

1. For 2 BTC capped at 90,000 USDT/BTC with taker 0.2%, required reserve is 180,360 USDT.
2. The fee reserve does not permit execution above the 90,000 USDT/BTC unit-price maximum.

#### FR-BAL-006 — Sell Base reservation

**Status:** PROVISIONAL

**Requirement**

The system SHALL reserve the SELL active Base quantity for both Limit and Market Sell, without an extra USDT reservation; its USDT fee SHALL be deducted from actual sale proceeds.

**Business Traceability**

- OQ-015
- OQ-016
- OQ-022
- BR-BAL-002
- BR-FEE-002
- BUSINESS.md sections 12.

**Acceptance Criteria**

1. With 3 BTC owned, reserving a 2 BTC SELL leaves 1 BTC available and 2 BTC locked.
2. A 0.5 BTC execution leaves 1.5 BTC committed to the active remainder; the sale fee comes from the USDT proceeds.

#### FR-BAL-007 — Recalculate and release surplus after every Trade

**Status:** PROVISIONAL

**Requirement**

The system SHALL account for actual Trade cost, recompute the reservation for active remaining quantity, and release surplus immediately after every Trade using the applicable reservation formula.

**Business Traceability**

- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-023
- OQ-024
- OQ-046
- BR-BAL-002
- BR-BAL-003
- BR-STL-001
- BAL-INV-002
- BAL-INV-003
- BUSINESS.md sections 12, 18.

**Acceptance Criteria**

1. For the 1 BTC Limit Buy at 80,000 USDT/BTC with maker 0.1%, taker 0.2%, a 0.4 BTC taker fill at 75,000 costs 30,060 USDT and leaves reserve 48,096 USDT.
2. From the initial 80,160 USDT reserve, surplus released is 2,004 USDT; with initial 100,000 USDT, available becomes 21,844 and locked 48,096 USDT.

#### FR-BAL-008 — Release unused reservation at termination

**Status:** PROVISIONAL

**Requirement**

The system SHALL release all unused reservation when an order remainder ends, without returning assets already exchanged or fees already paid. Repeated observation or an already-ended cancellation SHALL NOT release the same funds twice.

**Business Traceability**

- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-023
- OQ-024
- OQ-027
- OQ-031
- OQ-046
- BR-BAL-002
- BR-BAL-003
- BR-STL-001
- BR-ORD-003
- BR-ORD-007
- BR-FEE-001
- BR-FEE-002
- BR-PREC-001
- BAL-INV-002
- BAL-INV-003
- BUSINESS.md sections 12, 14.

**Acceptance Criteria**

1. In the fee-enabled partial-buy example, cancelling the remaining 0.6 BTC releases 48,096 USDT and leaves 69,940 USDT available, 0 USDT locked, and the acquired 0.4 BTC intact.
2. The paid 60 USDT fee and 30,000 USDT executed notional are not refunded; repeating the cancellation changes no balance.

### 6.6 Trade / Settlement

#### FR-TRD-001 — Trade facts

**Status:** PROVISIONAL

**Requirement**

The system SHALL describe each actual Trade by its buyer, seller, symbol, Base quantity, resting-order execution price, occurrence time/order, participating orders, per-Trade maker/taker roles, and each side fee. These SHALL be business facts; public observations SHALL follow the separate disclosure rule.

**Business Traceability**

- OQ-009
- OQ-014
- OQ-015
- OQ-016
- OQ-024
- OQ-037
- BR-ORD-005
- BR-FEE-001
- BR-FEE-002
- BR-STL-001
- BR-MKT-002
- TRD-INV-002
- BUSINESS.md sections 16, 18.

**Acceptance Criteria**

1. Both sides of one Trade agree on quantity, symbol, and execution price while their fees may differ.
2. Order submission, reservation, Simulation Funding, and a prevented self-match do not create such an actual-execution record.

#### FR-TRD-002 — Immediate ownership settlement

**Status:** PROVISIONAL

**Requirement**

The system SHALL settle each actual Trade immediately: the buyer receives the full Base quantity and pays notional plus buyer fee; the seller gives that Base quantity and receives notional minus seller fee; Fee Treasury receives both fees. Received Base and net USDT proceeds SHALL be available immediately, with no unsettled balance stage.

**Business Traceability**

- OQ-014
- OQ-015
- OQ-016
- OQ-023
- OQ-024
- OQ-046
- BR-STL-001
- BR-BAL-003
- BR-FEE-001
- BR-FEE-002
- STL-INV-001
- STL-INV-002
- BUSINESS.md sections 18.

**Acceptance Criteria**

1. For 1 BTC at 86,000 USDT/BTC, buyer Taker 0.2% and seller Maker 0.1%, buyer pays 86,172 USDT, seller receives 85,914 USDT, and treasury receives 258 USDT.
2. Only executed quantity transfers; remaining unfilled quantity creates no asset exchange.

#### FR-TRD-003 — Exactly-once settlement effect

**Status:** PROVISIONAL

**Requirement**

The system SHALL apply each Trade asset and fee transfers exactly once. Reporting or observing that Trade again SHALL NOT settle it again, increase filled totals again, or release its funds again.

**Business Traceability**

- OQ-024
- OQ-046
- BR-STL-001
- STL-INV-003
- TRD-INV-003
- BUSINESS.md sections 18, 30.

**Acceptance Criteria**

1. After one Trade settles, repeated presentation of the same Trade leaves every participant and treasury balance unchanged.
2. Each participating order filled quantity includes that Trade quantity once.

#### FR-TRD-004 — Immutable Trade history

**Status:** PROVISIONAL

**Requirement**

The system SHALL preserve confirmed Trades as immutable historical facts and SHALL NOT support editing, deletion, reversal, exceptional correction, or administrative rewriting of them in V1. Simulation Funding SHALL NOT be used as a disguised Trade correction.

**Business Traceability**

- OQ-045
- BR-TRD-001
- TRD-INV-001
- BUSINESS.md sections 16, 32.

**Acceptance Criteria**

1. An administrator cannot change a completed Trade price, quantity, counterparties, or fees, delete it, or undo its ownership exchange.
2. An invalid experiment remains available with the research-validity handling of FR-ADM-004 rather than corrected Trade history.

### 6.7 Fee

#### FR-FEE-001 — Configurable simulated fees

**Status:** APPROVED

**Requirement**

The system SHALL support explicitly configurable simulated Maker and Taker rates, which may be equal or different and may be zero. Enabled fees SHALL be reflected consistently in funding, reservations, settlement, balances, strategy results, and supported PnL.

**Business Traceability**

- OQ-013
- OQ-014
- BR-FEE-001
- BUSINESS.md sections 26.

**Acceptance Criteria**

1. A configured zero-fee scenario produces no trading fee; a scenario with different Maker/Taker rates uses the role applicable to each Trade.
2. A charged fee is included consistently in the supported balance/performance views; selecting zero in an example does not establish a default for all experiments.

#### FR-FEE-002 — Maker and Taker per Trade

**Status:** APPROVED

**Requirement**

The system SHALL classify the resting side as Maker and the incoming side as Taker separately for every Trade. It SHALL NOT assign one permanent Maker/Taker classification to an entire order.

**Business Traceability**

- OQ-014
- BR-FEE-001
- BUSINESS.md sections 16, 26.

**Acceptance Criteria**

1. An incoming 2 BTC Limit Buy consumes a resting 1 BTC Ask as Taker; its 1 BTC remainder rests and is Maker when a later SELL executes against it.
2. Each execution uses that execution role even though both fills belong to the same BUY order.

#### FR-FEE-003 — USDT Fee Treasury

**Status:** PROVISIONAL

**Requirement**

The system SHALL charge both counterparties fees in USDT and credit exactly those amounts to a platform-owned, non-trading Fee Treasury within the experiment, initially zero. Buyer fee SHALL be additional to notional; seller fee SHALL come from proceeds. Treasury receipts SHALL NOT count as strategy profit.

**Business Traceability**

- OQ-015
- BR-FEE-002
- BUSINESS.md sections 18, 26.

**Acceptance Criteria**

1. A new experiment treasury starts with 0 USDT; the buyer and seller fee deductions exactly equal the treasury increase.
2. Treasury funds cannot submit trading orders and are not attributed as participant trading profit.

#### FR-FEE-004 — Per-Trade fee calculation and rounding

**Status:** PROVISIONAL

**Requirement**

The system SHALL calculate each side fee for each Trade as `floor8(notional × applicable rate)` USDT. It SHALL NOT apply minimum fees, rebates, discounts, a separate fee token, hidden rounding charges, or aggregate-order fee calculation.

**Business Traceability**

- OQ-014
- OQ-015
- OQ-016
- OQ-046
- BR-FEE-002
- BR-PREC-001
- BUSINESS.md sections 26, 32.

**Acceptance Criteria**

1. For 86,000 USDT notional, 0.1% produces 86 USDT and 0.2% produces 172 USDT.
2. An exact computed fee of 0.000000000001 USDT floors to 0.00000000 USDT, without a minimum charge; fees are floored separately for each actual Trade and side.

#### FR-FEE-005 — Rate validation and experiment stability

**Status:** PROVISIONAL

**Requirement**

The system SHALL require explicit Maker and Taker rates at setup, each inclusively between 0% and 1%, in 0.01 percentage-point steps (0.0001 as a fraction). It SHALL keep both fixed for the experiment and SHALL NOT infer an implicit default.

**Business Traceability**

- OQ-016
- OQ-042
- BR-FEE-002
- BR-SIM-002
- BUSINESS.md sections 26, 32.

**Acceptance Criteria**

1. 0%, 0.01%, and 1% pass rate validation; negative rates, 1.01%, and 0.015% fail.
2. A running experiment cannot change either rate; a comparison with changed rates uses a separate configured experiment.

### 6.8 Market Data

#### FR-MKT-001 — Exact V1 markets

**Status:** APPROVED

**Requirement**

The system SHALL support exactly BTC/USDT, ETH/USDT, and SOL/USDT, using USDT as the common Quote Asset. It SHALL NOT provide another Quote Asset or cross pair in V1.

**Business Traceability**

- OQ-001
- OQ-002
- BR-MKT-003
- BUSINESS.md sections 3, 5.

**Acceptance Criteria**

1. Each supported market identifies BTC, ETH, or SOL as Base and USDT as Quote.
2. BTC/ETH and any additional symbol are outside the V1 market set.

#### FR-MKT-002 — Full aggregated book depth

**Status:** PROVISIONAL

**Requirement**

The system SHALL expose full aggregated active resting quantity at every price level for each symbol. Aggregation SHALL NOT merge individual order ownership or acceptance priority.

**Business Traceability**

- OQ-037
- BR-MKT-002
- BUSINESS.md sections 9, 23.

**Acceptance Criteria**

1. Two active Asks of 0.4 and 0.6 BTC at 86,000 USDT/BTC appear as 1 BTC at that level while retaining separate matching priority.
2. All active levels are represented; cancelling a remainder reduces its level quantity even without a Trade.

#### FR-MKT-003 — Own-account queue and outcome observations

**Status:** PROVISIONAL

**Requirement**

The system SHALL expose to each participant its own active orders, acceptance priority, same-price order count and active Base quantity ahead, fills, fees, balances, and termination reasons. Queue observations SHALL include all earlier active same-price orders and SHALL NOT imply guaranteed future execution.

**Business Traceability**

- OQ-010
- OQ-027
- OQ-037
- BR-ORD-001
- BR-ORD-003
- BR-MKT-002
- BUSINESS.md sections 23.

**Acceptance Criteria**

1. For an own resting order behind earlier same-price quantities of 0.4 and 0.6 BTC, the observation is two orders and 1 BTC ahead.
2. A later observation reflects preceding orders that have filled or ended; displayed position does not promise a fill.

#### FR-MKT-004 — Anonymous public Trade history

**Status:** PROVISIONAL

**Requirement**

The system SHALL provide anonymous actual Trade history and SHALL NOT disclose other accounts identities or individual order histories in participant market observations.

**Business Traceability**

- OQ-037
- BR-MKT-002
- BUSINESS.md sections 16, 23.

**Acceptance Criteria**

1. A participant may observe a public execution price and quantity without receiving the counterparty identities or other-account order history.
2. Own-order details remain available under FR-MKT-003 without making them public to other accounts.

#### FR-MKT-005 — Best quotes and unavailable values

**Status:** PROVISIONAL

**Requirement**

The system SHALL report Best Bid as highest active Bid, Best Ask as lowest active Ask, and Spread as Best Ask minus Best Bid. An absent side SHALL have an unavailable best quote, and if either side is absent Spread SHALL be unavailable rather than zero.

**Business Traceability**

- OQ-037
- OQ-044
- BR-MKT-001
- BR-MKT-002
- BUSINESS.md sections 9, 19, 32.

**Acceptance Criteria**

1. Best Bid 85,500 and Best Ask 86,000 USDT/BTC produce Spread 500 USDT/BTC.
2. A one-sided book has the absent quote and Spread unavailable, without using a scenario reference as a quote.

#### FR-MKT-006 — Trade-derived Last Price

**Status:** PROVISIONAL

**Requirement**

The system SHALL leave Last Price unavailable until the first actual Trade, then use the latest actual Trade in authoritative execution order. It SHALL NOT substitute Simulation Funding, a scenario acquisition reference, an order quote, cancellation, prevented self-match, or external price for Last Price.

**Business Traceability**

- OQ-003
- OQ-011
- OQ-044
- BR-MKT-001
- BR-SIM-001
- BR-ORD-006
- MKT-INV-001
- SIM-INV-001
- TRD-INV-004
- BUSINESS.md sections 19, 23, 32.

**Acceptance Criteria**

1. Before any Trade, funding and resting orders leave Last Price unavailable.
2. After a Trade at 87,000 USDT/BTC, a quote cancellation changes depth but leaves Last Price 87,000; a later actual Trade alone advances it.

#### FR-MKT-007 — Base and gross Quote volume

**Status:** PROVISIONAL

**Requirement**

The system SHALL count each actual Trade once per symbol in Base quantity and gross USDT notional, for each one-minute experiment-time interval and cumulatively for the experiment. It SHALL exclude fees, submissions, funding, cancellation, and STP and SHALL NOT provide rolling 24-hour volume in V1.

**Business Traceability**

- OQ-003
- OQ-011
- OQ-035
- OQ-038
- OQ-039
- BR-MKT-001
- BR-MKT-004
- MKT-INV-002
- BUSINESS.md sections 23, 24.

**Acceptance Criteria**

1. One 0.5 BTC Trade at 86,000 USDT/BTC adds 0.5 BTC and 43,000 USDT volume, not twice those values for two counterparties.
2. Fees and non-execution activity add no volume; cumulative totals equal the sum of the experiment actual Trades.

#### FR-MKT-008 — One-minute OHLCV

**Status:** PROVISIONAL

**Requirement**

The system SHALL produce one-minute OHLCV on virtual experiment UTC time, including interval start and excluding end. Open/Close SHALL use first/last actual Trade in authoritative execution order, High/Low the extrema, and volumes the actual Trade sums. Same-time Trades SHALL follow execution order.

**Business Traceability**

- OQ-035
- OQ-038
- OQ-039
- BR-MKT-001
- BR-MKT-004
- BUSINESS.md sections 24.

**Acceptance Criteria**

1. For the source minute with prices 86,000, 88,000, 85,000, 87,000 and respective quantities 1, 0.5, 2, 0.5 BTC, OHLC is 86,000/88,000/85,000/87,000 USDT/BTC; volumes are 4 BTC and 343,500 USDT.
2. A Trade exactly at 10:01:00 belongs to the interval beginning 10:01:00, not the preceding minute; same-time first/last follow business execution order.

#### FR-MKT-009 — Empty and final partial candles

**Status:** PROVISIONAL

**Requirement**

The system SHALL report unavailable OHLC and zero Base/Quote volumes for an empty interval, without carrying forward a fabricated candle price. It SHALL retain and label an incomplete final interval at experiment end. Last Price MAY separately retain its last actual Trade value.

**Business Traceability**

- OQ-035
- OQ-039
- OQ-044
- BR-MKT-001
- BR-MKT-004
- BUSINESS.md sections 24.

**Acceptance Criteria**

1. An empty minute following a Trade has unavailable OHLC and zero volumes even though Last Price remains known.
2. If end occurs partway through a minute, its retained candle is labeled a partial final interval.

### 6.9 Portfolio / PnL

#### FR-PF-001 — Portfolio balances and measures

**Status:** PROVISIONAL

**Requirement**

The system SHALL report available, locked, and total balances per asset; spot positions including locked holdings; fees; weighted-average cost; realized/unrealized PnL per Base asset in USDT; and total USDT equity when all nonzero holdings can be valued. Total balance SHALL equal available plus locked.

**Business Traceability**

- OQ-024
- OQ-033
- OQ-034
- BR-STL-001
- BR-PF-001
- BUSINESS.md sections 12, 25.

**Acceptance Criteria**

1. A reservation moves assets from available to locked without reducing the position or total owned amount.
2. Known balances and realized PnL remain available even when some market-based valuation is unavailable.

#### FR-PF-002 — Weighted-average cost and realized PnL

**Status:** PROVISIONAL

**Requirement**

The system SHALL use weighted-average cost only: BUY adds notional plus buyer fee to basis; SELL removes `floor8(basis before × sold quantity / quantity before)` USDT, except full liquidation SHALL remove all remaining basis. Realized PnL SHALL equal net sale proceeds minus removed basis. Average cost SHALL be basis divided by held quantity and unavailable for zero holdings.

**Business Traceability**

- OQ-015
- OQ-016
- OQ-033
- OQ-046
- BR-PF-001
- BR-FEE-002
- BR-PREC-001
- BUSINESS.md sections 25.

**Acceptance Criteria**

1. In a zero-fee case, buying 1 BTC at 80,000 and 1 BTC at 90,000 gives 170,000 USDT basis and 85,000 USDT/BTC average; selling 0.5 BTC at 88,000 realizes 1,500 USDT and leaves 127,500 USDT basis.
2. Proportional rounding residual remains in the remaining position; the final full sale removes all residual basis, with no double subtraction of fees.

#### FR-PF-003 — Seeded capital cost basis

**Status:** PROVISIONAL

**Requirement**

The system SHALL assign seeded Base an explicit scenario acquisition-reference price valid on the symbol grid and initial basis equal to seeded quantity times that price. It SHALL separate funded capital, realized/unrealized PnL, and fees; funding SHALL NOT create trading profit or Last Price.

**Business Traceability**

- OQ-003
- OQ-017
- OQ-018
- OQ-033
- OQ-042
- OQ-046
- BR-PF-001
- BR-SIM-001
- BR-SIM-002
- BR-PREC-001
- SIM-INV-001
- BUSINESS.md sections 25.

**Acceptance Criteria**

1. Seeding 1 BTC with a declared 80,000 USDT/BTC acquisition reference records 80,000 USDT basis and funding, but no Trade, trading profit, or Last Price.
2. Seeded USDT is capital, not profit; fees included in basis/proceeds are not subtracted a second time in performance.

#### FR-PF-004 — Last Price valuation and staleness

**Status:** PROVISIONAL

**Requirement**

The system SHALL value nonzero Base holdings at the experiment Last Price and calculate unrealized PnL as marked value minus remaining basis, without hypothetical future selling fees. Without that price, holding valuation, its unrealized PnL, and total equity SHALL be unavailable. A Last Price older than 60 experiment seconds SHALL be retained but flagged stale with its age; values SHALL be identified as indicative, not guaranteed liquidation proceeds.

**Business Traceability**

- OQ-033
- OQ-034
- OQ-044
- OQ-046
- BR-PF-001
- BR-MKT-001
- BR-PREC-001
- BUSINESS.md sections 19, 25.

**Acceptance Criteria**

1. For 1.5 BTC, basis 127,500 USDT and Last Price 87,000 USDT/BTC, marked value is 130,500 and unrealized PnL 3,000 USDT.
2. Before the first Trade, seeded Base cost references do not supply missing valuation; known balances and realized PnL remain visible.
3. A Last Price aged exactly 60 seconds is not older than 60; at greater age it is flagged stale and retained.

### 6.10 Algorithmic Trading

#### FR-ALGO-001 — Strategy participation under ordinary rules

**Status:** PROVISIONAL

**Requirement**

The system SHALL support algorithmic strategy participants observing simulated market information and their own capital to submit ordinary V1 orders, cancel their active orders, or take no action. Strategies SHALL remain subject to ordinary funding, priority, protection, and STP rules and SHALL evaluate actual fills rather than assume requested quantity executed.

**Business Traceability**

- OQ-004
- OQ-005
- OQ-008
- OQ-010
- OQ-011
- OQ-040
- OQ-043
- BR-ALGO-001
- BR-ACT-002
- BR-ORD-004
- BR-BAL-001
- BR-ORD-001
- BR-ORD-006
- BUSINESS.md sections 28.

**Acceptance Criteria**

1. A strategy requesting 2 BTC but receiving 0.5 BTC observes that actual fill and cannot treat the unfilled quantity as owned.
2. A strategy has no privileged fill, shared-capital, or queue-priority bypass compared with ordinary participants.

#### FR-ALGO-002 — Synthetic scenario reruns and research scope

**Status:** PROVISIONAL

**Requirement**

The system SHALL support rerunning controlled synthetic scenarios as separate experiments with declared initial funding, fee rates, action schedules, timing assumptions, and participant decisions. It SHALL NOT provide historical-data backtesting, optimization suites, or strategy marketplaces in V1, or claim a synthetic run proves historical-market performance.

**Business Traceability**

- OQ-035
- OQ-040
- OQ-041
- OQ-042
- BR-ALGO-001
- BR-SIM-002
- BUSINESS.md sections 28, 29.

**Acceptance Criteria**

1. A repeat comparison identifies the scenario inputs and participant decisions used in each separate run; changing the strategy decisions changes the inputs.
2. Historical data replay is not presented as a V1 capability; synthetic rerun does not create a real-market performance claim.

### 6.11 HFT Experiments

#### FR-HFT-001 — Observation and action delays

**Status:** PROVISIONAL

**Requirement**

The system SHALL support explicit nonnegative observation and action delays per participant, fixed at setup for an experiment and measured on the virtual experiment clock. Observation delay SHALL affect which market information has reached a participant; action delay SHALL affect when an order/cancellation reaches authoritative business ordering. Delays SHALL NOT bypass existing priority; random timing jitter SHALL be deferred.

**Business Traceability**

- OQ-010
- OQ-031
- OQ-035
- OQ-041
- BR-ALGO-001
- BR-ORD-001
- BR-ORD-007
- BUSINESS.md sections 29.

**Acceptance Criteria**

1. Zero delay is permitted; a negative delay is invalid and configured delays cannot change during the experiment.
2. An earlier observation may differ from the book when the resulting action becomes effective; changing delay in another run cannot reorder already accepted orders.
3. The precise clock resolution and delay-delivery details remain TBD-002 and do not authorize a new priority policy.

#### FR-HFT-002 — Execution quantity, price, and cost metrics

**Status:** PROVISIONAL

**Requirement**

The system SHALL report per-order fill ratio (`filled / original`), quantity-weighted Average Execution Price (`executed notional / filled Base`), actual fees, fee-inclusive average buying cost or net selling proceeds per filled Base unit, own same-price queue count/quantity ahead, and resulting portfolio PnL. Price/cost averages SHALL be unavailable when nothing fills.

**Business Traceability**

- OQ-013
- OQ-014
- OQ-015
- OQ-016
- OQ-033
- OQ-037
- OQ-041
- OQ-046
- BR-ALGO-001
- BR-FEE-001
- BR-FEE-002
- BR-PF-001
- BR-MKT-002
- BR-PREC-001
- BUSINESS.md sections 19, 29.

**Acceptance Criteria**

1. For 1 BTC at 86,000 and 1 BTC at 87,000 on a 2 BTC request, fill ratio is 1 and Average Execution Price is 86,500 USDT/BTC before fees.
2. A zero-fill request has fill ratio 0 but unavailable average execution price and unit cost/proceeds, rather than zero price.

#### FR-HFT-003 — Decision-observed-quote slippage

**Status:** PROVISIONAL

**Requirement**

The system SHALL measure slippage against the opposing best quote actually observed at the participant decision: Ask for BUY and Bid for SELL. Adverse per-unit slippage SHALL be average minus reference for BUY and reference minus average for SELL; percentage SHALL be that difference divided by reference times 100. Missing reference or fills SHALL yield unavailable slippage. This metric SHALL NOT replace mandatory Market protection.

**Business Traceability**

- OQ-008
- OQ-041
- OQ-046
- BR-ORD-004
- BR-ALGO-001
- BR-PREC-001
- BUSINESS.md sections 21, 29.

**Acceptance Criteria**

1. BUY average 86,500 versus observed Ask 86,000 USDT/BTC gives adverse 500 USDT/BTC and displayed 0.5814%; SELL average 85,000 versus observed Bid 85,500 also gives adverse 500 USDT/BTC.
2. An improved price produces negative adverse slippage; absence of a decision-time observed quote or any fills produces unavailable metrics.

#### FR-HFT-004 — Fill timing and cancellation outcomes

**Status:** PROVISIONAL

**Requirement**

The system SHALL report experiment-clock time to first fill, time to full fill, and effective cancellation outcome. An unachieved first/full fill SHALL have an unavailable elapsed metric rather than zero; displayed-time ties SHALL retain authoritative business order. The metric origin SHALL remain TBD-003 until clarified in the business baseline.

**Business Traceability**

- OQ-026
- OQ-031
- OQ-035
- OQ-041
- BR-ALGO-001
- BR-ORD-003
- BR-ORD-007
- BUSINESS.md sections 29.

**Acceptance Criteria**

1. An order with one partial fill but no full fill has an unavailable time-to-full-fill value; an order with no fills also has unavailable time-to-first-fill.
2. Timing reports distinguish successful cancellation from too-late/already-ended outcomes; exact elapsed-value verification is blocked on TBD-003.

### 6.12 Administration

#### FR-ADM-001 — Simulation Funding without fabricated execution

**Status:** APPROVED

**Requirement**

The system SHALL permit administrators to allocate approved simulated assets to dedicated liquidity/scenario accounts and SHALL identify these allocations as Simulation Funding. Funding SHALL NOT create a Trade, execution, volume, trading profit, or Last Price; administrators SHALL NOT manufacture direct Trades or forced fills to satisfy orders.

**Business Traceability**

- OQ-003
- BR-SIM-001
- SIM-INV-001
- BUSINESS.md sections 5, 23, 27.

**Acceptance Criteria**

1. Allocating BTC and USDT changes funded balances but creates no execution, volume, trading PnL, or market price.
2. Liquidity appears through submitted ordinary orders, rather than a funding grant being converted into a forced counterparty.

#### FR-ADM-002 — No privileged funded-account trading

**Status:** APPROVED

**Requirement**

The system SHALL apply normal funding, matching priority, and account-level STP to administratively funded accounts, without exemptions or forced execution.

**Business Traceability**

- OQ-003
- OQ-005
- OQ-010
- OQ-011
- BR-ACT-001
- BR-BAL-001
- BR-ORD-001
- BR-ORD-006
- BUSINESS.md sections 4, 27.

**Acceptance Criteria**

1. A later scenario order cannot overtake an earlier same-price trader order because the administrator seeded its assets.
2. An unfunded scenario obligation or same-account match receives no administrative exception.

#### FR-ADM-003 — Setup-only scenario configuration

**Status:** PROVISIONAL

**Requirement**

The system SHALL let the administrator declare starting funds, seeded-asset acquisition references, Maker/Taker rates, scenario action schedules, and participant delays before start. Normal accounts SHALL start with configured USDT and zero Base; scenario accounts MAY receive Base. Base setup quantities SHALL follow symbol steps and USDT allocations 0.00000001 USDT increments. It SHALL NOT allow in-run funding or arbitrary Trade/price edits or forced fills.

**Business Traceability**

- OQ-003
- OQ-016
- OQ-017
- OQ-018
- OQ-033
- OQ-035
- OQ-041
- OQ-042
- OQ-046
- BR-SIM-001
- BR-SIM-002
- BR-FEE-002
- BR-PF-001
- BR-PREC-001
- BR-ALGO-001
- BUSINESS.md sections 5, 25–29, 32.

**Acceptance Criteria**

1. Setup contains explicit amounts, seeded costs, fee rates, and timing assumptions without universal defaults; allocations satisfy the declared numeric grids.
2. Once running, additional funding and changes to fixed rates/delays cannot alter that experiment; a differently configured comparison uses a separate experiment.

#### FR-ADM-004 — Invalid experiment handling

**Status:** PROVISIONAL

**Requirement**

The system SHALL preserve an invalid experiment history with an explicit research-validity label, end it, and use a separate experiment for a fresh run. It SHALL NOT erase invalid history or disguise correction as funding.

**Business Traceability**

- OQ-035
- OQ-042
- OQ-045
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md sections 16, 32.

**Acceptance Criteria**

1. Labelling a run invalid for research preserves its original Trades, prices, fees, and results with that label.
2. A replacement experiment receives new declared starting funds and history; the old Trade records remain unchanged.

## 7. Non-Functional Requirements

These specify business qualities and boundaries. They select no architecture or technology. Numeric business rules already in the source remain distinct from unspecified operational performance targets.

### NFR-DET-001 — Deterministic accepted-order priority

**Status:** APPROVED

**Requirement**

The system SHALL establish a unique authoritative deterministic acceptance sequence for accepted orders and SHALL make equal-price priority explainable from that sequence, independently of client timestamps and order size.

**Business Traceability**

- OQ-010
- BR-ORD-001
- ORD-INV-005
- BUSINESS.md sections 10, 30.

**Acceptance Criteria**

1. For the same accepted order sequence and eligible book, price/sequence selection is repeatable, including equal displayed timestamps.
2. Changing only client timestamps does not change accepted-order queue priority.

### NFR-DET-002 — Deterministic business outcomes

**Status:** PROVISIONAL

**Requirement**

The system SHALL produce consistent Trade boundaries, fee rounding, cancellation/end precedence, and same-time candle ordering for the same effective business action order and configuration. The means of generating that order SHALL remain outside this document.

**Business Traceability**

- OQ-016
- OQ-031
- OQ-035
- OQ-039
- OQ-046
- BR-ORD-007
- BR-FEE-002
- BR-MKT-004
- BR-PREC-001
- BUSINESS.md sections 10, 14, 24, 32.

**Acceptance Criteria**

1. Repeating an eligible pair with the same active quantities produces the same one-Trade quantity and fee rounding.
2. Trade/cancel/end and same-time OHLC outcomes follow the declared authoritative execution order, without a client-time tie-break.

### NFR-COR-001 — Asset and obligation consistency

**Status:** PROVISIONAL

**Requirement**

The system SHALL conserve each simulated asset across participants and Fee Treasury during trading, account separately for Simulation Funding, keep available/locked balances nonnegative, and prevent duplicate settlement or reservation release. Reservation/release alone SHALL change availability rather than total ownership.

**Business Traceability**

- OQ-003
- OQ-005
- OQ-014
- OQ-015
- OQ-016
- OQ-022
- OQ-023
- OQ-024
- OQ-046
- BR-SIM-001
- BR-BAL-001
- BR-BAL-002
- BR-BAL-003
- BR-STL-001
- BR-FEE-001
- BR-FEE-002
- BR-PREC-001
- BAL-INV-001
- BAL-INV-002
- BAL-INV-003
- BAL-INV-004
- STL-INV-001
- STL-INV-002
- STL-INV-003
- BUSINESS.md sections 12, 18, 30.

**Acceptance Criteria**

1. In the source two-Trade example, final USDT is 27,656 buyer + 171,828 seller + 516 treasury = 200,000; final BTC is 2 buyer + 1 seller = 3 BTC.
2. Before/after reserve or release with no Trade/funding, total owned units are unchanged; observing an existing settlement again changes nothing.

### NFR-COR-002 — Exact decimal obligations

**Status:** PROVISIONAL

**Requirement**

The system SHALL express all asset balances in exact 8-decimal units. It SHALL calculate execution notional exactly from valid price and quantity without settlement-notional rounding, floor fees/proportional cost removal to 8 decimals, ceil buy reserves to 8 decimals, and retain residual funds/basis. These rules SHALL NOT select a programming type.

**Business Traceability**

- OQ-017
- OQ-018
- OQ-022
- OQ-033
- OQ-046
- BR-PREC-001
- BR-BAL-002
- BR-PF-001
- BUSINESS.md sections 6, 12, 25, 32.

**Acceptance Criteria**

1. At tick 0.01 USDT/BTC and BTC step 0.000001 BTC, notional 0.00000001 USDT is exact and is neither discarded nor rounded to a larger unit.
2. Full liquidation removes remaining cost residual; unused funding stays owned and release never loses decimal units.

### NFR-COR-003 — Display rounding without financial effect

**Status:** PROVISIONAL

**Requirement**

The system SHALL round displayed derived prices/costs half-up to 8 decimals and displayed percentages half-up to 4 decimals. Display rounding SHALL NOT change Trade prices, quantities, fees, reservations, balances, or cost obligations.

**Business Traceability**

- OQ-034
- OQ-041
- OQ-046
- BR-PREC-001
- BR-PF-001
- BR-ALGO-001
- BUSINESS.md sections 19, 21, 25, 32.

**Acceptance Criteria**

1. Executed notional 260,000 USDT over 3 BTC displays Average Execution Price 86,666.66666667 USDT/BTC.
2. Adverse slippage 500 / 86,000 × 100 displays 0.5814%; the displayed value is not used to recalculate settled obligations.

### NFR-AUD-001 — Explainable experiment outcomes

**Status:** PROVISIONAL

**Requirement**

The system SHALL make participant outcomes explainable through own acceptance priority, actual fills/fees, final state/reason, effective cancellation result, and preserved experiment history. It SHALL keep funding, execution, and research-validity labels distinguishable without exposing prohibited other-account history.

**Business Traceability**

- OQ-025
- OQ-026
- OQ-027
- OQ-031
- OQ-037
- OQ-042
- OQ-045
- BR-ORD-003
- BR-ORD-007
- BR-MKT-002
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md sections 13, 14, 16, 23, 32.

**Acceptance Criteria**

1. A cancelled partly filled order can be explained using its cumulative fills, termination reason, and released unused funds without claiming that earlier Trades were reversed.
2. An invalid-for-research run retains its original execution facts and explicit validity label. Retention duration/access details remain TBD-005.

### NFR-REP-001 — Reproducible synthetic experiments

**Status:** PROVISIONAL

**Requirement**

The system SHALL support reproduction of synthetic trading outcomes when the scenario, funding, rates, action schedule, timing assumptions, participant decisions, and authoritative effective action order are the same. Changed participant decisions or timing SHALL be identified as changed inputs; reproducibility SHALL NOT claim historical or real-market profitability.

**Business Traceability**

- OQ-010
- OQ-016
- OQ-031
- OQ-035
- OQ-040
- OQ-041
- OQ-042
- OQ-046
- BR-ORD-001
- BR-ORD-007
- BR-FEE-002
- BR-SIM-002
- BR-ALGO-001
- BR-PREC-001
- BUSINESS.md sections 28, 29, 32.

**Acceptance Criteria**

1. Two separate runs with identical declared inputs and effective action order can be compared for equal fills, prices, fees, end balances, and Trade-derived summaries.
2. A run with a changed strategy or delay is not described as an identical-input reproduction. Same-time schedule ordering details remain TBD-002.

### NFR-SEC-001 — Business ownership and disclosure boundaries

**Status:** PROVISIONAL

**Requirement**

The system SHALL enforce own-account cancellation, isolated participant capital, and privacy of other-account identities/individual histories in participant observations. It SHALL NOT infer a technical identity or access-control design from the business actor descriptions.

**Business Traceability**

- OQ-031
- OQ-037
- OQ-043
- BR-ORD-007
- BR-MKT-002
- BR-ACT-002
- BUSINESS.md sections 4, 14, 23.

**Acceptance Criteria**

1. A participant cannot cancel another account order, spend its capital, or obtain its individual order history through own-account observations.
2. Public Trade observations remain anonymous; registration, administrator visibility, and detailed permissions remain TBD-004.

### NFR-SEC-002 — Administrative business limits

**Status:** PROVISIONAL

**Requirement**

The system SHALL constrain administrative scenario authority so it cannot manufacture execution, bypass ordinary trading rules, add in-run funding, or rewrite Trade history. Invalid outcomes SHALL remain labeled historical facts rather than administratively corrected Trades.

**Business Traceability**

- OQ-003
- OQ-042
- OQ-045
- BR-SIM-001
- BR-SIM-002
- BR-TRD-001
- BUSINESS.md sections 4, 16, 27, 32.

**Acceptance Criteria**

1. Administrative funding produces no Trade or price and cannot repair an earlier Trade by disguising a correction.
2. A running experiment rejects funding/configuration changes prohibited by its business rules while preserving its existing history.

### NFR-PERF-001 — Performance and capacity targets remain TBD

**Status:** PROVISIONAL

**Requirement**

The system SHALL distinguish virtual observation/action delays and experiment outcomes from measured platform performance. Quantitative response-time, throughput, capacity, concurrent-participant, availability, and recovery targets SHALL remain TBD pending an owner-defined workload and acceptance decision; V1 SHALL NOT claim production-HFT latency or real-network/hardware benchmark results.

**Business Traceability**

- OQ-041
- BR-ALGO-001
- BUSINESS.md sections 29.

**Acceptance Criteria**

1. No numeric operational performance threshold is asserted in this specification; the 60-second staleness rule and one-minute candles are business semantics, not platform performance targets.
2. Performance acceptance is explicitly blocked on TBD-006; a simulated action delay alone is not evidence of real execution speed.

## 8. Use Cases

These concise flows compose the requirements above. Status applies to the complete flow, including alternative outcomes.

### UC-001 — Submit Limit Buy

**Status:** PROVISIONAL

**Primary Actor:** Human Trader (strategy/scenario participation is governed separately)

**Preconditions:** Account has available USDT sufficient for the full fee-inclusive reservation; experiment is running and request satisfies symbol, price, and quantity rules.

**Trigger:** Participant submits a Base quantity and BUY Limit Price.

**Main Flow**

1. Validate the request and full funding before acceptance or STP; reserve the required USDT and establish acceptance priority.
2. Match eligible Asks by price then acceptance sequence, applying STP and resting-order execution prices.
3. Settle each Trade and recalculate/release surplus; rest any GTC remainder with the original sequence.

**Alternative / Failure Flows:** Invalid/underfunded request is REJECTED without effects; no eligible Ask leaves OPEN; partial execution with a remainder leaves PARTIALLY_FILLED; complete execution leaves FILLED.

**Postconditions:** Actual fills, fees, balances, and an optional funded resting remainder reflect the outcome.

**Requirements:** FR-ORD-002, FR-ORD-003, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-004, FR-BAL-007, FR-TRD-002, FR-SIM-001.

**Business Traceability**

- OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-013, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-002 — Submit Limit Sell

**Status:** PROVISIONAL

**Primary Actor:** Human Trader

**Preconditions:** Participant owns sufficient available Base; experiment is running; requested price and quantity are valid.

**Trigger:** Participant submits a Base quantity and SELL Limit Price.

**Main Flow**

1. Validate and reserve the full Base quantity, then accept with authoritative priority.
2. Match eligible Bids, applying price/sequence priority, STP, and resting prices.
3. Receive net USDT proceeds per Trade; keep any GTC remainder funded by remaining Base.

**Alternative / Failure Flows:** Insufficient Base or invalid input rejects before effects; no match rests OPEN; partial/full outcomes follow the lifecycle.

**Postconditions:** Executed Base is sold, net proceeds available, and any unexecuted active Base remains reserved.

**Requirements:** FR-ORD-002, FR-ORD-003, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-006, FR-TRD-002, FR-SIM-001.

**Business Traceability**

- OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-003 — Submit protected Market Buy

**Status:** PROVISIONAL

**Primary Actor:** Human Trader

**Preconditions:** Participant supplies Base quantity and absolute maximum price; full protected fee-inclusive funding is available in a running experiment.

**Trigger:** Participant submits the protected BUY request.

**Main Flow**

1. Validate request and full protected reservation before acceptance or STP.
2. Immediately consume eligible Asks no higher than the maximum with ordinary priority/STP and per-Trade settlement.
3. End any remainder and release unused reserve; do not place Market interest on the book.

**Alternative / Failure Flows:** Invalid boundary/quantity or insufficient funds gives REJECTED; partial or zero execution ends CANCELLED/MARKET_REMAINDER; full execution gives FILLED.

**Postconditions:** Only actual fills and fees remain; no active Market remainder or unused reserve remains.

**Requirements:** FR-ORD-001, FR-ORD-004, FR-ORD-005, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-ORD-015, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-005, FR-BAL-008, FR-TRD-002, FR-SIM-001.

**Business Traceability**

- OQ-004, OQ-006, OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-031, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-ORD-007, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-004 — Submit protected Market Sell

**Status:** PROVISIONAL

**Primary Actor:** Human Trader

**Preconditions:** Participant owns the requested available Base and specifies an absolute minimum; experiment is running.

**Trigger:** Participant submits the protected SELL request.

**Main Flow**

1. Validate and reserve the full Base quantity before acceptance or STP.
2. Immediately match eligible Bids no lower than the minimum, applying ordinary priority/STP.
3. Settle fills with seller fees deducted from proceeds; terminate and release any unused Base remainder.

**Alternative / Failure Flows:** Invalid request/insufficient Base is REJECTED; zero/partial fill ends CANCELLED/MARKET_REMAINDER without reversing fills; complete execution is FILLED.

**Postconditions:** Actual net sale proceeds are available; no Market remainder rests.

**Requirements:** FR-ORD-001, FR-ORD-004, FR-ORD-006, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-ORD-015, FR-MAT-001, FR-MAT-002, FR-MAT-003, FR-MAT-004, FR-BAL-003, FR-BAL-006, FR-BAL-008, FR-TRD-002, FR-SIM-001.

**Business Traceability**

- OQ-004, OQ-006, OQ-007, OQ-008, OQ-009, OQ-010, OQ-011, OQ-012, OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-023, OQ-024, OQ-025, OQ-026, OQ-027, OQ-031, OQ-035, OQ-046
- BR-ACT-001, BR-BAL-002, BR-BAL-003, BR-BAL-004, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-002, BR-ORD-003, BR-ORD-004, BR-ORD-005, BR-ORD-006, BR-ORD-007, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-005 — Partial Fill

**Status:** APPROVED

**Primary Actor:** Trader

**Preconditions:** A valid funded Limit or protected Market Order has less immediately eligible counterparty quantity than requested.

**Trigger:** The incoming order consumes available eligible interest.

**Main Flow**

1. Execute eligible quantity and preserve the resulting Trades.
2. For Limit, retain the GTC remainder with original priority; for Market, end the remainder immediately.

**Alternative / Failure Flows:** Zero liquidity yields no Trade; a Limit may rest while a Market ends; protection may restrict eligible quantity.

**Postconditions:** Completed fills stand and neither order type assumes a complete fill. This use case deliberately leaves proposed lifecycle labels and settlement mechanics to their own requirements.

**Requirements:** FR-ORD-003, FR-ORD-004, FR-ORD-005, FR-ORD-006, FR-MAT-002, FR-MAT-003.

**Business Traceability**

- OQ-006, OQ-007, OQ-008, OQ-009, OQ-010, OQ-012
- BR-ORD-001, BR-ORD-002, BR-ORD-004, BR-ORD-005

### UC-006 — Self-Trade Prevention

**Status:** APPROVED

**Primary Actor:** Exchange / Trading Platform

**Preconditions:** An accepted funded incoming order reaches its next eligible resting counterparty, owned by the same trading account.

**Trigger:** The platform compares the two trading-account identities.

**Main Flow**

1. Cancel the self-owned resting active remainder, preserving its earlier fills and distinguishing STP from user cancellation.
2. Create no self-Trade, volume, Last Price update, or fee.
3. Continue the incoming order against subsequent eligible interest.

**Alternative / Failure Flows:** A distinct account is not grouped as self-owned by human owner; if no other eligible interest remains, follow the incoming type remainder rule.

**Postconditions:** The self-owned resting remainder is gone and only actual other-account Trades have execution effects.

**Requirements:** FR-MAT-004, FR-MAT-005, FR-ORD-003, FR-ORD-004.

**Business Traceability**

- OQ-006, OQ-007, OQ-011, OQ-012
- BR-ACT-001, BR-ORD-002, BR-ORD-004, BR-ORD-006

### UC-007 — Cancel active Limit Order

**Status:** PROVISIONAL

**Primary Actor:** Order-owning participant

**Preconditions:** The participant has an active own-account Limit remainder.

**Trigger:** The participant requests cancellation.

**Main Flow**

1. Determine cancellation effectiveness in authoritative order; an outstanding request alone is not success.
2. End only the active remainder and record CANCELLED/USER_REQUEST.
3. Release unused reservation while preserving all earlier Trades and fees.

**Alternative / Failure Flows:** Earlier fills stand; full fill gives too late/already filled; prior termination gives already ended; nonexistent/rejected or other-account orders cannot be cancelled by this flow.

**Postconditions:** No future fill of the effectively cancelled remainder is possible and no repeated release occurs.

**Requirements:** FR-ORD-014, FR-ORD-015, FR-ORD-016, FR-ORD-020, FR-MAT-007, FR-BAL-008.

**Business Traceability**

- OQ-010, OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-026, OQ-027, OQ-028, OQ-031, OQ-035, OQ-046
- BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-ORD-001, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-SIM-002, BR-STL-001

### UC-008 — Reject underfunded/invalid Order

**Status:** PROVISIONAL

**Primary Actor:** Submitting participant

**Preconditions:** A proposed order lacks full available funds or violates a V1 type, symbol, protection, price, or quantity rule.

**Trigger:** The system validates the submitted request.

**Main Flow**

1. Check the applicable business rules and full funding before acceptance or STP effects.
2. Return REJECTED without Trades, prior-order cancellation, silent rounding, or resizing.

**Alternative / Failure Flows:** If the request passes all checks it proceeds to the applicable submission use case; expected later STP release does not cure current underfunding.

**Postconditions:** No accepted order or execution effect arises from the rejected request; existing commitments remain unchanged.

**Requirements:** FR-ORD-001, FR-ORD-005, FR-ORD-006, FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-ORD-012, FR-BAL-003, FR-MKT-001.

**Business Traceability**

- OQ-001, OQ-002, OQ-004, OQ-006, OQ-008, OQ-017, OQ-018, OQ-019, OQ-020, OQ-021, OQ-022, OQ-025, OQ-026, OQ-027
- BR-BAL-002, BR-BAL-004, BR-MKT-003, BR-ORD-003, BR-ORD-004, BR-PREC-001

### UC-009 — Prepare simulation scenario

**Status:** PROVISIONAL

**Primary Actor:** Administrator

**Preconditions:** A separate experiment is in setup; explicit scenario inputs are available.

**Trigger:** Administrator prepares starting capital, rates, scenario actions, and timing assumptions.

**Main Flow**

1. Declare scenario date, funding amounts, seeded acquisition costs, rates, and participant delays.
2. Allocate normal USDT-only capital and dedicated scenario assets on valid grids, separately identifying Simulation Funding.
3. Declare funded scenario Limit Orders for submission in scenario action order while running; retain an empty book until orders are submitted.

**Alternative / Failure Flows:** Invalid amounts/rates are not accepted; in-run funding is unavailable; missing scenario values must be supplied rather than invented.

**Postconditions:** The scenario has declared inputs with no fabricated Trades, volume, Last Price, or profit; start/control details remain TBD-001.

**Requirements:** FR-ACT-002, FR-ACT-003, FR-SIM-001, FR-SIM-004, FR-FEE-005, FR-PF-003, FR-HFT-001, FR-ADM-001, FR-ADM-002, FR-ADM-003.

**Business Traceability**

- OQ-003, OQ-005, OQ-010, OQ-011, OQ-016, OQ-017, OQ-018, OQ-031, OQ-033, OQ-035, OQ-036, OQ-041, OQ-042, OQ-043, OQ-046
- BR-ACT-001, BR-ACT-002, BR-ALGO-001, BR-BAL-001, BR-FEE-002, BR-ORD-001, BR-ORD-006, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-001, BR-SIM-002

### UC-010 — Run algorithmic strategy

**Status:** PROVISIONAL

**Primary Actor:** Algorithmic Strategy

**Preconditions:** Running experiment has assigned separate capital, simulated observations, fixed delays, and declared strategy decisions/inputs.

**Trigger:** The strategy decides to submit, cancel, or do nothing.

**Main Flow**

1. Use the information observed at decision time to select an ordinary V1 action.
2. Apply action delay before authoritative ordering; apply the same funded matching and STP rules as other participants.
3. Observe actual fills, fees, own queue, cancellation results, and supported execution-quality/PnL measures.

**Alternative / Failure Flows:** An action may reject, partially fill, end without fills, or cancel too late; missing references/fills yield unavailable respective metrics, not invented success.

**Postconditions:** Strategy results reflect actual simulated outcomes; a rerun with changed strategy/delay is a changed experiment input.

**Requirements:** FR-ACT-003, FR-ALGO-001, FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, FR-MAT-007, FR-PF-004.

**Business Traceability**

- OQ-004, OQ-005, OQ-008, OQ-010, OQ-011, OQ-013, OQ-014, OQ-015, OQ-016, OQ-026, OQ-031, OQ-033, OQ-034, OQ-035, OQ-037, OQ-040, OQ-041, OQ-042, OQ-043, OQ-044, OQ-046
- BR-ACT-002, BR-ALGO-001, BR-BAL-001, BR-FEE-001, BR-FEE-002, BR-MKT-001, BR-MKT-002, BR-ORD-001, BR-ORD-003, BR-ORD-004, BR-ORD-006, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-002

### UC-011 — End simulation experiment

**Status:** PROVISIONAL

**Primary Actor:** Exchange / Trading Platform processes the end; the actor authorized to initiate it and end trigger remain TBD-001

**Preconditions:** An experiment exists with historical activity and possibly active remainders.

**Trigger:** Experiment end becomes effective in authoritative business ordering.

**Main Flow**

1. Complete business actions that precede end under the defined precedence.
2. Cancel all active remainders with EXPERIMENT_ENDED and release unused reservations.
3. Preserve balances, Trades, fees, and history, and label any final partial candle.

**Alternative / Failure Flows:** Previously filled/ended orders have no active remainder to stop; invalid history is retained with a validity label; reset starts a separate experiment without asset transfer.

**Postconditions:** No active remainder survives end and earlier execution is not reversed.

**Requirements:** FR-SIM-002, FR-SIM-003, FR-MAT-007, FR-MKT-009, FR-TRD-004, FR-ADM-004.

**Business Traceability**

- OQ-010, OQ-023, OQ-027, OQ-030, OQ-031, OQ-035, OQ-039, OQ-042, OQ-044, OQ-045
- BR-BAL-003, BR-MKT-001, BR-MKT-004, BR-ORD-001, BR-ORD-003, BR-ORD-007, BR-SIM-002, BR-TRD-001

## 9. Acceptance Scenarios

All cases are specification-level acceptance scenarios, not executed tests. Approved scenarios avoid proposed state labels and mechanics. Provisional fixtures do not approve their proposed rules. Local criteria in sections 6–7 remain required even when no composed scenario repeats them.

### AS-001 — Better price precedes earlier worse price

**Status:** APPROVED

**Requirements:** FR-MAT-001, FR-ORD-002.

- **Given** an earlier Ask for 1 BTC at 86,000 and a later Ask for 1 BTC at 85,000 USDT/BTC, from different eligible accounts.
- **When** a funded BUY LIMIT for 1 BTC at 86,000 USDT/BTC arrives.
- **Then** the 85,000 Ask executes first; for SELL, a Bid at 85,500 similarly precedes a Bid at 85,000 USDT/BTC.

**Business Traceability:** OQ-009, OQ-010, OQ-012, BR-ORD-001, BR-ORD-002, BR-ORD-005.

### AS-002 — Same-price acceptance order

**Status:** APPROVED

**Requirements:** FR-MAT-002, NFR-DET-001.

- **Given** Ask A for 0.4 BTC at sequence #1001 and Ask B for 2 BTC at #1002, both 86,000 USDT/BTC.
- **When** a funded 1 BTC BUY eligible at that price arrives.
- **Then** A fills 0.4 BTC first, B fills 0.6 BTC, and B retains its sequence for the remaining 1.4 BTC.

**Business Traceability:** OQ-010, BR-ORD-001.

### AS-003 — Client timestamp cannot move priority

**Status:** APPROVED

**Requirements:** FR-MAT-002, NFR-DET-001.

- **Given** the same queue as AS-002, with B carrying an earlier client timestamp or both orders having the same displayed time.
- **When** priority is evaluated.
- **Then** A remains first because #1001 precedes #1002; neither client time nor displayed-time equality changes it.

**Business Traceability:** OQ-010, BR-ORD-001.

### AS-004 — Larger size cannot jump the queue

**Status:** APPROVED

**Requirements:** FR-MAT-002.

- **Given** an earlier 0.4 BTC Ask and a later 2 BTC Ask at the same price.
- **When** eligible incoming demand is allocated.
- **Then** the earlier 0.4 BTC executes first; the larger later order cannot overtake it.

**Business Traceability:** OQ-010, BR-ORD-001.

### AS-005 — Execution price follows the resting side

**Status:** APPROVED

**Requirements:** FR-MAT-003.

- **Given** a resting BUY at 90,000 USDT/BTC and an incoming SELL with limit 86,000 USDT/BTC.
- **When** they match.
- **Then** execution is at 90,000 USDT/BTC; in a separate case with the SELL resting at 86,000 and BUY incoming at 90,000, execution is at 86,000.

**Business Traceability:** OQ-008, OQ-009, BR-ORD-004, BR-ORD-005.

### AS-006 — Limit partial fill rests as GTC

**Status:** APPROVED

**Requirements:** FR-ORD-002, FR-ORD-003, FR-MAT-002.

- **Given** a funded BUY LIMIT for 10 BTC at 90,000 USDT/BTC and only 4 BTC immediately eligible at a resting Ask of 86,000.
- **When** the immediate match completes.
- **Then** 4 BTC remains executed, 6 BTC rests at 90,000 with the original acceptance sequence, and no future fill is guaranteed.

**Business Traceability:** OQ-007, OQ-009, OQ-010, OQ-012, BR-ORD-001, BR-ORD-002, BR-ORD-005.

### AS-007 — Market partial and zero fills terminate

**Status:** APPROVED

**Requirements:** FR-ORD-004.

- **Given** a funded Market Buy for 5 BTC and only 3 BTC immediately eligible within its protection.
- **When** matching completes.
- **Then** 3 BTC remains executed, 2 BTC terminates without resting; a separate zero-eligible-liquidity case creates no Trade and ends all 5 BTC.

**Business Traceability:** OQ-006, OQ-007, OQ-012, BR-ORD-002, BR-ORD-004.

### AS-008 — Market Buy absolute maximum

**Status:** APPROVED

**Requirements:** FR-ORD-005, FR-ORD-004, FR-MAT-003.

- **Given** a funded Market Buy for 2 BTC capped at 86,000 USDT/BTC, with Asks of 1 BTC at 86,000 and 2 BTC at 87,000.
- **When** matching applies the maximum.
- **Then** only 1 BTC executes at 86,000 and the other 1 BTC ends; fees cannot authorize a higher unit price.

**Business Traceability:** OQ-006, OQ-007, OQ-008, OQ-009, OQ-012, BR-ORD-002, BR-ORD-004, BR-ORD-005.

### AS-009 — Market Sell absolute minimum

**Status:** APPROVED

**Requirements:** FR-ORD-006, FR-ORD-004, FR-MAT-003.

- **Given** a funded Market Sell for 3 BTC with minimum 80,000 USDT/BTC, and Bids of 1 BTC each at 82,000, 80,000, and 79,000.
- **When** matching applies the minimum.
- **Then** 1 BTC executes at each of 82,000 and 80,000; the final 1 BTC ends without execution at 79,000.

**Business Traceability:** OQ-006, OQ-007, OQ-008, OQ-009, OQ-012, BR-ORD-002, BR-ORD-004, BR-ORD-005.

### AS-010 — STP cancels resting self-interest and continues

**Status:** APPROVED

**Requirements:** FR-MAT-004.

- **Given** account A incoming BUY encounters A eligible resting SELL remainder of 0.4 BTC, followed by B eligible SELL for 0.6 BTC.
- **When** matching reaches A self-owned resting order.
- **Then** A resting remainder is cancelled distinctly as STP, its earlier fills stand, and the incoming order continues to the 0.6 BTC counterparty B.

**Business Traceability:** OQ-011, BR-ACT-001, BR-ORD-006.

### AS-011 — STP has no market execution effects

**Status:** APPROVED

**Requirements:** FR-MAT-005.

- **Given** only a same-account eligible match, with recorded Trade count, volume, Last Price, and fees.
- **When** STP cancels that resting remainder.
- **Then** those execution measures remain unchanged; depth may decrease, but no self-Trade, volume, Last Price update, or fee is created.

**Business Traceability:** OQ-011, BR-ORD-006.

### AS-012 — Funding is capital rather than trading activity

**Status:** APPROVED

**Requirements:** FR-ACT-002, FR-ADM-001, FR-ADM-002.

- **Given** an administrator selects initial simulated funds and seeds a scenario account.
- **When** Simulation Funding is recorded.
- **Then** only funded capital changes; no Trade, execution, volume, profit, or Last Price is created, and subsequent scenario orders have ordinary funding/priority/STP rules.

**Business Traceability:** OQ-003, OQ-005, OQ-010, OQ-011, BR-ACT-001, BR-BAL-001, BR-ORD-001, BR-ORD-006, BR-SIM-001.

### AS-013 — One order takes then makes

**Status:** APPROVED

**Requirements:** FR-FEE-001, FR-FEE-002, FR-ORD-003, FR-MAT-003.

- **Given** a resting SELL for 1 BTC at 86,000 and a funded incoming BUY LIMIT for 2 BTC at 90,000 USDT/BTC.
- **When** the BUY consumes the Ask, rests its remainder, then a later eligible SELL matches it.
- **Then** the BUY is Taker on the 86,000 Trade and Maker on the 90,000 Trade; the role belongs to each Trade.

**Business Traceability:** OQ-007, OQ-008, OQ-009, OQ-012, OQ-013, OQ-014, BR-FEE-001, BR-ORD-002, BR-ORD-004, BR-ORD-005.

### AS-014 — Reject underfunding before STP

**Status:** PROVISIONAL

**Requirements:** FR-BAL-003, FR-BAL-005.

- **Given** a 2 BTC Market Buy capped at 90,000 USDT/BTC, taker fee 0.2%, only 180,359 USDT available, and an otherwise eligible self-owned resting SELL with reserved BTC.
- **When** funding validation runs.
- **Then** the 180,360 USDT full requirement fails; request is REJECTED, creates no fills, is not resized, and does not cancel the self-owned resting order.

**Business Traceability:** OQ-008, OQ-015, OQ-016, OQ-021, OQ-022, OQ-027, OQ-046, BR-BAL-002, BR-BAL-004, BR-ORD-003, BR-PREC-001.

### AS-015 — Reservation recalculation and release

**Status:** PROVISIONAL

**Requirements:** FR-BAL-004, FR-BAL-007, FR-BAL-008.

- **Given** 100,000 USDT initial balance, BUY LIMIT 1 BTC at 80,000 USDT/BTC, maker 0.1%, taker 0.2%.
- **When** 0.4 BTC executes against a 75,000 USDT/BTC Ask, then the remaining 0.6 BTC is cancelled.
- **Then** initial reserve 80,160; execution cost 30,060; reserve after fill 48,096; surplus release 2,004; after cancellation 69,940 USDT is available and 0.4 BTC remains owned.

**Business Traceability:** OQ-013, OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-027, OQ-031, OQ-046, BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-STL-001.

### AS-016 — Cancellation preserves past fills

**Status:** PROVISIONAL

**Requirements:** FR-ORD-020, FR-ORD-016, FR-BAL-008.

- **Given** an original 1 BTC order with 0.4 BTC already executed.
- **When** effective cancellation ends its 0.6 BTC active remainder.
- **Then** filled stays 0.4, unexecuted 0.6, active remaining becomes 0 BTC, and only unused funds are released; the execution and its fee remain.

**Business Traceability:** OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-027, OQ-028, OQ-031, OQ-046, BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-STL-001.

### AS-017 — Terminal orders never reactivate

**Status:** PROVISIONAL

**Requirements:** FR-ORD-012, FR-ORD-015, FR-ORD-016.

- **Given** orders in FILLED, CANCELLED, or REJECTED.
- **When** later activity or another cancellation references them.
- **Then** none returns to OPEN/PARTIALLY_FILLED; active remaining stays zero; a valid zero-fill Market outcome is CANCELLED/MARKET_REMAINDER rather than REJECTED.

**Business Traceability:** OQ-025, OQ-026, OQ-027, OQ-028, OQ-035, BR-ORD-003, BR-ORD-007, BR-SIM-002.

### AS-018 — Settle and accumulate each Trade once

**Status:** PROVISIONAL

**Requirements:** FR-TRD-002, FR-TRD-003, FR-FEE-002, FR-FEE-004, NFR-COR-001.

- **Given** a 1 BTC Trade at 86,000 USDT/BTC with buyer Taker 0.2%, seller Maker 0.1%.
- **When** settlement occurs and that same Trade is later reported again.
- **Then** buyer pays 86,172 USDT, seller receives 85,914, treasury receives 258, and 1 BTC changes ownership once; repeated reporting changes no balances, fees, or filled totals.

**Business Traceability:** OQ-003, OQ-005, OQ-014, OQ-015, OQ-016, OQ-022, OQ-023, OQ-024, OQ-046, BR-BAL-001, BR-BAL-002, BR-BAL-003, BR-FEE-001, BR-FEE-002, BR-PREC-001, BR-SIM-001, BR-STL-001.

### AS-019 — Market summaries use actual Trades once

**Status:** PROVISIONAL

**Requirements:** FR-MKT-006, FR-MKT-007, FR-MKT-008.

- **Given** four Trades in one minute at 86,000/88,000/85,000/87,000 USDT/BTC for 1/0.5/2/0.5 BTC, plus funding, cancellations, and STP events.
- **When** market summaries are produced.
- **Then** Last Price is 87,000; Base volume is 4 BTC; gross Quote volume is 343,500 USDT; only the four Trades contribute OHLCV and fees do not inflate volume.

**Business Traceability:** OQ-003, OQ-011, OQ-035, OQ-038, OQ-039, OQ-044, BR-MKT-001, BR-MKT-004, BR-ORD-006, BR-SIM-001.

### AS-020 — Empty candles do not fabricate prices

**Status:** PROVISIONAL

**Requirements:** FR-MKT-006, FR-MKT-009.

- **Given** a minute without actual Trades following a minute whose latest Trade is 87,000 USDT/BTC.
- **When** the empty candle is reported.
- **Then** OHLC is unavailable and both volumes are zero, while separate Last Price may still be 87,000; no price is carried into the candle.

**Business Traceability:** OQ-003, OQ-011, OQ-035, OQ-039, OQ-044, BR-MKT-001, BR-MKT-004, BR-ORD-006, BR-SIM-001.

### AS-021 — Administrators cannot rewrite Trades

**Status:** PROVISIONAL

**Requirements:** FR-TRD-004, FR-ADM-004, NFR-SEC-002.

- **Given** an experiment with confirmed Trades later deemed invalid for research.
- **When** an administrator seeks to correct its trading results.
- **Then** Trade edit/delete/reversal and funding-as-correction are unavailable; the original history is retained with an explicit validity label and a fresh run uses a separate experiment.

**Business Traceability:** OQ-003, OQ-035, OQ-042, OQ-045, BR-SIM-001, BR-SIM-002, BR-TRD-001.

### AS-022 — Experiment end preserves executed history

**Status:** PROVISIONAL

**Requirements:** FR-SIM-002, FR-SIM-003.

- **Given** a partly filled GTC Limit with an active remainder and unused reserve.
- **When** experiment end becomes effective in authoritative order.
- **Then** remainder becomes CANCELLED/EXPERIMENT_ENDED, active quantity is zero, unused reserve is released, and historical Trades, fees, and resulting balances remain; a reset uses a separate history.

**Business Traceability:** OQ-023, OQ-027, OQ-030, OQ-031, OQ-035, OQ-042, OQ-045, BR-BAL-003, BR-ORD-003, BR-ORD-007, BR-SIM-002, BR-TRD-001.

### AS-023 — Exact grids and order bounds

**Status:** PROVISIONAL

**Requirements:** FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011.

- **Given** otherwise valid and funded requests.
- **When** price and quantity validation is applied.
- **Then** inclusive tick/quantity boundaries pass their respective checks; off-grid, below-minimum, and above-maximum values fail without rounding/resizing; no extra notional or fill-size threshold appears.

**Business Traceability:** OQ-017, OQ-018, OQ-019, OQ-020, BR-PREC-001.

### AS-024 — Fee precision and bounded configuration

**Status:** PROVISIONAL

**Requirements:** FR-FEE-003, FR-FEE-004, FR-FEE-005, NFR-COR-002.

- **Given** explicit maker/taker rates and a smallest valid BTC notional of 0.00000001 USDT.
- **When** a 0.01% rate is applied per Trade/side.
- **Then** the exact 0.000000000001 USDT fee floors to zero without a minimum; invalid rates fail setup and fixed rates cannot change in-run.

**Business Traceability:** OQ-014, OQ-015, OQ-016, OQ-017, OQ-018, OQ-022, OQ-033, OQ-042, OQ-046, BR-BAL-002, BR-FEE-002, BR-PF-001, BR-PREC-001, BR-SIM-002.

### AS-025 — Authoritative action precedence and replacement

**Status:** PROVISIONAL

**Requirements:** FR-MAT-006, FR-MAT-007, FR-ORD-014, FR-ORD-018, FR-ORD-020, NFR-DET-002.

- **Given** an incoming match, an active-order cancel, and a later replacement request.
- **When** actions take effect in their authoritative order.
- **Then** one incoming immediate outcome completes before the next action; an earlier fill stands and earlier cancellation prevents a later fill of that remainder; replacement receives fresh checks/priority and failure does not restore the old order.

**Business Traceability:** OQ-010, OQ-016, OQ-021, OQ-023, OQ-026, OQ-027, OQ-028, OQ-029, OQ-031, OQ-035, OQ-039, OQ-046, BR-BAL-003, BR-BAL-004, BR-FEE-002, BR-MKT-004, BR-ORD-001, BR-ORD-003, BR-ORD-007, BR-PREC-001, BR-SIM-002.

### AS-026 — Weighted basis, fees, and unpriced capital

**Status:** PROVISIONAL

**Requirements:** FR-PF-001, FR-PF-002, FR-PF-003, FR-PF-004.

- **Given** the source zero-fee purchases of 1 BTC at 80,000 and 1 BTC at 90,000 USDT/BTC.
- **When** 0.5 BTC sells at 88,000 and Last Price is later 87,000 USDT/BTC.
- **Then** realized PnL is 1,500, remaining basis 127,500, and unrealized PnL 3,000 USDT; in a separate seeded-only no-Trade case Base valuation/equity is unavailable and capital is not profit.

**Business Traceability:** OQ-003, OQ-015, OQ-016, OQ-017, OQ-018, OQ-024, OQ-033, OQ-034, OQ-042, OQ-044, OQ-046, BR-FEE-002, BR-MKT-001, BR-PF-001, BR-PREC-001, BR-SIM-001, BR-SIM-002, BR-STL-001.

### AS-027 — Anonymous depth and own queue

**Status:** PROVISIONAL

**Requirements:** FR-ACT-003, FR-MKT-002, FR-MKT-003, FR-MKT-004, FR-MKT-005, NFR-SEC-001.

- **Given** two earlier same-price orders of 0.4 and 0.6 BTC ahead of a participant own order.
- **When** the participant observes the book and attempts to act on another account.
- **Then** queue ahead is two orders/1 BTC, depth is aggregated, other-account identities/history remain private, and other-account capital/cancellation is unavailable; an absent side makes its best quote and Spread unavailable.

**Business Traceability:** OQ-010, OQ-027, OQ-031, OQ-037, OQ-043, OQ-044, BR-ACT-002, BR-MKT-001, BR-MKT-002, BR-ORD-001, BR-ORD-003, BR-ORD-007.

### AS-028 — Minute boundaries and partial final interval

**Status:** PROVISIONAL

**Requirements:** FR-SIM-001, FR-MKT-008, FR-MKT-009.

- **Given** Trades at 10:00:59 and exactly 10:01:00 on the experiment clock, including multiple same-time Trades.
- **When** the experiment ends during the 10:01 minute.
- **Then** the boundary Trade belongs to 10:01, same-time Open/Close follows execution order, and the final incomplete interval is retained and labeled.

**Business Traceability:** OQ-035, OQ-038, OQ-039, OQ-044, BR-MKT-001, BR-MKT-004, BR-SIM-002.

### AS-029 — Synthetic strategy and delayed execution metrics

**Status:** PROVISIONAL

**Requirements:** FR-ALGO-001, FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, NFR-REP-001, NFR-COR-003.

- **Given** a strategy with declared fixed delays sees Ask 86,000 USDT/BTC and later buys 2 BTC at average 86,500.
- **When** its synthetic outcomes are reported and a separate controlled rerun is prepared.
- **Then** adverse slippage is 500 USDT/BTC or 0.5814%; fees and actual fills determine costs; unachieved fill timing is unavailable; changed delays are changed inputs, with timing origins still TBD-003.

**Business Traceability:** OQ-004, OQ-005, OQ-008, OQ-010, OQ-011, OQ-013, OQ-014, OQ-015, OQ-016, OQ-026, OQ-031, OQ-033, OQ-034, OQ-035, OQ-037, OQ-040, OQ-041, OQ-042, OQ-043, OQ-046, BR-ACT-002, BR-ALGO-001, BR-BAL-001, BR-FEE-001, BR-FEE-002, BR-MKT-002, BR-ORD-001, BR-ORD-003, BR-ORD-004, BR-ORD-006, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-002.

### AS-030 — Setup controls and finite scenario inventory

**Status:** PROVISIONAL

**Requirements:** FR-SIM-004, FR-ADM-003, FR-FEE-005, FR-ORD-017, FR-ORD-019.

- **Given** a configured scenario with funded accounts and predefined Limit Orders.
- **When** the experiment starts, inventory is consumed, and the administrator attempts in-run funding or an amendment.
- **Then** predefined funded scenario Limit Orders establish initial scenario depth while ordinary participant GTC remainders can also add depth; inventory does not refill automatically; prohibited funding/amendment is unavailable; remaining GTC orders do not expire merely as time advances.

**Business Traceability:** OQ-003, OQ-005, OQ-010, OQ-011, OQ-012, OQ-016, OQ-017, OQ-018, OQ-027, OQ-028, OQ-030, OQ-033, OQ-035, OQ-036, OQ-041, OQ-042, OQ-043, OQ-046, BR-ACT-001, BR-ACT-002, BR-ALGO-001, BR-FEE-002, BR-ORD-002, BR-ORD-003, BR-ORD-007, BR-PF-001, BR-PREC-001, BR-SIM-001, BR-SIM-002.

### AS-031 — V1 scope and claims

**Status:** PROVISIONAL

**Requirements:** FR-ACT-001, FR-MKT-001, FR-ORD-001, FR-ORD-007, FR-BAL-001, FR-ALGO-002, NFR-PERF-001.

- **Given** the V1 capability description.
- **When** scope and performance claims are reviewed.
- **Then** only the three simulated funded spot markets and Limit/protected Market types are offered; advanced instructions and real-market integrations stay deferred, and numerical operational performance acceptance stays TBD.

**Business Traceability:** OQ-001, OQ-002, OQ-004, OQ-005, OQ-006, OQ-012, OQ-013, OQ-035, OQ-040, OQ-041, OQ-042, BR-ALGO-001, BR-BAL-001, BR-FEE-001, BR-MKT-003, BR-ORD-002, BR-ORD-004, BR-SIM-002.

## 10. Business Invariants

The meanings below reproduce BUSINESS.md section 30 without changing identifiers. The final column identifies the requirement status boundary, not a new approval of the invariant. An approved domain principle can have provisional project-specific enforcement details; follow each linked requirement status. Each mapped requirement has acceptance criteria in sections 6–7.

| Source invariant | Preserved meaning | Requirement coverage | Approval boundary |
|------------------|-------------------|----------------------|-------------------|
| ORD-INV-001 | `0 ≤ filled ≤ original` for every order; original quantity never changes in V1. | FR-ORD-016, FR-ORD-017 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| ORD-INV-002 | `original = filled + unexecuted`; active remaining is zero after termination. | FR-ORD-016 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| ORD-INV-003 | Every execution respects both applicable boundaries, including Market protection. | FR-ORD-002, FR-ORD-005, FR-ORD-006, FR-MAT-003 | APPROVED behavior. |
| ORD-INV-004 | Effective cancellation prevents future execution of that remainder; prior fills stand. | FR-ORD-020, FR-MAT-007 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| BAL-INV-001 | Reserved assets cannot fund a second order, including across symbols. | FR-BAL-002, NFR-COR-001 | Approved principle; provisional refinements remain separate. |
| BAL-INV-002 | Reservation/release alone changes availability, not total ownership. | FR-BAL-007, FR-BAL-008, NFR-COR-001 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| BAL-INV-003 | Releasing an unused reservation cannot refund assets already exchanged or fees already paid. | FR-BAL-008, FR-ORD-020 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| TRD-INV-001 | A confirmed Trade remains immutable historical fact. | FR-TRD-004 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| TRD-INV-002 | Both sides agree on symbol, Base quantity, and resting-order execution price. | FR-MAT-003, FR-TRD-001 | Approved principle; provisional refinements remain separate. |
| TRD-INV-003 | Each Trade contributes its quantity once to each participating order's filled total. | FR-ORD-016, FR-TRD-003 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| STL-INV-001 | Settlement gives consistent ownership to all parties, including the fee recipient. | FR-TRD-002, NFR-COR-001 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| STL-INV-002 | Trading conserves each asset across participants plus Fee Treasury; Simulation Funding is accounted for separately. | FR-TRD-002, FR-ADM-001, NFR-COR-001 | Approved principle; provisional refinements remain separate. |
| STL-INV-003 | Each Trade's asset/fee transfers apply once, never again because it is reported again. | FR-TRD-003 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| MKT-INV-001 | Last Price reflects only the latest actual Trade in authoritative execution order. | FR-MKT-006 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| MKT-INV-002 | Market volume counts each Trade once, not once per counterparty. | FR-MKT-007 | PROVISIONAL project detail; source domain concept is not a separate approval. |
| ORD-INV-005 | Equal-price priority follows platform acceptance sequence, never client time or larger quantity. | FR-MAT-002, NFR-DET-001 | APPROVED behavior. |
| TRD-INV-004 | No Trade occurs between the same trading account's orders; STP cancels the resting remainder and creates no fee/volume/Last Price. | FR-MAT-004, FR-MAT-005 | APPROVED behavior. |
| BAL-INV-004 | Accepted commitments cannot require unavailable assets; available and locked balances remain nonnegative. | FR-BAL-001, FR-BAL-003, FR-BAL-004, FR-BAL-005, FR-BAL-006 | Approved principle; provisional refinements remain separate. |
| SIM-INV-001 | Funding is not execution, volume, or trading profit and cannot manufacture a market price. | FR-ADM-001, FR-PF-003 | Approved principle; provisional refinements remain separate. |

## 11. Traceability Matrix

### 11.1 Business → Requirements: decisions

Every OQ was checked individually for system-level impact. Each row lists direct requirement dependencies; the BR matrix also supports rule-level impact review. Follow mapped requirements into section 11.3 to find their local acceptance criteria, composed scenarios, and use cases. For example, changing OQ-027 requires reviewing every requirement listed in its row and every verification item connected to those requirements.

OQ-032 intentionally creates no new requirement: it restates the approved OQ-004 advanced-order exclusion represented by FR-ORD-001. The OQ-032 row is an impact note, not a proposed dependency of that approved requirement. All other 45 decisions have direct requirement coverage. A selected scenario parameter is not a coverage gap.

| Business decision | Source status | Requirements / checked impact |
|-------------------|---------------|-------------------------------|
| OQ-001 | APPROVED | FR-MKT-001 |
| OQ-002 | APPROVED | FR-ACT-001, FR-BAL-001, FR-MKT-001 |
| OQ-003 | APPROVED | FR-ACT-002, FR-SIM-004, FR-MKT-006, FR-MKT-007, FR-PF-003, FR-ADM-001, FR-ADM-002, FR-ADM-003, NFR-COR-001, NFR-SEC-002 |
| OQ-004 | APPROVED | FR-ORD-001, FR-ALGO-001 |
| OQ-005 | APPROVED | FR-ACT-001, FR-SIM-004, FR-BAL-001, FR-BAL-002, FR-ALGO-001, FR-ADM-002, NFR-COR-001 |
| OQ-006 | APPROVED | FR-ORD-001, FR-ORD-004 |
| OQ-007 | APPROVED | FR-ORD-003, FR-ORD-004 |
| OQ-008 | APPROVED | FR-ORD-005, FR-ORD-006, FR-MAT-003, FR-BAL-005, FR-ALGO-001, FR-HFT-003 |
| OQ-009 | APPROVED | FR-ORD-002, FR-MAT-003, FR-TRD-001 |
| OQ-010 | APPROVED | FR-SIM-004, FR-ORD-018, FR-MAT-001, FR-MAT-002, FR-MAT-007, FR-MKT-003, FR-ALGO-001, FR-HFT-001, FR-ADM-002, NFR-DET-001, NFR-REP-001 |
| OQ-011 | APPROVED | FR-SIM-004, FR-MAT-004, FR-MAT-005, FR-MKT-006, FR-MKT-007, FR-ALGO-001, FR-ADM-002 |
| OQ-012 | APPROVED | FR-ORD-002, FR-ORD-003, FR-ORD-004, FR-ORD-007, FR-ORD-019 |
| OQ-013 | APPROVED | FR-BAL-001, FR-BAL-004, FR-FEE-001, FR-HFT-002 |
| OQ-014 | APPROVED | FR-BAL-004, FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-001, FR-FEE-002, FR-FEE-004, FR-HFT-002, NFR-COR-001 |
| OQ-015 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-003, FR-FEE-004, FR-PF-002, FR-HFT-002, NFR-COR-001 |
| OQ-016 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-004, FR-FEE-005, FR-PF-002, FR-HFT-002, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-REP-001 |
| OQ-017 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-008, FR-PF-003, FR-ADM-003, NFR-COR-002 |
| OQ-018 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-009, FR-ORD-010, FR-PF-003, FR-ADM-003, NFR-COR-002 |
| OQ-019 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-010 |
| OQ-020 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-011 |
| OQ-021 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-018, FR-BAL-003 |
| OQ-022 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-003, FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, NFR-COR-001, NFR-COR-002 |
| OQ-023 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-020, FR-BAL-007, FR-BAL-008, FR-TRD-002, NFR-COR-001 |
| OQ-024 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-TRD-003, FR-PF-001, NFR-COR-001 |
| OQ-025 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-012, FR-ORD-013, NFR-AUD-001 |
| OQ-026 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-012, FR-ORD-014, FR-HFT-004, NFR-AUD-001 |
| OQ-027 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-012, FR-ORD-013, FR-ORD-015, FR-ORD-016, FR-ORD-019, FR-ORD-020, FR-BAL-003, FR-BAL-008, FR-MKT-003, NFR-AUD-001 |
| OQ-028 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-016, FR-ORD-017, FR-ORD-018 |
| OQ-029 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-018 |
| OQ-030 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-019 |
| OQ-031 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-014, FR-ORD-018, FR-ORD-020, FR-MAT-006, FR-MAT-007, FR-BAL-008, FR-HFT-001, FR-HFT-004, NFR-DET-002, NFR-AUD-001, NFR-REP-001, NFR-SEC-001 |
| OQ-032 | PROPOSED — OWNER REVIEW REQUIRED | No new requirement: redundant restatement of OQ-004; review FR-ORD-001 for consistency, without changing its APPROVED status. |
| OQ-033 | PROPOSED — OWNER REVIEW REQUIRED | FR-PF-001, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-ADM-003, NFR-COR-002 |
| OQ-034 | PROPOSED — OWNER REVIEW REQUIRED | FR-PF-001, FR-PF-004, NFR-COR-003 |
| OQ-035 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-001, FR-SIM-002, FR-SIM-003, FR-SIM-004, FR-ORD-015, FR-ORD-019, FR-MAT-007, FR-MKT-007, FR-MKT-008, FR-MKT-009, FR-ALGO-002, FR-HFT-001, FR-HFT-004, FR-ADM-003, FR-ADM-004, NFR-DET-002, NFR-REP-001 |
| OQ-036 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-004 |
| OQ-037 | PROPOSED — OWNER REVIEW REQUIRED | FR-TRD-001, FR-MKT-002, FR-MKT-003, FR-MKT-004, FR-MKT-005, FR-HFT-002, NFR-AUD-001, NFR-SEC-001 |
| OQ-038 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-007, FR-MKT-008 |
| OQ-039 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-007, FR-MKT-008, FR-MKT-009, NFR-DET-002 |
| OQ-040 | PROPOSED — OWNER REVIEW REQUIRED | FR-ALGO-001, FR-ALGO-002, NFR-REP-001 |
| OQ-041 | PROPOSED — OWNER REVIEW REQUIRED | FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, FR-ADM-003, NFR-COR-003, NFR-REP-001, NFR-PERF-001 |
| OQ-042 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-SIM-003, FR-FEE-005, FR-PF-003, FR-ALGO-002, FR-ADM-003, FR-ADM-004, NFR-AUD-001, NFR-REP-001, NFR-SEC-002 |
| OQ-043 | PROPOSED — OWNER REVIEW REQUIRED | FR-ACT-003, FR-SIM-004, FR-ALGO-001, NFR-SEC-001 |
| OQ-044 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-005, FR-MKT-006, FR-MKT-009, FR-PF-004 |
| OQ-045 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-SIM-003, FR-TRD-004, FR-ADM-004, NFR-AUD-001, NFR-SEC-002 |
| OQ-046 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-004, FR-BAL-005, FR-BAL-007, FR-BAL-008, FR-TRD-002, FR-TRD-003, FR-FEE-004, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-HFT-003, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-COR-002, NFR-COR-003, NFR-REP-001 |

### 11.2 Business → Requirements: rules

BR rows are mapped exactly by identifier. Composite source rules can support several narrower requirements with different exact OQ sets; the cited clause does not silently import unrelated policy. No requirement marked APPROVED cites a proposed BR.

| Business rule | Source status | Requirement references |
|---------------|---------------|------------------------|
| BR-ORD-001 | APPROVED | FR-ORD-018, FR-MAT-001, FR-MAT-002, FR-MAT-007, FR-MKT-003, FR-ALGO-001, FR-HFT-001, FR-ADM-002, NFR-DET-001, NFR-REP-001 |
| BR-ORD-002 | APPROVED | FR-ORD-002, FR-ORD-003, FR-ORD-004, FR-ORD-007, FR-ORD-019 |
| BR-ORD-003 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-012, FR-ORD-013, FR-ORD-014, FR-ORD-015, FR-ORD-016, FR-ORD-019, FR-ORD-020, FR-BAL-003, FR-BAL-008, FR-MKT-003, FR-HFT-004, NFR-AUD-001 |
| BR-BAL-001 | APPROVED | FR-ACT-001, FR-BAL-001, FR-BAL-002, FR-ALGO-001, FR-ADM-002, NFR-COR-001 |
| BR-BAL-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-003, FR-BAL-004, FR-BAL-005, FR-BAL-006, FR-BAL-007, FR-BAL-008, NFR-COR-001, NFR-COR-002 |
| BR-BAL-003 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-020, FR-BAL-007, FR-BAL-008, FR-TRD-002, NFR-COR-001 |
| BR-BAL-004 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-018, FR-BAL-003 |
| BR-TRD-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-SIM-003, FR-TRD-004, FR-ADM-004, NFR-AUD-001, NFR-SEC-002 |
| BR-STL-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-007, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-TRD-003, FR-PF-001, NFR-COR-001 |
| BR-MKT-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-005, FR-MKT-006, FR-MKT-007, FR-MKT-008, FR-MKT-009, FR-PF-004 |
| BR-MKT-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-TRD-001, FR-MKT-002, FR-MKT-003, FR-MKT-004, FR-MKT-005, FR-HFT-002, NFR-AUD-001, NFR-SEC-001 |
| BR-PF-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-PF-001, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-ADM-003, NFR-COR-002, NFR-COR-003 |
| BR-ACT-001 | APPROVED | FR-SIM-004, FR-MAT-004, FR-ADM-002 |
| BR-ORD-004 | APPROVED | FR-ORD-001, FR-ORD-004, FR-ORD-005, FR-ORD-006, FR-MAT-003, FR-ALGO-001, FR-HFT-003 |
| BR-ORD-005 | APPROVED | FR-ORD-002, FR-MAT-003, FR-TRD-001 |
| BR-ORD-006 | APPROVED | FR-MAT-004, FR-MAT-005, FR-MKT-006, FR-ALGO-001, FR-ADM-002 |
| BR-FEE-001 | APPROVED | FR-BAL-001, FR-BAL-004, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-001, FR-FEE-002, FR-HFT-002, NFR-COR-001 |
| BR-FEE-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-BAL-006, FR-BAL-008, FR-TRD-001, FR-TRD-002, FR-FEE-003, FR-FEE-004, FR-FEE-005, FR-PF-002, FR-HFT-002, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-REP-001 |
| BR-MKT-003 | APPROVED | FR-ACT-001, FR-MKT-001 |
| BR-SIM-001 | APPROVED | FR-ACT-002, FR-SIM-004, FR-MKT-006, FR-PF-003, FR-ADM-001, FR-ADM-003, NFR-COR-001, NFR-SEC-002 |
| BR-PREC-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-ORD-008, FR-ORD-009, FR-ORD-010, FR-ORD-011, FR-BAL-004, FR-BAL-005, FR-BAL-008, FR-FEE-004, FR-PF-002, FR-PF-003, FR-PF-004, FR-HFT-002, FR-HFT-003, FR-ADM-003, NFR-DET-002, NFR-COR-001, NFR-COR-002, NFR-COR-003, NFR-REP-001 |
| BR-ORD-007 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-002, FR-ORD-014, FR-ORD-016, FR-ORD-017, FR-ORD-018, FR-ORD-019, FR-ORD-020, FR-MAT-006, FR-MAT-007, FR-BAL-008, FR-HFT-001, FR-HFT-004, NFR-DET-002, NFR-AUD-001, NFR-REP-001, NFR-SEC-001 |
| BR-SIM-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-SIM-001, FR-SIM-002, FR-SIM-003, FR-ORD-015, FR-ORD-019, FR-MAT-007, FR-FEE-005, FR-PF-003, FR-ALGO-002, FR-ADM-003, FR-ADM-004, NFR-AUD-001, NFR-REP-001, NFR-SEC-002 |
| BR-ACT-002 | PROPOSED — OWNER REVIEW REQUIRED | FR-ACT-003, FR-SIM-004, FR-ALGO-001, NFR-SEC-001 |
| BR-MKT-004 | PROPOSED — OWNER REVIEW REQUIRED | FR-MKT-007, FR-MKT-008, FR-MKT-009, NFR-DET-002 |
| BR-ALGO-001 | PROPOSED — OWNER REVIEW REQUIRED | FR-ALGO-001, FR-ALGO-002, FR-HFT-001, FR-HFT-002, FR-HFT-003, FR-HFT-004, FR-ADM-003, NFR-COR-003, NFR-REP-001, NFR-PERF-001 |

### 11.3 Requirements → Verification

Every requirement maps to its own numbered acceptance criteria. Scenario and use-case columns add broader verification contexts; “Local criteria” means the requirement is fully represented by its local criteria rather than an invented use case. References such as FR-ACT-001/AC-1 identify numbered criteria under the exact requirement heading. Criteria for unresolved details explicitly retain their TBD qualification.

| Requirement | Status | Acceptance criteria | Acceptance scenarios | Use cases |
|-------------|--------|---------------------|----------------------|-----------|
| FR-ACT-001 | APPROVED | FR-ACT-001/AC-1, FR-ACT-001/AC-2 | AS-031 | Local criteria |
| FR-ACT-002 | APPROVED | FR-ACT-002/AC-1, FR-ACT-002/AC-2 | AS-012 | UC-009 |
| FR-ACT-003 | PROVISIONAL | FR-ACT-003/AC-1, FR-ACT-003/AC-2 | AS-027 | UC-009, UC-010 |
| FR-SIM-001 | PROVISIONAL | FR-SIM-001/AC-1, FR-SIM-001/AC-2 | AS-028 | UC-001, UC-002, UC-003, UC-004, UC-009 |
| FR-SIM-002 | PROVISIONAL | FR-SIM-002/AC-1, FR-SIM-002/AC-2 | AS-022 | UC-011 |
| FR-SIM-003 | PROVISIONAL | FR-SIM-003/AC-1, FR-SIM-003/AC-2 | AS-022 | UC-011 |
| FR-SIM-004 | PROVISIONAL | FR-SIM-004/AC-1, FR-SIM-004/AC-2, FR-SIM-004/AC-3 | AS-030 | UC-009 |
| FR-ORD-001 | APPROVED | FR-ORD-001/AC-1, FR-ORD-001/AC-2 | AS-031 | UC-003, UC-004, UC-008 |
| FR-ORD-002 | APPROVED | FR-ORD-002/AC-1, FR-ORD-002/AC-2 | AS-001, AS-006 | UC-001, UC-002 |
| FR-ORD-003 | APPROVED | FR-ORD-003/AC-1, FR-ORD-003/AC-2 | AS-006, AS-013 | UC-001, UC-002, UC-005, UC-006 |
| FR-ORD-004 | APPROVED | FR-ORD-004/AC-1, FR-ORD-004/AC-2 | AS-007, AS-008, AS-009 | UC-003, UC-004, UC-005, UC-006 |
| FR-ORD-005 | APPROVED | FR-ORD-005/AC-1, FR-ORD-005/AC-2 | AS-008 | UC-003, UC-005, UC-008 |
| FR-ORD-006 | APPROVED | FR-ORD-006/AC-1, FR-ORD-006/AC-2 | AS-009 | UC-004, UC-005, UC-008 |
| FR-ORD-007 | APPROVED | FR-ORD-007/AC-1, FR-ORD-007/AC-2 | AS-031 | Local criteria |
| FR-ORD-008 | PROVISIONAL | FR-ORD-008/AC-1, FR-ORD-008/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-009 | PROVISIONAL | FR-ORD-009/AC-1, FR-ORD-009/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-010 | PROVISIONAL | FR-ORD-010/AC-1, FR-ORD-010/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-011 | PROVISIONAL | FR-ORD-011/AC-1, FR-ORD-011/AC-2 | AS-023 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-012 | PROVISIONAL | FR-ORD-012/AC-1, FR-ORD-012/AC-2, FR-ORD-012/AC-3 | AS-017 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-ORD-013 | PROVISIONAL | FR-ORD-013/AC-1, FR-ORD-013/AC-2 | Local criteria | Local criteria |
| FR-ORD-014 | PROVISIONAL | FR-ORD-014/AC-1, FR-ORD-014/AC-2 | AS-025 | UC-007 |
| FR-ORD-015 | PROVISIONAL | FR-ORD-015/AC-1, FR-ORD-015/AC-2 | AS-017 | UC-003, UC-004, UC-007 |
| FR-ORD-016 | PROVISIONAL | FR-ORD-016/AC-1, FR-ORD-016/AC-2 | AS-016, AS-017 | UC-007 |
| FR-ORD-017 | PROVISIONAL | FR-ORD-017/AC-1, FR-ORD-017/AC-2 | AS-030 | Local criteria |
| FR-ORD-018 | PROVISIONAL | FR-ORD-018/AC-1, FR-ORD-018/AC-2 | AS-025 | Local criteria |
| FR-ORD-019 | PROVISIONAL | FR-ORD-019/AC-1, FR-ORD-019/AC-2 | AS-030 | Local criteria |
| FR-ORD-020 | PROVISIONAL | FR-ORD-020/AC-1, FR-ORD-020/AC-2, FR-ORD-020/AC-3 | AS-016, AS-025 | UC-007 |
| FR-MAT-001 | APPROVED | FR-MAT-001/AC-1, FR-MAT-001/AC-2 | AS-001 | UC-001, UC-002, UC-003, UC-004 |
| FR-MAT-002 | APPROVED | FR-MAT-002/AC-1, FR-MAT-002/AC-2, FR-MAT-002/AC-3 | AS-002, AS-003, AS-004, AS-006 | UC-001, UC-002, UC-003, UC-004, UC-005 |
| FR-MAT-003 | APPROVED | FR-MAT-003/AC-1, FR-MAT-003/AC-2 | AS-005, AS-008, AS-009, AS-013 | UC-001, UC-002, UC-003, UC-004, UC-005 |
| FR-MAT-004 | APPROVED | FR-MAT-004/AC-1, FR-MAT-004/AC-2 | AS-010 | UC-001, UC-002, UC-003, UC-004, UC-006 |
| FR-MAT-005 | APPROVED | FR-MAT-005/AC-1, FR-MAT-005/AC-2 | AS-011 | UC-006 |
| FR-MAT-006 | PROVISIONAL | FR-MAT-006/AC-1, FR-MAT-006/AC-2 | AS-025 | Local criteria |
| FR-MAT-007 | PROVISIONAL | FR-MAT-007/AC-1, FR-MAT-007/AC-2 | AS-025 | UC-007, UC-010, UC-011 |
| FR-BAL-001 | APPROVED | FR-BAL-001/AC-1, FR-BAL-001/AC-2 | AS-031 | Local criteria |
| FR-BAL-002 | APPROVED | FR-BAL-002/AC-1, FR-BAL-002/AC-2 | Local criteria | Local criteria |
| FR-BAL-003 | PROVISIONAL | FR-BAL-003/AC-1, FR-BAL-003/AC-2 | AS-014 | UC-001, UC-002, UC-003, UC-004, UC-008 |
| FR-BAL-004 | PROVISIONAL | FR-BAL-004/AC-1, FR-BAL-004/AC-2 | AS-015 | UC-001 |
| FR-BAL-005 | PROVISIONAL | FR-BAL-005/AC-1, FR-BAL-005/AC-2 | AS-014 | UC-003 |
| FR-BAL-006 | PROVISIONAL | FR-BAL-006/AC-1, FR-BAL-006/AC-2 | Local criteria | UC-002, UC-004 |
| FR-BAL-007 | PROVISIONAL | FR-BAL-007/AC-1, FR-BAL-007/AC-2 | AS-015 | UC-001 |
| FR-BAL-008 | PROVISIONAL | FR-BAL-008/AC-1, FR-BAL-008/AC-2 | AS-015, AS-016 | UC-003, UC-004, UC-007 |
| FR-TRD-001 | PROVISIONAL | FR-TRD-001/AC-1, FR-TRD-001/AC-2 | Local criteria | Local criteria |
| FR-TRD-002 | PROVISIONAL | FR-TRD-002/AC-1, FR-TRD-002/AC-2 | AS-018 | UC-001, UC-002, UC-003, UC-004 |
| FR-TRD-003 | PROVISIONAL | FR-TRD-003/AC-1, FR-TRD-003/AC-2 | AS-018 | Local criteria |
| FR-TRD-004 | PROVISIONAL | FR-TRD-004/AC-1, FR-TRD-004/AC-2 | AS-021 | UC-011 |
| FR-FEE-001 | APPROVED | FR-FEE-001/AC-1, FR-FEE-001/AC-2 | AS-013 | Local criteria |
| FR-FEE-002 | APPROVED | FR-FEE-002/AC-1, FR-FEE-002/AC-2 | AS-013, AS-018 | Local criteria |
| FR-FEE-003 | PROVISIONAL | FR-FEE-003/AC-1, FR-FEE-003/AC-2 | AS-024 | Local criteria |
| FR-FEE-004 | PROVISIONAL | FR-FEE-004/AC-1, FR-FEE-004/AC-2 | AS-018, AS-024 | Local criteria |
| FR-FEE-005 | PROVISIONAL | FR-FEE-005/AC-1, FR-FEE-005/AC-2 | AS-024, AS-030 | UC-009 |
| FR-MKT-001 | APPROVED | FR-MKT-001/AC-1, FR-MKT-001/AC-2 | AS-031 | UC-008 |
| FR-MKT-002 | PROVISIONAL | FR-MKT-002/AC-1, FR-MKT-002/AC-2 | AS-027 | Local criteria |
| FR-MKT-003 | PROVISIONAL | FR-MKT-003/AC-1, FR-MKT-003/AC-2 | AS-027 | Local criteria |
| FR-MKT-004 | PROVISIONAL | FR-MKT-004/AC-1, FR-MKT-004/AC-2 | AS-027 | Local criteria |
| FR-MKT-005 | PROVISIONAL | FR-MKT-005/AC-1, FR-MKT-005/AC-2 | AS-027 | Local criteria |
| FR-MKT-006 | PROVISIONAL | FR-MKT-006/AC-1, FR-MKT-006/AC-2 | AS-019, AS-020 | Local criteria |
| FR-MKT-007 | PROVISIONAL | FR-MKT-007/AC-1, FR-MKT-007/AC-2 | AS-019 | Local criteria |
| FR-MKT-008 | PROVISIONAL | FR-MKT-008/AC-1, FR-MKT-008/AC-2 | AS-019, AS-028 | Local criteria |
| FR-MKT-009 | PROVISIONAL | FR-MKT-009/AC-1, FR-MKT-009/AC-2 | AS-020, AS-028 | UC-011 |
| FR-PF-001 | PROVISIONAL | FR-PF-001/AC-1, FR-PF-001/AC-2 | AS-026 | Local criteria |
| FR-PF-002 | PROVISIONAL | FR-PF-002/AC-1, FR-PF-002/AC-2 | AS-026 | Local criteria |
| FR-PF-003 | PROVISIONAL | FR-PF-003/AC-1, FR-PF-003/AC-2 | AS-026 | UC-009 |
| FR-PF-004 | PROVISIONAL | FR-PF-004/AC-1, FR-PF-004/AC-2, FR-PF-004/AC-3 | AS-026 | UC-010 |
| FR-ALGO-001 | PROVISIONAL | FR-ALGO-001/AC-1, FR-ALGO-001/AC-2 | AS-029 | UC-010 |
| FR-ALGO-002 | PROVISIONAL | FR-ALGO-002/AC-1, FR-ALGO-002/AC-2 | AS-029, AS-031 | UC-010 |
| FR-HFT-001 | PROVISIONAL | FR-HFT-001/AC-1, FR-HFT-001/AC-2, FR-HFT-001/AC-3 | AS-029 | UC-009, UC-010 |
| FR-HFT-002 | PROVISIONAL | FR-HFT-002/AC-1, FR-HFT-002/AC-2 | AS-029 | UC-010 |
| FR-HFT-003 | PROVISIONAL | FR-HFT-003/AC-1, FR-HFT-003/AC-2 | AS-029 | UC-010 |
| FR-HFT-004 | PROVISIONAL | FR-HFT-004/AC-1, FR-HFT-004/AC-2 | AS-029 | UC-010 |
| FR-ADM-001 | APPROVED | FR-ADM-001/AC-1, FR-ADM-001/AC-2 | AS-012 | UC-009 |
| FR-ADM-002 | APPROVED | FR-ADM-002/AC-1, FR-ADM-002/AC-2 | AS-012 | UC-009 |
| FR-ADM-003 | PROVISIONAL | FR-ADM-003/AC-1, FR-ADM-003/AC-2 | AS-030 | UC-009 |
| FR-ADM-004 | PROVISIONAL | FR-ADM-004/AC-1, FR-ADM-004/AC-2 | AS-021 | UC-011 |
| NFR-DET-001 | APPROVED | NFR-DET-001/AC-1, NFR-DET-001/AC-2 | AS-002, AS-003 | Local criteria |
| NFR-DET-002 | PROVISIONAL | NFR-DET-002/AC-1, NFR-DET-002/AC-2 | AS-025 | Local criteria |
| NFR-COR-001 | PROVISIONAL | NFR-COR-001/AC-1, NFR-COR-001/AC-2 | AS-018 | Local criteria |
| NFR-COR-002 | PROVISIONAL | NFR-COR-002/AC-1, NFR-COR-002/AC-2 | AS-024 | Local criteria |
| NFR-COR-003 | PROVISIONAL | NFR-COR-003/AC-1, NFR-COR-003/AC-2 | AS-029 | Local criteria |
| NFR-AUD-001 | PROVISIONAL | NFR-AUD-001/AC-1, NFR-AUD-001/AC-2 | Local criteria | Local criteria |
| NFR-REP-001 | PROVISIONAL | NFR-REP-001/AC-1, NFR-REP-001/AC-2 | AS-029 | Local criteria |
| NFR-SEC-001 | PROVISIONAL | NFR-SEC-001/AC-1, NFR-SEC-001/AC-2 | AS-027 | Local criteria |
| NFR-SEC-002 | PROVISIONAL | NFR-SEC-002/AC-1, NFR-SEC-002/AC-2 | AS-021 | Local criteria |
| NFR-PERF-001 | PROVISIONAL | NFR-PERF-001/AC-1, NFR-PERF-001/AC-2 | AS-031 | Local criteria |

## 12. Deferred / Out-of-Scope

Status applies to the deferral decision. Proposed exclusions remain PROVISIONAL; examples here do not authorize future implementation. Definitions of deferred concepts in BUSINESS.md are educational, not V1 requirements.

| Capability outside V1 | Deferral status | Exact business basis |
|----------------------|-----------------|----------------------|
| Real-money trading; real cryptocurrency rights/redemption | APPROVED | OQ-002; BR-MKT-003; BUSINESS.md section 3 |
| Real custody; bank transfers; deposits and withdrawals | APPROVED | OQ-002; BR-MKT-003; BUSINESS.md section 3 |
| Blockchain settlement | APPROVED | OQ-002; BR-MKT-003 |
| External real-market venue or market-data integration | APPROVED | OQ-002; BR-MKT-003 |
| Additional Quote Assets, symbols, or cross pairs | APPROVED | OQ-001; BR-MKT-003 |
| Borrowing, margin, leverage, short selling | APPROVED | OQ-005; BR-BAL-001 |
| Futures, perpetuals, options, other derivatives | APPROVED | OQ-005; BR-BAL-001 |
| Quote-budget Market Buy | APPROVED | OQ-006; BR-ORD-004 |
| Explicit IOC/FOK instructions | APPROVED | OQ-012; BR-ORD-002 |
| Stop, Stop-Limit, Trailing Stop, Iceberg, Pegged, other advanced/conditional orders | APPROVED | OQ-004; BR-ORD-004; OQ-032 only restates this approved exclusion |
| Cross-account human-owner grouping for STP | APPROVED | OQ-011; BR-ORD-006 |
| Order amendment | PROVISIONAL | OQ-028; BR-ORD-007 |
| Combined cancel-and-replace | PROVISIONAL | OQ-029; BR-ORD-007 |
| Automatic GTC expiry, maximum GTC lifetime, EXPIRED state | PROVISIONAL | OQ-030; BR-ORD-007 |
| Historical-data backtesting, optimization suites, strategy marketplaces, institutional research-suite features | PROVISIONAL | OQ-040; BR-ALGO-001; BUSINESS.md section 28 |
| Guaranteed system-provided always-on market maker and automatic inventory replenishment | PROVISIONAL | OQ-036; BR-ACT-002; BUSINESS.md section 27 |
| Shared strategy capital and transfers between accounts | PROVISIONAL | OQ-043; BR-ACT-002 |
| In-run funding and asset transfers between experiments | PROVISIONAL | OQ-042; BR-SIM-002 |
| Trade correction, editing, deletion, or reversal, including administrative exceptions | PROVISIONAL | OQ-045; BR-TRD-001 |
| Random timing jitter | PROVISIONAL | OQ-041; BR-ALGO-001 |
| Tax-lot/FIFO portfolio cost accounting | PROVISIONAL | OQ-033; BR-PF-001; BUSINESS.md section 25 |
| Rolling 24-hour volume and candle intervals other than one minute | PROVISIONAL | OQ-038, OQ-039; BR-MKT-004 |
| Production-HFT latency/performance claims and real-network/hardware benchmarks | PROVISIONAL | OQ-041; BR-ALGO-001; BUSINESS.md section 29 already disclaims a production HFT venue |
| Real regulatory-compliance implementation | APPROVED scope exclusion | OQ-002; BUSINESS.md section 3; this creates no additional compliance capability |

## 13. Architecture and Implementation Deferred

This document **DOES NOT decide** any of the following. They belong to the later **Architecture / System Design** stage and require separate authorization:

- Monolith vs microservices, service boundaries, or Go / Java / C++ ownership.
- Kafka, Redis, PostgreSQL, database schema, or persistence implementation.
- REST API, WebSocket protocol, message format, or interface design.
- Docker, Kubernetes, cloud provider, deployment topology, or infrastructure.
- Threading model, concurrency implementation, locking strategy, or sequence-generation technology.

Authoritative business ordering, immediate settlement, exact decimal amounts, and historical preservation specify externally meaningful behavior. They select no transaction mechanism, programming type, transport, runtime, or storage model. This draft introduces no source code, service scaffolding, implementation plan, or technology choice.

## 14. Open Requirements / TBD Items

These gaps were found while deriving requirements; they are not new business decisions, proposed defaults, or fabricated approvals. Their resolution belongs to owner/team business review. Existing approved rules remain binding; concrete proposed OQs remain PROVISIONAL rather than being erased as “unknown.”

| Gap | Missing business decision / information | Affected sources and requirements | Current boundary |
|-----|-----------------------------------------|-----------------------------------|------------------|
| TBD-001 | Exact experiment start/end authorization, duration/end trigger, complete experiment lifecycle, and handling of submissions while time is stopped. The source does not define a general pause/resume feature. | OQ-035, OQ-042; FR-SIM-001, FR-SIM-002, FR-ADM-003; UC-009, UC-011 | Bounded runs and end effects are specified; do not invent extra experiment states or assume Administrator is the only end actor. |
| TBD-002 | Virtual-time/delay precision and limits, delivery behavior of delayed observations, whether own balance/fill observations have the same delay, and business tie resolution for simultaneous scheduled arrivals/end beyond the authoritative-order principle. | OQ-010, OQ-031, OQ-035, OQ-041; FR-MAT-007, FR-HFT-001, NFR-REP-001 | Preserve fixed nonnegative delays and existing acceptance priority. Sequence-generation implementation remains deferred, not a requirements gap to solve technically here. |
| TBD-003 | Reference origin for time to first/full fill: the source does not select participant decision, submission, platform arrival, or acceptance. | OQ-041; FR-HFT-004; AS-029 | Report missing/unachieved fills as unavailable; exact elapsed-value acceptance waits for the business origin decision. |
| TBD-004 | Participant identity/account administration, detailed administrative visibility/permissions, and how a scenario-hosted strategy relates to the separate-participant-account rule. | OQ-031, OQ-037, OQ-040, OQ-042, OQ-043; BUSINESS.md sections 4, 14, 23, 27; FR-ACT-003, FR-SIM-004, NFR-SEC-001 | Own-account cancellation and private other-account histories are defined. Do not add registration/login behavior, unrestricted admin visibility, or shared participant capital. |
| TBD-005 | History retention duration, export needs, and detailed post-experiment access policy. | OQ-035, OQ-042, OQ-045; FR-SIM-003, FR-TRD-004, NFR-AUD-001 | Preserve history and research-validity labels. No invented retention period, export format, or administrative deletion exception. |
| TBD-006 | Quantitative latency, throughput, capacity, concurrent-user, availability, and recovery targets; representative workload and measurement conditions. | BUSINESS.md section 29; OQ-041; NFR-PERF-001 | All numeric operational targets remain TBD. No production-HFT claim or invented service-level threshold. |

Scenario-specific initial balances, fee rates within the proposed bounds, scenario date/action schedule, acquisition references, and participant delays are **configuration inputs**, not universal defaults waiting to be invented. Strategy algorithms, including adaptive market-making logic, are not designed by this requirements document; ordinary permitted strategy participation is not prohibited by that omission. OQ-015 through OQ-046 still require explicit owner/team approval before provisional requirements become approved.

## 15. Requirements Review Checklist

The checked items record a documentation consistency review on 2026-10-07, **not** human acceptance, implementation completion, or executed behavioral tests.

- [x] Checked all 46 OQs individually for requirements impact; documented OQ-032's intentional absence of a new standalone requirement.
- [x] Represented every approved OQ-001 through OQ-014 without importing proposed funding, state, fee, or experiment details into its approved requirements.
- [x] Kept every requirement with any OQ-015 through OQ-046 dependency PROVISIONAL; retained the proposed source status in decision and rule matrices.
- [x] Used exact OQ and BR IDs, preserved all 19 invariant IDs/meanings, and provided requirements-to-criteria/scenario/use-case mappings.
- [x] Preserved Market immediate termination, absolute protection, resting-order execution prices, price/acceptance priority, and no client-time or size overtaking.
- [x] Preserved STP cancellation of the self-owned resting remainder, continuation of the incoming order, and no Trade/volume/Last Price/fee for the prevented match.
- [x] Preserved per-Trade Maker/Taker classification and the same-order Taker-then-Maker case.
- [x] Kept Simulation Funding separate from Trade, execution, volume, trading profit, and Last Price.
- [x] Included partial fills, underfunding, reservation/release, cancellation history, terminality, once-only settlement, trade-derived data, empty candles, immutable history, and experiment-end scenarios.
- [x] Checked stated arithmetic, units, decimal rules, source references, table formatting, and local source links.
- [x] Recorded unsupported details as TBD; added no business defaults, numerical operational performance targets, architecture selections, or implementation choices.
- [ ] Owner/team reviews provisional business decisions and the resulting provisional requirements.
- [ ] Owner/team resolves applicable TBD items and records actual outcomes through the business review process.
- [ ] Owner/team formally accepts this requirements specification; no such approval is asserted here.
