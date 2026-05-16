# Multi-Asset VaR Risk Engine on Real Futures Data

## 6 CME Futures Contracts | 3 Core Methodologies | Kupiec POF Backtesting

This repository contains **Notebook 5** of my Quantitative Research portfolio, implementing a production-quality **Value at Risk (VaR) & Expected Shortfall (CVaR) Engine** tested on raw tick-level trade data.

The engine quantifies 1-day ahead market risk for an equally weighted portfolio across 6 liquid asset classes and statistically validates model accuracy using parametric and non-parametric frameworks.

---

## 1. Project Architecture & Methodology

Financial risk models often fail not due to mathematical flaws, but due to poor data ingestion pipelines. This project addresses two major market microstructure anomalies:
1. **The Session Boundary Problem:** Futures trade ~23 hours a day. Standard calendar-day resampling mixes trading sessions. This engine converts UTC timestamps to `America/Chicago` time and establishes a strict daily boundary.
2. **Asynchronous Tick Arrival:** Raw trade ticks arrive stochastically. To eliminate microstructure noise and align the cross-section, ticks are aggregated into **1-Minute VWAP bars**. A **Strict Synchronous Snapshot** is then captured between 15:55 and 16:05 CT daily, picking the last available transaction price before the session settlement. This preserves the economic integrity of the Covariance Matrix.

---

## 2. Portfolio Specification

The engine monitors a diversified multi-asset futures portfolio:
* **Foreign Exchange:** `6E` (Euro FX), `6J` (Japanese Yen)
* **Equity Indices:** `NKD` (Nikkei 225 Dollar-Denominated)
* **Cryptocurrencies:** `MBT` (Micro Bitcoin), `MET` (Micro Ether)
* **Fixed Income/Rates:** `ZT` (2-Year Treasury Note)

*Note: COMEX Metals (GC, SI, HG) were intentionally omitted in Phase 1 to maintain absolute 16:00 CT data homogeneity, avoiding asynchronous correlation dampening caused by early 13:30 CT cash closes.*

---

## 3. Implemented Risk Frameworks

The engine computes risk at both **95%** and **99%** confidence levels using three distinct approaches:

### A. Historical Simulation (Non-Parametric)
* Makes no distributional assumptions. 
* Captures empirical fat-tails, skewness, and regime shifts naturally.
* Implements **Conditional VaR (CVaR / Expected Shortfall)** to quantify tail risk conditional on a breach.

### B. Parametric (Variance-Covariance)
* Assumes a multivariate normal return distribution.
* Portfolio variance is mapped analytically via: 
  $$\sigma_{p}^{2} = w^T \Sigma w$$
* Fast and analytically tractable, allowing for immediate risk decomposition.

### C. Monte Carlo Simulation (Stochastic)
* Simulates 10,000 correlated portfolio return paths.
* Uses **Cholesky Factorization** to decompose the positive-definite covariance matrix ($\Sigma = L L^T$) and inject historical correlation structures into uncorrelated standard normal random variables ($Z$):
  $$\text{Simulated Returns} = \mu + Z \cdot L^T$$

---

## 4. Statistical Validation (Backtesting)

To ensure the engine is fit for capital allocation rules (e.g., Basel frameworks or Prop Firm drawdown constraints), models are subjected to the **Kupiec Proportion of Failures (POF) Test**. 

The Likelihood Ratio ($LR$) statistic evaluates whether the number of observed out-of-sample breaches ($N$) matches the theoretical significance level ($\alpha$) over a sample size ($T$):

$$LR_{POF} = -2 \ln \left[ \left(\frac{1-\alpha}{1-\hat{p}}\right)^{T-N} \left(\frac{\alpha}{\hat{p}}\right)^N \right] \sim \chi^2(1)$$

Where $\hat{p} = N/T$ is the empirical violation rate. Models are rejected if the $p\text{-value} < 0.05$.

---

## 5. Key Findings & Insights

* **The Normality Trap:** Parametric VaR systematically underprojects risk at the 99% confidence level due to the fat-tailed (leptokurtic) distribution of asset returns (especially crypto and equities). It exhibits a statistically significant rate of failure under Kupiec validation at 99%.
* **Tail Robustness:** Historical Simulation and Monte Carlo models pass the Kupiec backtest across both confidence horizons, validating their capacity to capture non-linear extreme tail risks.

---

## 6. Repository Structure

```text
├── Var engine.ipynb          # Core Jupyter Notebook (Clean, Documented)
├── 03_historical_var.png    # Output Visualizations (Dark Theme)
├── var_engine_brief.docx    # Initial Project Specifications
└── README.md                # Project Overview
