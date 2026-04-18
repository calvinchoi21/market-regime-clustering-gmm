# Market Regime Clustering for Sector Rotation Strategy using GMM

A machine learning project that uses **Gaussian Mixture Models (GMM)** to identify distinct market regimes and dynamically allocate capital across U.S. sector ETFs based on regime-specific risk-adjusted performance.

## Overview

Financial markets cycle through different macro environments — bull runs, corrections, recoveries, and panics. This project detects these regimes using unsupervised clustering and constructs a dynamic sector rotation strategy that adapts allocations to the current market environment.

**Data coverage:** January 2000 – January 2025

## Methodology

### 1. Data Collection
- **Yahoo Finance:** S&P 500 (SPY), VIX, and 10 sector ETFs (XLK, XLF, XLE, XLV, XLY, XLU, XLI, XLB, IYR, XLP)
- **FRED API:** GDP, CPI, Unemployment Rate, Federal Funds Rate, Treasury yields (2Y, 10Y, 3M), Consumer Sentiment

### 2. Feature Engineering
- Technical indicators: RSI (14-period), SMA Ratio (50/200-day), Golden/Death Cross
- Macro signals: yield curve spreads (10Y–2Y, 10Y–3M), GDP and CPI momentum
- Sector daily returns and a synthetic cash return series

### 3. Dimensionality Reduction & Clustering
- **StandardScaler** normalization followed by **PCA** (retaining 95% variance)
- **GMM with 5 components** fit on the PCA-reduced feature space
- 4-day regime confirmation buffer to smooth noisy transitions

### 4. Regime Identification

| Regime | Label | Key Characteristics |
|--------|-------|---------------------|
| 0 | Moderate Expansion | Low VIX (~14), bullish technicals, slight yield curve inversion |
| 1 | Bullish Risk-On | Strong momentum, steep yield curve, early-to-mid expansion |
| 2 | Choppy Recovery | Elevated VIX (~25), weakening trend, supportive macro |
| 3 | Panic / Market Correction | VIX spike (~42), technical breakdown, fear-driven selling |
| 4 | Recovery and Stabilization | Rebuilding momentum, moderate VIX (~16), positive yield curve |

### 5. Sector Allocation Strategy
For each regime, sectors are ranked by their **annualized Sharpe ratio** and assigned to tiers:
- **Top 3 sectors:** 70% allocation (24%, 23%, 23%)
- **Next 3 sectors:** 30% allocation (10% each)
- **Remaining sectors:** 0% weight
- **Cash (3M T-bill):** only eligible during Panic/Correction regime

### 6. Backtest
Strategy backtested from 2000–2024 with **$100,000 initial capital**, compared against a buy-and-hold S&P 500 benchmark.

## Results

The GMM-based sector rotation strategy demonstrates:
- Well-diversified allocations with low correlation to any single sector ETF
- Dynamic adaptation to macro and sentiment regimes
- High overall correlation with the S&P 500 (expected given full equity allocation)

## Project Structure

```
├── Market Regime Clustering for Sector Rotation Strategy using GMM.ipynb  # Main analysis notebook
├── Market_Regime_Clustering_for_Sector_Rotation_Strategy_using_GMM.pdf    # Report
├── requirements.txt                                                        # Python dependencies
└── README.md
```

## Requirements

```
pip install -r requirements.txt
```

Key dependencies: `pandas`, `numpy`, `matplotlib`, `seaborn`, `yfinance`, `fredapi`, `scikit-learn`

## Setup

1. Clone the repository
2. Install dependencies: `pip install -r requirements.txt`
3. Obtain a free [FRED API key](https://fred.stlouisfed.org/docs/api/api_key.html) and set it in the notebook
4. Run the notebook top-to-bottom

> **Note:** The notebook requires a valid FRED API key. Replace the placeholder in the data-loading cell with your own key before running.

## Course Context

This project was completed as part of **ISYE 6740 – Computational Data Analysis** at Georgia Tech (OMSA program).
