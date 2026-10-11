# Results: selected configuration

[Overview](../README.md) · [Strategy](strategy.md) · [Research process](research.md)

I selected **`f76421ce`** because its backtest results looked promising and used that configuration in live trading. This page presents the historical simulation of that configuration; the figures are **not realized live P&L**.

## Selected configuration: `f76421ce`

| Backtest segment | Dates | Total return | CAGR | Annualized Sharpe | Maximum drawdown |
| --- | --- | ---: | ---: | ---: | ---: |
| Full | 1 Jul 2022–6 Apr 2026 | **+171.09%** | 30.33% | 2.13 | 13.69% |
| Training segment | 1 Jul 2022–14 Feb 2025 | +116.25% | 34.15% | 2.44 | 7.01% |
| Later reporting segment | 17 Feb 2025–6 Apr 2026 | **+25.36%** | 22.12% | 1.51 | 13.69% |

![Cumulative returns and drawdown for f76421ce](assets/selected_f76421ce.png)

This configuration rebalances daily at the close, uses 0.75 months of portfolio momentum and selects 10 stocks per factor-portfolio leg. It weights qualifying portfolios by positive momentum and uses no staggered tranches.

The simulation includes cash and RUONIA funding adjustments. It excludes commissions, bid–ask spreads, slippage, market impact, stock-borrow fees and volume-constrained fills. The full series contains 954 observations, including the initial zero-return period through 11 January 2023. The original evaluation reused fixed factor specifications and scored the full window. Its 70/30 split is a reporting division, so the later segment should not be interpreted as an untouched holdout.

## Reading the results

Returns are compounded. CAGR uses elapsed calendar time. Sharpe is the mean daily return divided by its sample standard deviation, annualized with `sqrt(252)`, without an additional risk-free subtraction after financing. Maximum drawdown is reported as a positive loss magnitude in the table; the chart shows declines from prior portfolio peaks.

Other configurations produced mixed results, including negative later-period returns. The separate [grid, Optuna and walk-forward experiments](research.md#experiment-diagnostics) document that sensitivity; they do not establish the robustness of `f76421ce`. Full-period scoring and visibility of test results during research limit any out-of-sample interpretation of the selected configuration.
