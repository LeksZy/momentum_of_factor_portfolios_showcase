# Selected strategy: results

[Overview](../README.md) · [Strategy](strategy.md) · [Research process](research.md)

I selected **`f76421ce`** because its backtest results looked promising and used this configuration in live trading. This page reports its **historical simulation after modeled cash and funding, before trading costs**. Realized live account returns are not presented.

## Performance

| Reporting segment | Dates | Total return | CAGR | Annualized Sharpe | Maximum drawdown |
| --- | --- | ---: | ---: | ---: | ---: |
| Full history | 1 Jul 2022–6 Apr 2026 | **+171.09%** | **30.33%** | **2.13** | **13.69%** |
| Earlier segment | 1 Jul 2022–14 Feb 2025 | +116.25% | 34.15% | 2.44 | 7.01% |
| Later segment | 17 Feb 2025–6 Apr 2026 | **+25.36%** | 22.12% | 1.51 | 13.69% |

![Selected strategy: historical portfolio wealth and drawdown](assets/selected_f76421ce.png)

The chart starts portfolio wealth at 100. The blue shaded interval marks the later reporting segment. Drawdown measures the decline from the preceding portfolio peak; a profitable strategy can still experience substantial drawdowns.

The selected setup uses daily close rebalancing, a 0.75-month portfolio-momentum lookback and ten stocks per selection leg. Capital is allocated proportionally across qualifying positive-momentum candidates, with no staggered tranches. [Configuration and accounting](strategy.md#selected-configuration).

## What the figures include

- **Cash and funding:** saved RUONIA funding charges and cash accrual are included.
- **Execution:** commissions, spreads, slippage, market impact, stock-borrow fees and volume-constrained fills are excluded. The later execution simulator is a separate research extension.
- **Calendar:** 954 Monday–Friday observations, including the initial zero-return interval through 11 January 2023. The first nonzero return is on 12 January 2023.
- **Selection:** six fixed specifications were reused and assessed over the full history. The 70/30 division is a reporting split; neither segment establishes an independent final holdout. Test results were visible during research and could influence configuration selection.

## How the metrics are calculated

| Metric | Definition |
| --- | --- |
| Total return | Compound all daily portfolio returns within the stated segment. |
| CAGR | Annualize the compounded return using elapsed calendar time and a 365.25-day year. |
| Annualized Sharpe | Daily mean return divided by sample standard deviation, multiplied by √252. No additional risk-free series is subtracted after financing. |
| Maximum drawdown | Largest decline from a preceding wealth peak, shown as a positive loss magnitude in the table. |

Figures were reconciled against the saved daily return series and run summaries. Segment returns compound; they should not be added together.

No benchmark outperformance is claimed. The earlier benchmark snapshot contains MCFTR under an IMOEXF input label and ends before the full evaluation period; a like-for-like full-period comparison is not established here.

Other configurations produced mixed results, including negative later-period returns. Their [grid, Optuna and walk-forward diagnostics](research.md#experiment-diagnostics) remain available as research context. They do not establish robustness of the selected configuration.
