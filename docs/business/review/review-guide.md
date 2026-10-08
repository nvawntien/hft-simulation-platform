# Team review guide

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](../README.md)

## Phase A — migrated teaching checklist

<a id="source-section-34"></a>

## 34. Team Knowledge Checklist

Explain the reason, not just the label. Distinguish approved decisions from recommended details.

1. What leaves and enters each account when buying BTC/USDT? How does selling SOL/USDT differ in units?
2. Why is an Order not a Trade, and why is administrative funding neither one?
3. A resting BUY is at 90,000 and an incoming SELL limit is 86,000. What is the execution price, and why?
4. Reverse which side is resting. Why does the execution price become 86,000?
5. How can one Limit Order be Taker on one Trade and Maker on a later Trade?
6. Why does a Market Buy specify Base quantity and an absolute maximum price rather than a Quote budget or slippage percentage?
7. What happens when only 3 BTC is eligible for a Market Buy of 5 BTC? What happens with zero eligible quantity?
8. Why does a GTC Limit remainder rest while a Market remainder never rests?
9. How do equal displayed timestamps, acceptance sequence, and order quantity affect priority?
10. When an incoming order encounters its own account's resting order, which order is cancelled and what market/fee effects are prohibited?
11. Why does STP not prevent a trade merely because two distinct accounts share a human owner?
12. How do aggregated levels differ from individual orders, and what do count/quantity ahead tell a researcher?
13. Why must BTC/USDT and ETH/USDT orders not reuse the same reserved USDT?
14. Calculate the protected Market Buy reservation for 2 BTC, maximum 90,000, taker fee 0.2%.
15. Why does a Limit Buy reserve using the larger maker/taker rate even while it is currently a taker?
16. In section 12's fee-enabled partial fill, explain the 30,060 cost, 48,096 remaining reserve, and 2,004 release.
17. Why can SELL reserve only Base while still funding its USDT fee under the recommended rate bounds?
18. Reconcile the buyer, seller, and treasury balances after each Trade in section 18.
19. Why can a CANCELLED order have nonzero Filled Quantity? Which events distinguish user cancellation, STP, and a Market remainder?
20. If cancellation is too late, why can it not reverse a Trade? What priority does a separately submitted replacement receive?
21. Explain how Last Price, Best Bid, Best Ask, and Average Execution Price can all differ.
22. Distinguish slippage from impact and fees. Why can mandatory protection still result in incomplete execution?
23. How do empty minutes, same-time Trades, and a Trade exactly on a minute boundary affect the recommended candles?
24. How do weighted-average basis, fees, seeded assets, and unavailable/stale Last Price affect PnL and portfolio valuation?
25. Which decisions are already APPROVED, which require final owner review, and why do simulated queue/delay experiments not establish production HFT performance?

## Phase B — review process for six student developers

### Start with meaning, then challenge outcomes

All six members first read the Business README, Scope/Actors, exact decision statuses and the invariant register. Review one module with its scenarios, then read the cross-domain effects. A PR suggestion is valid when it cites the current source/BS/GAP, gives a concrete counterexample or clarity improvement, and states whether it preserves meaning or asks for a policy change. Correct arithmetic, a matching Mermaid transition and precise traceability are evidence; “industry usually does this” or “AI suggested it” is not approval.

**Cách góp ý:** nêu tình huống cụ thể, trạng thái trước/sau, tài sản nào bị giữ/chuyển/trả và thời điểm/thứ tự đang dùng. Dẫn OQ/BR/invariant/BS. Nếu có hai kết quả hợp lý mà nguồn chưa chọn, viết GAP thay vì tự chọn. Không đánh dấu APPROVED khi sửa câu chữ, merge PR hay thêm ví dụ.

### Proposed review allocation and order

| Member | Primary reading responsibility | Cross-review needed |
|--------|--------------------------------|---------------------|
| 1 | Scope/Actors, Experiment, Administration; start/end/gap controls | Member 6 on clocks/latency and Member 4 on end balances |
| 2 | Markets/Assets, Orders and cancellation/invalid quantity | Member 3 on priority/STP and Member 4 on reservation |
| 3 | Matching/STP, same-time precedence, book ownership | Member 2 on Order states and Member 6 on scheduled ties |
| 4 | Reservation, Trade/Settlement/Fees, conservation/precision | Member 5 on volume/PnL and Member 2 on termination |
| 5 | Market Data, Candle boundaries, Portfolio/PnL | Member 4 on exact money and Member 1 on end/clock |
| 6 | Algo/HFT, observation/action delays, queue/slippage/metrics | Member 1 on lifecycle and Member 3 on effective order |

This is a proposed work allocation, not recorded review or names/approvals. After common reading, review in this sequence: (1) Phase A inventory/register fidelity; (2) BLOCKER experiment/end/tie gaps; (3) Orders/Matching/STP; (4) Reservation/Settlement/Fees; (5) Market Data/Portfolio; (6) HFT comparisons plus identity/observation gaps. Share financial and time findings before approving any complete flow.

### Concise module review checklist

- Business meaning agrees with canonical source; concepts, proposal and approval stay distinct.
- Object ownership, meaningful attributes and what must stay unchanged are clear.
- Success, invalid/foreign/repeated/terminal/no-result/partial/full outcomes are covered where meaningful.
- State transitions agree with source; missing states/control actions are OPEN rather than invented.
- Available/Locked/Total, actual transfers, fees, release, Trade counting and residuals reconcile.
- Time start/progress/stopped behavior, same-time allocation, end and candle boundaries are explicit or GAP-linked.
- No-action is stated and does not imply automatic expiry, maker, timer or fill.
- Diagrams and decision tables use exactly the same outcomes/status boundaries as text.
- OQ/BR/invariant/BS links work; a proposed dependency keeps the complete scenario PROVISIONAL.
- Unsupported assumptions, source conflicts and downstream FR impact are visible; no technology choice is introduced.

### When behavior is unspecified

1. Identify the missing **business decision**, not merely a missing technical mechanism.
2. Cite the domain, source OQ/BR/invariant and a concrete BS or boundary case.
3. Add a gap/comment with known facts, possible alternatives labelled discussion only, material effects and exact owner question. Existing proposed OQs remain intact; ask the authorized owner how new OQs are allocated.
4. Obtain explicit owner/team decision with actual authority/date/outcome. Do not infer approval from a merge or AI explanation.
5. Update canonical Business and affected scenarios, diagrams, tables, invariants/labels and navigation; do not independently edit duplicate policy copies.
6. Reconcile Requirements/criteria/UC/AS and reading aids in separately authorized work, preserving traceability and inherited statuses.
7. Only then implement the resolved behavior in an explicitly authorized implementation phase.

**Không dùng phán đoán cá nhân hoặc AI để thay quyết định còn thiếu.** Có thể đề xuất phương án có lý do, nhưng chưa được dùng như chính sách đã chọn. A clarification preserving source meaning can be reviewed as documentation; changing ownership, fills, priority, costs, timestamps or research outcomes requires an explicit policy decision.

### Reviewable Phase A and Phase B changes

Phase A copies original prose/register rows, freezes both source editions and adds source/ID navigation. Phase B appends clearly labelled analysis and adds scenarios/diagrams/gaps/coverage. [Migration evidence](migration-report.md#phase-review-boundary) explains the checkpoint. No commit/stage/push or human review outcome has been fabricated; the owner may later authorize separate commits/PRs for those phases.
