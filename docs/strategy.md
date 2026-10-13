# Strategy and implementation

[Overview](../README.md) · [Selected results](results.md) · [Research process](research.md)

## Research idea

A **factor portfolio** is a basket of stocks ranked by a shared characteristic: for example, low valuations or high recent returns. Different baskets can perform differently as market conditions change. I investigated whether their recent performance could guide allocation between them.

The strategy has two decisions: which stocks belong in each basket, and how much capital each basket receives. **Stock momentum** is one stock-selection signal; **portfolio momentum** is the allocation signal applied to all candidate baskets.

The research uses a fixed universe of Russian-market identifiers. Eligibility depends on available prices and factor observations. It does not reconstruct historical index membership, and the original selection has no volume or spread screen.

## Selected configuration

I selected `f76421ce` for its promising historical results and used it in live trading. Its saved configuration is:

| Decision | Setting |
| --- | --- |
| Rebalance schedule | Daily close, Monday–Friday calendar |
| Allocation signal | Trailing factor-portfolio return over 0.75 months |
| Stock selection | Ten stocks per selection leg, subject to eligible observations |
| Candidate set | Six fixed factor-portfolio specifications |
| Allocation weights | Proportional to strictly positive portfolio-momentum scores |
| Staggered tranches | Disabled |

Several candidates can receive capital simultaneously. The recorded factor-count setting does not cap this momentum-weighted allocation mode. The original positive-return cutoff was zero; a later 1.5% default describes a different strategy version.

| Selected basket | Signal and interpretation |
| --- | --- |
| High minus low beta | One-week sensitivity to the supplied market benchmark |
| High minus low stock momentum | Two-week compounded stock returns |
| Short low-momentum stocks | Three-month stock returns |
| High minus low volatility | Three-month return dispersion |
| High minus low dividend carry | Three-month dividends relative to price |
| Low minus high P/E | Positive price-to-earnings ratios |

Long–short baskets initially assign total weight +1 to the long leg and −1 to the short leg. Their gross exposure is two before opposing positions cancel. The combined portfolio can have changing net and gross exposures; its allocation weights summing to one does not make it market-neutral.

## Portfolio engineering

| Problem | Implementation decision | Consequence |
| --- | --- | --- |
| The same stock appears in multiple baskets | Aggregate signed security weights and net opposing positions | Exposure reflects the combined position, rather than the sum of separate basket exposures. |
| Holdings change value between resets | Allow security weights to drift | The assembled strategy differs from the fixed-weight return proxies used to rank candidate baskets. |
| Missing or stale prices | Substitute zero for missing security returns in the main simulation | Keeps the return matrix aligned, but can understate uncertainty; some factor calculations instead omit missing observations. |
| Financial information has a release delay | Apply an assumed 30-calendar-day lag in the research factor construction | A lag is a proxy for availability; actual publication timestamps remain unverified. |
| Signals and execution occur at different times | Apply decisions dated T to the next observed return interval | Close-price fills remain an assumption; the simulation does not demonstrate their availability. |
| Cash and long/short positions need consistent financing | Account for RUONIA funding and cash accrual separately | Simplified financing can differ from broker margin and stock-borrow terms. |

RUONIA, the overnight ruble rate, is converted to a daily rate by dividing its annual decimal value by 252. The model charges funding on one unit of principal and accrues cash on one minus net exposure. The resulting net financing adjustment is **minus net exposure × daily RUONIA**. Flat and net-zero positions receive no net accrual under this convention.

Dividends enter stock returns on the next observed trading date. Whole-month portfolio lookbacks use calendar offsets; fractional months use 30 days per month. These conventions are part of the historical model, rather than assumptions about actual settlement dates.

## Rebalancing and execution research

**Staggering** spreads portfolio resets across multiple schedules. It was explored as a timing choice and is disabled in `f76421ce`. It does not, by itself, constrain trading to available volume.

A later research extension addresses execution explicitly:

| Capability | Selected historical backtest | Later minute-level simulator |
| --- | --- | --- |
| Fill prices and timing | Close-based return convention | Minute-bar price proxies, with latency |
| Liquidity constraints | No volume-constrained fills | Participation caps based on preceding completed volume; residual quantities carried as partial fills |
| Tradable quantities | Continuous portfolio weights | Whole lots and adverse tick rounding |
| Trading costs | Excluded | Configurable fees and slippage assumptions |
| Funding and borrowing | Simplified RUONIA cash/funding; no stock-borrow fees | Cash/share accounting with funding and borrow-cost assumptions |
| Portfolio limits | Strategy weights and netting | Gross/net exposure, individual-stock and turnover limits |

The later simulator is a **research prototype**. Existing tests cover timing, partial fills, unavailable shorts, lot/tick constraints, costs, funding and restart accounting; the 16-test suite passes. These checks establish accounting behavior, not improved performance or deployed execution.

The simulator uses minute-bar price and volume proxies; it has no historical order-book replay or calibrated market-impact model. VWAP is an execution benchmark. Live VWAP execution remains unverified, and the selected backtest has not been rerun with the later simulator.

## Interpretation and next steps

The main research limitations are the fixed universe, assumed information availability and missing-return handling. Part of the earlier factor implementation also applies a rolling-signal shift across stock boundaries at the first observation of a new stock. Historical strategy versions therefore need to be distinguished when reproducing or extending the work.

The next evaluation step is to apply the execution model to a frozen configuration and reserved period, with measured spread, borrow and liquidity inputs. That would support a more informative comparison of theoretical and executable returns. [Searches and validation](research.md).
