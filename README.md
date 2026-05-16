# Multi-Asset VaR Engine on Real Futures Data

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Quant Finance](https://img.shields.io/badge/Fields-Quant%20Finance%20%7C%20Risk%20Management-g)](https://github.com/Tejemouti)

> *"A model that cannot be validated is not a risk model. It is a guess dressed in mathematics."*

This repository delivers a production-grade **Value at Risk (VaR) & Conditional Value at Risk (CVaR) Engine** tailored for multi-asset futures portfolios. The engine integrates data pipeline engineering, empirical statistical diagnostics, multiple quantitative risk models, component decomposition, and rigorous backtesting validation into a unified analytics suite.


---

## 📐 Engine Architecture & Workflow

The project is structured systematically into four operational phases, replicating an institutional risk management desk workflow:

### Phase 1: Exploratory Data Analysis & Normality Diagnostics
* **Empirical Distribution Mapping:** Extracts historical returns for **6 core contracts** (6E, 6J, NKD, MBT, MET, ZT).
* **Statistical Verification:** Conducts rigorous normality tests to calculate **Skewness** and **Excess Kurtosis**. 
* **The Fat-Tail Problem:** Proves why traditional parametric assumptions fail by identifying heavy tails across high-volatility assets (Crypto/Equities).

### Phase 2: Core Risk Estimation Methodologies
The engine computes risk simultaneously across **95% and 99% confidence intervals** using three pillars:
1. **Historical Simulation (Non-Parametric):** Reconstructs risk directly from empirical joint distributions without imposing artificial statistical shapes.
2. **Parametric VaR (EWMA Covariance):** Captures dynamic, time-varying volatilities and asset cross-correlations using Exponentially Weighted Moving Averages.
3. **Monte Carlo Simulation (Cholesky Matrix):** Runs thousands of stochastic path simulations while preserving the historical correlation matrix via Cholesky decomposition.

### Phase 3: Component Risk Decomposition
Goes beyond aggregate portfolio metrics to isolate risk drivers:
* **Marginal VaR ($MVaR$):** Quantifies portfolio risk sensitivity relative to minor shifts in position scaling.
* **Component VaR ($CVaR_{comp}$):** Extracts the absolute dollar risk contribution per asset contract.
* **Percentage Contribution:** Pinpoints toxic risk concentration to assist in portfolio rebalancing.

### Phase 4: Statistical Backtesting Framework (Kupiec POF)
* Implements the **Kupiec Proportion of Failures (POF) Likelihood Ratio Test** to validate model accuracy.
* **Key Finding:** Explains the structural failure of Parametric VaR at the 99% level ($p$-value = 0.027) due to tail-underestimation, validating the baseline robustness of Monte Carlo and Historical architectures.

---

## 🛠️ Project Directory Structure

```bash
├── Var engine.ipynb        # Core Jupyter Notebook (Data pipelines, Math, & Visualization)
├── image_1cacf3.png        # Compiled analytical dashboard image 
├── 001_._Combined.pdf      # Complete presentation-ready research report
├── README.md               # Repository documentation
└── requirements.txt        # Analytical environment dependencies
