# Glossary — canonical vocabulary

**DRAFT — REQUIRES TEAM / OWNER REVIEW**

[Business navigation](README.md)

## Phase A — migrated source

<a id="source-section-33"></a>

## 33. Glossary

Definitions are educational; project-policy status follows the decision register. Deferred concepts remain useful vocabulary.

| English Term | Explanation | Example / V1 distinction |
|--------------|-------------|--------------------------|
| Trader / Trading Account | A participant / its separate funded trading identity. | STP compares accounts, not human ownership groups. |
| Asset | A unit that can be held and exchanged. | Simulated BTC, ETH, SOL, USDT. |
| Base / Quote Asset | Asset being bought/sold / asset expressing and paying its price. | BTC / USDT in BTC/USDT. |
| Symbol / Trading Pair | Named Base/Quote market. | ETH/USDT. |
| Spot Trading | Exchange of the assets themselves. | Receive BTC for USDT. |
| Order / Trade | Trading intention / actual matched exchange. | A cancelled remainder is not a reversed Trade. |
| BUY / SELL | Acquire / give Base units. | SELL SOL receives USDT. |
| Limit Order / Limit Price | Priced intention / maximum buy or minimum sell boundary. | BUY LIMIT 90,000 can execute at 86,000. |
| Market Order | Immediate execution against eligible interest. | V1 requires an absolute protection price and never rests. |
| Quote-budget Buy | Buying by a spending cap rather than Base quantity. | Domain concept — deferred beyond V1. |
| Original / Filled Quantity | Requested Base amount / actual executed amount. | 10 requested, 4 filled. |
| Unexecuted / Active Remaining | Not executed / still eligible to execute. | After cancelling the remaining 6, unexecuted is 6 but active remaining is 0. |
| Notional | Quantity × execution price before fees. | 0.5 × 86,000 = 43,000 USDT. |
| Fill / Partial Fill | Execution / execution of less than the original quantity. | 0.4 BTC of a 1 BTC order. |
| Order Book / Resting Order | Current standing interest / an active order in it. | Only Limit remainders rest in V1. |
| Incoming / Aggressive Order | Newly presented interest / interest consuming resting liquidity. | A marketable Limit can take liquidity. |
| Bid / Ask | Buying / selling offer. | Bid 85,500; Ask 86,000. |
| Best Bid / Best Ask | Highest bid / lowest ask. | Both may change without a Trade. |
| Spread | Best Ask minus Best Bid. | 500 when quotes are 85,500 / 86,000. |
| Price Level / Aggregated Depth | One price / summed active quantities at prices. | 0.4 + 0.6 = 1 BTC at 86,000. |
| Matching | Allocation of eligible opposite interest. | Price first, then platform acceptance order. |
| Price-Time Priority / FIFO | Better price first, then earlier same-price priority. | Later larger orders cannot overtake. |
| Acceptance Sequence | Authoritative order of platform acceptance. | #1001 precedes #1002 despite equal displayed time. |
| Queue Position | Position among competing same-price orders. | Count and quantity ahead of one's order. |
| Execution Price | Actual unit price of a Trade. | Always the matched resting price in V1. |
| Price Improvement | Execution better than the incoming boundary/comparison. | Buy at 86,000 rather than its 90,000 limit. |
| Average Execution Price | Total executed notional / executed Base quantity. | 173,000 / 2 = 86,500, excluding fees. |
| Last Price | Latest actual Trade price. | Unavailable before the first Trade. |
| Reference Price / Midpoint | Named comparison price / halfway between two named prices. | Midpoint is not V1's execution-price rule. |
| Available / Locked Balance | Reusable assets / assets committed to active orders. | Both still belong to the account. |
| Reservation / Release | Commit assets / return unused commitment to availability. | Release is not profit. |
| Settlement | Fulfillment of the actual exchange, including fees. | Recommended immediate settlement per Trade. |
| Cancellation | End an active unfilled remainder. | Existing fills and fees stand. |
| OPEN / PARTIALLY_FILLED | Resting without fills / resting with some fills. | Recommended nonterminal Limit states. |
| FILLED / CANCELLED / REJECTED | Completed / remainder ended / failed before acceptance. | Recommended terminal states. |
| NEW / CANCEL_PENDING / EXPIRED | Other possible industry lifecycle labels. | Not observable V1 states in the recommended model. |
| Termination Reason | Explanation separate from state. | USER_REQUEST, SELF_TRADE_PREVENTION, MARKET_REMAINDER, EXPERIMENT_ENDED. |
| Amendment / Cancel-and-Replace | Change an existing commitment / a combined replacement operation. | Deferred; separate cancel then new is recommended V1 behavior. |
| Time In Force (TIF) | Persistence/completeness instruction separate from type. | V1 Limit uses GTC. |
| GTC | Good Till Cancelled. | Rest until completion/cancellation or defined experiment end. |
| IOC | Immediate Or Cancel; execute available quantity and end the remainder. | Domain concept — explicit IOC deferred; Market does not require it. |
| FOK | Fill Or Kill; immediate complete execution or none. | Domain concept — deferred; never partial then reverse. |
| Self-Trade Prevention (STP) | Prevent execution within one trading account. | Cancel self-owned resting remainder; incoming continues. |
| Liquidity / Market Maker | Ability to execute size / participant supplying buying and selling interest. | Funded scenario accounts have no guaranteed replenishment. |
| Inventory | Assets held by a participant. | BTC available for scenario SELLs. |
| Slippage | Execution difference against a declared reference. | Adverse difference is positive in recommended reports. |
| Market Impact | Change in market conditions caused by trading. | Consuming low Asks can raise Last Price. |
| Price Protection | Absolute acceptable Market execution boundary in V1. | Maximum BUY 90,000; minimum SELL 80,000. |
| Market Data / Volume | Observations of interest/executions / quantity or value actually traded. | Funding creates no volume. |
| Candlestick / OHLCV | Interval summary of Open, High, Low, Close, Volume. | Recommended one-minute trade-derived candles. |
| Portfolio / Position | Holdings and performance / amount held. | Locked BTC remains part of the position. |
| Cost Basis / Average Cost | Assigned acquisition cost / basis per unit held. | Recommended weighted-average accounting includes buy fees. |
| Realized / Unrealized PnL | Gain/loss on sold units / indicative gain/loss on remaining holdings. | Seed funding is not profit. |
| Equity / Liquidation Value | Marked portfolio value / proceeds obtainable by selling holdings. | Last Price equity is not guaranteed liquidation proceeds. |
| Fee / Fee Treasury | Trading charge / its designated simulated platform recipient. | Recommended USDT fees are conserved with treasury balances. |
| Maker / Taker | Resting liquidity provider / incoming liquidity consumer for one Trade. | One order can take now and make later. |
| Tick / Quantity Step (Lot) | Allowed price / Base-quantity increments. | Recommended 0.01 USDT price tick. |
| Residual / Rounding | Amount left after allocation / a declared precision operation. | Retain cost residual until the position is fully sold. |
| Simulation Funding | Setup allocation of simulated capital. | No Trade, execution, volume, or profit. |
| Experiment / Experiment Clock | A bounded scenario / its authoritative virtual time. | Reset starts a separate history. |
| Algorithmic / Quantitative Trading | Automated decisions / decisions informed by quantitative evidence. | Strategy participation does not control matching rules. |
| Backtesting | Evaluating a strategy on historical scenarios/data with execution assumptions. | Historical-data backtesting is deferred beyond V1. |
| HFT / Latency | Timing-sensitive frequent automated trading / delay between relevant events. | Simulated observation/action delays are research inputs. |
| Execution Quality | Price, size, cost, completion, and timing of actual execution. | Speed without fills is not automatically better. |
| Stop / Stop-Limit / Trailing Stop | Triggered market/limit intent / a trigger that follows a reference. | Domain concepts — deferred, with no V1 trigger semantics. |
| Iceberg / Pegged Order | Partially displayed size / price linked to a reference. | Domain concepts — deferred beyond V1. |
| Margin / Leverage / Short Selling | Trading using credit or enlarged exposure / selling assets not owned. | Outside funded V1 spot scope. |
| Derivative / Futures / Perpetual / Option | Contracts tied to an underlying value, with differing expiry or exercise rights. | Domain concepts — all deferred beyond V1. |
| Business Invariant | Consistency condition within the stated model. | One Trade cannot settle twice. |
