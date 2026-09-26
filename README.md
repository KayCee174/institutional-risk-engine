# Institutional Risk Management Engine: When "Diversified" Isn't Actually Safe

A Python-based portfolio risk analytics engine implementing historical, parametric,
and Monte Carlo VaR/CVaR, stress testing, model backtesting, risk decomposition,
and risk-adjusted performance — built end-to-end across 13 phases on a three-asset
equity portfolio.

## The One-Line Version

I built a full risk-management stack — VaR, CVaR, correlated Monte Carlo, stress
testing, VaR backtesting, and risk decomposition — on an equal-weighted 3-stock
portfolio, and it caught real risk-limit breaches that a simple "did I lose money"
check would have completely missed.

## Why This Project Exists

Most VaR tutorials stop at "here's your Value at Risk" — one number, calculated
once, never checked against reality. That's not what a real risk desk does. A real
risk desk asks three follow-up questions this project is built to answer: **Is the
risk model actually accurate** (backtesting), **is risk constant or does it change
over time** (rolling risk), and **what happens to this exact portfolio in an actual
historical crash**, not a hypothetical one (stress testing).

## What's Inside

| Stage | What It Covers |
|---|---|
| **Foundation & VaR/CVaR** | Historical, Parametric Monte Carlo, and Correlated (Cholesky) Monte Carlo VaR/CVaR |
| **Multi-Horizon Risk** | 252-day multi-path Monte Carlo simulation with fan-chart visualization |
| **Drawdown Analysis** | Historical peak-to-trough drawdown ("underwater curve") and simulated drawdown distribution |
| **Stress Testing** | Full portfolio re-run through 2008, 2020, and 2022 market conditions, with recovery-time analysis |
| **Model Validation** | VaR backtesting via exception counting, the Kupiec POF test, and Basel-style traffic-light zoning |
| **Risk Decomposition** | Component VaR, Marginal VaR, and portfolio concentration (HHI) |
| **Time-Varying Risk** | Rolling 60-day VaR/CVaR to track risk as it expands and contracts over time |
| **Tail Risk & Performance** | Skewness, kurtosis, Jarque-Bera normality test, Sortino and Calmar ratios |
| **Executive Reporting** | Consolidated risk dashboard and a pass/fail risk-limit compliance matrix |

## The Key Finding

The portfolio **passed** its VaR, CVaR, and max drawdown limits — the metrics most
people stop at. But the risk-limit matrix caught two breaches those metrics alone
would have missed:

| Risk Limit | Policy Limit | Actual | Status |
|---|---|---|---|
| Annualized Volatility | 25.00% | 30.68% | ⚠️ BREACH |
| Herfindahl-Hirschman Index (Concentration) | 0.2500 | 0.3300 | ⚠️ BREACH |

A portfolio can clear every headline loss metric and still be running more
volatility and more single-asset concentration than its own policy allows. That gap
— between "didn't lose too much money" and "was actually managed within risk
appetite" — is the entire reason risk management exists as a discipline separate
from performance reporting.

*(Risk limits above are illustrative thresholds set for this project, not
regulatory requirements or investment recommendations.)*

## Visuals

**VaR/CVaR Method Comparison** — Historical, Parametric Monte Carlo, and Correlated
Monte Carlo estimates side by side, to check whether the models roughly agree.

[![VaR/CVaR Comparison](https://github.com/KayCee174/institutional-risk-engine/raw/main/results/figures/comparison_bar_chart.png)](/KayCee174/institutional-risk-engine/blob/main/results/figures/comparison_bar_chart.png)

**Historical Drawdown (Underwater Curve)** — how deep and how long the portfolio
stayed below its previous peak.

[![Drawdown](https://github.com/KayCee174/institutional-risk-engine/raw/main/results/figures/drawdown.png)](/KayCee174/institutional-risk-engine/blob/main/results/figures/drawdown.png)

**Multi-Path Monte Carlo Fan Chart** — 10,000 simulated one-year paths, showing the
spread of possible outcomes rather than a single point forecast.

[![Multi-Path Fan Chart](https://github.com/KayCee174/institutional-risk-engine/raw/main/results/figures/multi_path_fan.png)](/KayCee174/institutional-risk-engine/blob/main/results/figures/multi_path_fan.png)

**Historical Stress Scenarios** — the portfolio's actual path through 2008, 2020,
and 2022, normalized to a common starting point for direct comparison.

[![Stress Scenarios](https://github.com/KayCee174/institutional-risk-engine/raw/main/results/figures/stress_scenario_chart.png)](/KayCee174/institutional-risk-engine/blob/main/results/figures/stress_scenario_chart.png)

**VaR Backtest: Breaches & Model Validation** — every day the actual loss exceeded
the model's predicted VaR boundary, used to statistically test whether the model
can be trusted.

[![VaR Backtest Breaches](https://github.com/KayCee174/institutional-risk-engine/raw/main/results/figures/var_backtest_breach_chart.png)](/KayCee174/institutional-risk-engine/blob/main/results/figures/var_backtest_breach_chart.png)

*(Underlying numbers for these charts — the VaR/CVaR comparison, the full stress
test matrix, and the risk-limit compliance table — are available as CSVs in
`results/tables/`.)*

## How It's Built

- **Data**: 5 years of daily prices for NVDA, GOOG, and AAPL, pulled via `yfinance`
- **VaR methods**: Historical, Parametric Monte Carlo, and Correlated Monte Carlo
  (Cholesky decomposition), cross-compared side by side
- **Model validation**: Kupiec Proportion-of-Failures test with a binomial-CDF-based
  traffic-light zone assignment, not just a simple exception count
- **Risk decomposition**: Marginal and Component VaR derived directly from the
  covariance matrix, not approximated
- **Confidence level**: 95% used consistently across all VaR/CVaR/backtesting
  calculations

## Repo Structure

```
institutional-risk-engine/
├── notebooks/
│   └── institutional_risk_management_engine.ipynb   ← the full analysis, phase by phase
├── results/
│   ├── figures/                                      ← exported charts
│   └── tables/                                       ← exported CSV results
├── requirements.txt
└── README.md
```

## Running It Yourself

```
git clone https://github.com/KayCee174/institutional-risk-engine.git
cd institutional-risk-engine
pip install -r requirements.txt
jupyter lab notebooks/institutional_risk_management_engine.ipynb
```

Run all cells top to bottom. Swap the `tickers` list in Phase 1 to test the same
risk framework on a completely different portfolio — every function is written to
take weights and returns as parameters, not hardcoded to these three assets.

## Tech Stack

`Python` · `NumPy` · `pandas` · `SciPy` (statistical testing) · `Matplotlib` · `yfinance`

## What's Next

A planned follow-up project — **"Beyond Sharpe"** — will connect this risk engine
directly to the [portfolio optimizer](https://github.com/KayCee174/portfolio-optimization),
running all four optimizer strategies (equal-weight, Monte Carlo, min-volatility,
max-Sharpe) through this same risk framework to test whether the "mathematically
optimal" portfolio also holds up best under stress and backtesting — or whether,
like in the optimizer project, the boring choice wins again.

---

*This is a personal learning project exploring quantitative risk management and
model validation practices. Not financial advice.*
