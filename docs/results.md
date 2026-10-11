# Results

[Overview](../README.md) · [Strategy](strategy.md) · [Research process](research.md)

I selected **`f76421ce`** because its backtest results looked promising and used that configuration in live trading. The figures here describe the historical simulations behind the research, **not realized live P&L**.

## Selected configuration: `f76421ce`

| Backtest segment | Dates | Total return | CAGR | Annualized Sharpe | Maximum drawdown |
| --- | --- | ---: | ---: | ---: | ---: |
| Full | 1 Jul 2022–6 Apr 2026 | **+171.09%** | 30.33% | 2.13 | 13.69% |
| Training segment | 1 Jul 2022–14 Feb 2025 | +116.25% | 34.15% | 2.44 | 7.01% |
| Later reporting segment | 17 Feb 2025–6 Apr 2026 | **+25.36%** | 22.12% | 1.51 | 13.69% |

![Cumulative returns and drawdown for f76421ce](assets/selected_f76421ce.png)

This configuration rebalances daily at the close, uses 0.75 months of portfolio momentum and selects 10 stocks per factor-portfolio leg. It weights qualifying portfolios by positive momentum and uses no staggered tranches.

The simulation includes cash and RUONIA funding adjustments but excludes execution costs and stock-borrow fees. The full series contains 954 observations, including the initial zero-return period through 11 January 2023. The original evaluation reused fixed factor specifications and scored the full window. Its 70/30 split is a reporting division, so the later segment should not be interpreted as an untouched holdout.

## Parameter sensitivity

A separate 24-configuration grid compared rebalancing intervals, momentum lookbacks, stock counts and the number of factor portfolios. Its training period was **3 January 2024–10 June 2025**; the test period was **11 June 2025–22 January 2026**. The table shows the highest-training-Sharpe configuration within each frequency.

| Rebalancing | Momentum lookback | Stocks / factor portfolios | Training Sharpe | Test return | Test Sharpe | Test drawdown |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| Weekly | 3 months | 5 / 1 | 2.94 | −17.17% | −1.08 | 28.73% |
| Biweekly | 3 months | 10 / 1 | 2.84 | −9.56% | −1.31 | 14.84% |
| Monthly | 3 months | 5 / 2 | 3.44 | −7.99% | −0.73 | 19.60% |

![Test-period cumulative returns and drawdowns](assets/grid_test_performance.png)

Only four of the 24 settings produced positive test returns; the median test Sharpe was −0.75. Test returns ranged from −29.04% to +9.53%. Strong training performance did not persist in the later period.

![Test Sharpe across 24 parameter settings](assets/grid_parameter_sensitivity.png)

Each setting also searches factor combinations, so differences reflect the complete configuration rather than the isolated effect of one parameter. These grid examples are separate from the configuration I ultimately used.

## Optuna experiment

A separate 50-trial study selected a configuration with **biweekly rebalancing, a 0.5-month momentum lookback, 10 stocks, one selected factor portfolio and one tranche**. The inner search evaluated up to 25,000 factor combinations per trial. The table reports its training-selected winner.

| Period | Dates | Total return | CAGR | Annualized Sharpe | Maximum drawdown |
| --- | --- | ---: | ---: | ---: | ---: |
| Training | 2 May 2024–6 Sep 2025 | +121.68% | 80.58% | 3.67 | 5.40% |
| Test | 7 Sep 2025–13 Mar 2026 | **+13.18%** | 27.36% | 1.67 | 4.58% |
| Full | 2 May 2024–13 Mar 2026 | +150.91% | 63.90% | 3.13 | 5.40% |

![Training and test Sharpe across the Optuna trials](assets/optuna_train_test.png)

This study used a different input snapshot and includes weekend observations. It had no separate validation interval. Its results cannot be directly attributed to an improvement over the earlier grid or compared with `f76421ce` as if the inputs and periods were identical.

The +13.18% test return comprises **+12.59 percentage points from securities, +12.27 from cash accrual and −11.68 from funding**, with each daily component weighted by prior portfolio wealth. This is an accounting decomposition; it does not establish executable financing terms or individual stock contributions.

## Walk-forward evaluation

An expanding-window experiment reselected factor combinations at successive dates. For the example below, the outer settings remain weekly rebalancing, one-month momentum, five stocks and one factor portfolio.

| Test period | Total return | Annualized Sharpe | Maximum drawdown |
| --- | ---: | ---: | ---: |
| 27 Dec 2024–11 Jul 2025 | −3.62% | −0.13 | 22.50% |
| 14 Jul 2025–22 Jan 2026 | −26.00% | −3.00 | 29.25% |
| Combined periods | **−28.67%** | −1.26 | 33.66% |

The combined return compounds both test sequences. It is different from averaging their individual returns, and it does not include a separate model of transition costs between the reselected portfolios.

## Interpretation

Returns are compounded. CAGR uses elapsed calendar time; Sharpe uses mean daily return divided by sample standard deviation and annualizes with `sqrt(252)`, without subtracting another risk-free series after financing. The same 252-day convention was retained for the weekend-containing Optuna series. Drawdown is shown as a positive loss magnitude in the tables.

All results above exclude modeled commissions, bid–ask spreads, slippage, market impact and volume-constrained fills. Test results were visible during research and could influence configuration selection. Chronological splits and walk-forward exercises provide useful diagnostics but do not establish an untouched final holdout or a verified live track record.
