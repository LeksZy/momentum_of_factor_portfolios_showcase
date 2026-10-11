# Research process

[Overview](../README.md) · [Strategy](strategy.md) · [Results](results.md)

## Research question

The project studies whether the recent performance of factor portfolios can guide a changing allocation across them. Stock-level factors define candidate portfolios; momentum of those portfolios determines which candidates receive capital. This separates the signal used to select securities from the signal used to allocate among investment styles.

My work covered data preparation, factor construction, portfolio simulation, parameter optimization and evaluation. I ultimately selected `f76421ce` for live use on the strength of its promising historical results. Its published performance remains backtested performance, distinct from live account returns.

## Data preparation

The research combines closing prices, dividend events, financial ratios, a market benchmark and RUONIA rates. A fixed universe of 45 identifiers is used in the research version; 41 have quotes in its local price snapshot. This is a static research universe, without reconstructed historical index membership or a liquidity screen.

| Input | Coverage in the research snapshot | Treatment and limitations |
| --- | --- | --- |
| Closing prices | Jan 2015–Jan 2026; 86 tickers before universe filtering | Used for returns and factor calculations; this table has no intraday volume or spread data. |
| Dividends | Jan 2015–Jan 2026; 744 events | Added to returns on the next observed trading date. Original event-date and tax conventions remain unspecified. |
| Financial ratios | Mar 2020–Sep 2025; 648 observations | P/E and P/B are matched backward after an assumed 30-day availability lag. Actual publication timestamps are unavailable. |
| Market benchmark | Jan 2015–Nov 2025 | Stored as MCFTR despite an IMOEXF input label; coverage ends before several strategy evaluations. |
| RUONIA | Jan 2022–Jan 2026; 991 observations | Converted to annual decimal rates and used in the model's funding convention. |

These dates describe the research snapshot. The selected configuration used a later SQL-derived dataset through April 2026. The original collectors and exact historical input snapshot are not included here; provider attribution and corporate-action adjustments cannot be fully reconstructed from the available records.

Observations are aligned by security and trading date. The main simulation substitutes zero for missing security returns, while some factor-return calculations omit missing observations. This difference can affect exposure and comparability. An assumed reporting lag also does not guarantee that every financial observation was available at the simulated decision time.

## Parameter optimization

The inner search chooses one portfolio specification per enabled factor category. Each specification sets the factor, its observation window and the portfolio direction. Combinations are either evaluated exhaustively or sampled without replacement using a fixed seed and a maximum evaluation budget.

The outer searches vary portfolio-momentum lookback, rebalance frequency, stock count, factor count and, in selected experiments, tranche count. Cached candidate returns, array-based calculations and parallel workers reduce repeated computation.

The completed studies include:

- A 24-setting grid across weekly, biweekly and monthly rebalancing, one- and three-month momentum, five or ten stocks per leg, and one or two selected factor portfolios.
- Earlier 54-setting grids with an additional half-month lookback and a third selected factor portfolio, using combination budgets up to 65,536.
- A 50-trial Optuna study with 20 distinct outer parameter combinations, one to three tranches, ten stocks per leg, and a 25,000-combination budget per trial. Its base seed is 42; individual trials use different derived seeds.

The main objective is annualized daily Sharpe. Other experiments use return–drawdown objectives or sensitivity penalties. Repeated trials and capped searches do not establish a globally optimal strategy.

`f76421ce` is a fixed-specification evaluation, reusing six previously selected candidates. It uses daily close rebalancing, a 0.75-month momentum window and momentum-proportional weights above a historical zero cutoff. Its reporting run performs no new combination search. Later defaults use a 1.5% cutoff, which would change the strategy.

## Validation and interpretation

Chronological train/test splits and expanding-window evaluations are present in the research. The 24-setting grid trains through 10 June 2025 and tests from 11 June 2025 to 22 January 2026. The separate Optuna example trains through 6 September 2025 and tests from 7 September 2025 to 13 March 2026. Neither saved example has a separate validation interval.

Walk-forward experiments reselect the factor combination using expanding historical windows. Fold-average statistics and returns compounded across disjoint test periods answer different questions and must be kept separate. A completed two-fold example provides an additional assessment; incomplete later batches do not establish comprehensive robustness.

The selected configuration's 70/30 split divides its completed return series for reporting, with the later segment starting on 17 February 2025. Full-period scoring and visibility of test statistics during research prevent treating this as independent final out-of-sample evidence. Rebalance-start sensitivity was also explored, but the saved multi-anchor study produced no successful trials.

## Trading considerations

The model aggregates overlapping positions, allows weight drift and applies simplified RUONIA cash and funding adjustments. Its close-price timing convention assumes execution without modelling an order book. The selected results exclude transaction costs, slippage, market impact and borrow fees.

Staggered tranches were explored as a way to distribute rebalance timing. They are disabled in `f76421ce`, and do not themselves impose capacity constraints. Participation limits, partial fills, execution delays and liquidity-dependent costs remain separate modelling requirements before the historical returns can support a capacity or executable-return estimate.
