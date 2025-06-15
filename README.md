# Risk Management for Tech Index Portfolio  
**Volatility-Targeted Delta & Gamma Hedging**

> MATH 583 · Duke University · Final Project (Spring 2025)

![Duke University](https://img.shields.io/badge/Duke%20MIDS-2026-blue)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)

---

## Table of Contents
1. [Project Motivation](#project-motivation)  
2. [Methodology](#methodology)  
3. [Repository Layout](#repository-layout)  
4. [Quick Start](#quick-start)  
5. [Usage Examples](#usage-examples)  
6. [Results & Findings](#results--findings)  
7. [Authors](#authors)  
8. [Acknowledgments](#acknowledgments)  
9. [License](#license)

---

## Project Motivation
Global tech equities exhibit pronounced volatility clusters. Traditional delta‑hedging alone may leave portfolios exposed to convexity risk, while volatility targeting can systematically down‑weight risk but may miss intra‑day shocks.  
We explore a **combined framework** that:

* **Targets ex‑ante volatility** at a user‑defined level (e.g., 10 % annualized).  
* Implements **dynamic delta *and* gamma hedging** using listed index options.  
* Evaluates efficacy via back‑tests on NASDAQ‑100 constituent data from 2018‑2024.

Our goal: **Cut tail‑risk without sacrificing too much upside** compared to naïve 60/40 or pure delta‑hedged tech portfolios.

---

## Methodology
| Step | Description | Key Files |
|------|-------------|-----------|
| **1. Data ingest** | Pull daily & intraday prices via *yfinance* / *Polygon.io* APIs. | [`src/data_loader.py`](src/data_loader.py) |
| **2. Vol Forecast** | EWMA & GARCH(1,1) to estimate σ<sub>t+1</sub>. | [`src/vol_models.py`](src/vol_models.py) |
| **3. Target Weighting** | Scale equity positions: w<sub>t</sub> ∝ σ<sub>*target*</sub>/σ̂<sub>t</sub>. | [`notebooks/01_vol_target.ipynb`](notebooks/01_vol_target.ipynb) |
| **4. Greeks Estimation** | Compute Δ & Γ for each option via Black–Scholes. | [`src/greeks.py`](src/greeks.py) |
| **5. Hedging Engine** | Solve for option trades that minimize residual Δ & Γ subject to costs. | [`src/hedge_engine.py`](src/hedge_engine.py) |
| **6. Back‑test & Risk** | Track P/L, VaR, CVaR, drawdown vs. benchmarks. | [`notebooks/02_backtest.ipynb`](notebooks/02_backtest.ipynb) |

> **Dependencies:** Python ≥ 3.10 · `pandas` · `numpy` · `scipy` · `statsmodels` · `yfinance` · `matplotlib` · `seaborn` · `cvxpy`

---

## Repository Layout
```
├── data/                 # Raw & processed CSV/Parquet
├── notebooks/            # Jupyter analyses & visualisations
├── src/                  # Re‑usable modules
│   ├── __init__.py
│   ├── data_loader.py
│   ├── vol_models.py
│   ├── greeks.py
│   └── hedge_engine.py
├── reports/              # Generated plots + PDF write‑ups
├── tests/                # Unit tests (pytest)
├── requirements.txt
└── README.md
```

---

## Quick Start
```bash
# 1) Clone repo
git clone https://github.com/<your-org>/tech-index-risk-management.git
cd tech-index-risk-management

# 2) Create virtual env
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# 3) Run a minimal back-test
python src/hedge_engine.py --start 2022-01-01 --end 2024-01-01
```

---

## Usage Examples
*Forecast next‑day volatility and recommended hedge notional:*
```bash
python src/cli.py vol-target   --index NDQ   --sigma_target 0.10   --date 2025-06-14
```

*Launch full delta‑gamma back‑test with weekly re‑hedging and 5 bps slippage:*
```bash
python src/cli.py backtest   --index NDQ   --start 2018-01-01   --end 2024-12-31   --hedge_freq 5D   --slippage 0.0005
```

---

## Results & Findings
| Metric | Naïve Index | Vol‑Target Only | Δ‑Γ Hedge | **Combined** |
|--------|-------------|-----------------|-----------|--------------|
| Annual Return | 14.8 % | 13.1 % | 12.6 % | **13.9 %** |
| Annual Vol | 27.3 % | 10.2 % | 22.4 % | **11.3 %** |
| Max DD | −34 % | −12 % | −28 % | **−13 %** |
| Sharpe | 0.54 | 1.29 | 0.56 | **1.23** |

<sub>*See `reports/final_report.pdf` for full plots and statistical tests.*</sub>

---

## Authors
| Name | Role | GitHub |
|------|------|--------|
| **Nzarama Kouadio** | Data Science MSc ’26 | `@nk-kouadio` |
| **Si Min Loo (Lucy)** | Data Science MSc ’26 | `@sml-lucy` |
| **Ramil Mammadov** | Data Science MSc ’26 | `@rm564` |
| **Cynthia Zhou** | Data Science MSc ’26 | `@czhou-ds` |

---

## Acknowledgments
* Duke MATH 583 faculty & teaching staff  
* Quandl / Yahoo Finance for historical price data  
* Hull & White (2017) *Dynamic Hedging* for theoretical guidance  

---

## License
Distributed under the **MIT License**. See `LICENSE` for more information.
