# Time Series Analysis — Capstone Project 2026
**Consulting & Analytics Club, IIT Guwahati**

---

Name: Prem Kadam, Roll no: 250123050 
Indian Institute of Technology, Guwahati

---

> Data-driven stock price forecasting and virtual portfolio trading on StockGro using ARIMA, Facebook Prophet, and LSTM models.

---

## Project Overview

This project builds a complete time series forecasting pipeline for five NSE-listed stocks. Models are trained, validated, and used to allocate a virtual portfolio of **₹10,00,000** on the StockGro platform during the May 14–15, 2026 trading window.

---

## Repository Structure

```
├── capstone_notebook.ipynb  ← Main notebook (fully run with outputs)
├── README.md                ← This file
├── dashboard.html           ← HTML Dashboard displaying results
└── report.pdf               ← Final 10-page report
```

---

## Stock Universe

| Stock | Ticker | Sector |
|-------|--------|--------|
| HDFC Bank | HDFCBANK.NS | Banking |
| Infosys | INFY.NS | IT |
| Sun Pharma | SUNPHARMA.NS | Pharma |
| Hindustan Unilever | HINDUNILVR.NS | FMCG |
| Maruti Suzuki | MARUTI.NS | Auto |

---

## Methodology

### Two-Phase Approach

**Phase 1 — Validation**
- Train: Jan 2021 → Dec 2025 (1,236 days)
- Test: Jan 2026 → Mar 2026 (60 days)
- Purpose: Evaluate model accuracy out-of-sample

**Phase 2 — Final Forecast**
- Re-fit all models on full data: Jan 2021 → May 13, 2026 (1,324 days)
- Forecast: Day 1 = Thu 14 May 2026 | Day 2 = Fri 15 May 2026

---

## Models Used

### ARIMA
- Orders selected via AIC grid search (p∈{0..3}, d=1 fixed from ADF, q∈{0..3})
- Best orders: HDFCBANK (3,1,3) · INFY (0,1,0) · SUNPHARMA (3,1,3) · HINDUNILVR (0,1,0) · MARUTI (2,1,0)
- Avg validation MAPE: **9.29%**

### Facebook Prophet
- Weekly + yearly seasonality, changepoint_prior_scale = 0.05
- Explicit date passing to avoid NSE Saturday trading day shape mismatch
- Avg validation MAPE: **9.03%**

### LSTM
- Architecture: LSTM(64) → Dropout(0.2) → LSTM(32) → Dropout(0.2) → Dense(16) → Dense(1)
- Lookback window: 60 trading days
- Early stopping: patience = 10
- 5-day autoregressive forecast loop
- Avg validation MAPE: **2.39%** ← Best model

### Ensemble
- Simple average of ARIMA + Prophet + LSTM forecasts
- Used for final portfolio construction

---

## Portfolio Construction

### Strategy A — Forecast-Guided
Stocks ranked by predicted 5-day return from ensemble.
Weight proportional to positive predicted return.

### Strategy B — Volatility-Aware
Weights inversely proportional to GARCH(1,1) Day-1 forecasted volatility:
`w_i = (1/σ_i) / Σ(1/σ_j)`

### Final Portfolio
50% Strategy A + 50% Strategy B (normalised)

| Stock | Weight | Shares | Invested |
|-------|--------|--------|----------|
| HDFCBANK | 25.98% | 358 | ₹2,72,488 |
| INFY | 16.00% | 185 | ₹2,00,031 |
| SUNPHARMA | 11.55% | 60 | ₹1,10,884 |
| HINDUNILVR | 15.07% | 58 | ₹1,29,990 |
| MARUTI | 31.40% | 22 | ₹2,85,042 |
| **Total** | | | **₹9,98,435** |

---

## Results

### Model Performance (Validation — Jan–Mar 2026)

| Model | Avg MAPE (%) | Avg RMSE | Dir. Accuracy (%) |
|-------|-------------|----------|-------------------|
| ARIMA | 9.29 | 570.7 | 22.0 |
| Prophet | 9.03 | 621.5 | 51.5 |
| **LSTM** | **2.39** | **170.8** | **52.9** |

### StockGro Trading Results (May 14–15, 2026)

| Stock | D2 Pred | D2 Actual | Dir Correct? | P&L (₹) |
|-------|---------|-----------|-------------|---------|
| HDFCBANK | ₹783.96 | ₹767.50 | ✅ Yes | 2,276.88 |
| INFY | ₹1,163.90 | ₹1,119.00 | ✅ Yes | 6,983.75 |
| SUNPHARMA | ₹1,786.84 | ₹1,878.20 | ❌ No | 1,808.40 |
| HINDUNILVR | ₹2,275.05 | ₹2,272.20 | ✅ Yes | 1,797.42 |
| MARUTI | ₹13,973.88 | ₹13,221.00 | ✅ Yes | 5,820.32 |

**Total P&L: ₹18,686.77 | Return: 1.872% | Directional Accuracy: 80% (4/5)**
**StockGro Platform Return: +0.60% | ₹5,998.08**

---

## How to Run

### 1. Open in Google Colab
Upload `capstone_notebook.ipynb` to [Google Colab](https://colab.research.google.com)

### 2. Install dependencies
The first cell handles this automatically:
```python
!pip install yfinance prophet statsmodels scikit-learn tensorflow arch seaborn matplotlib
```

### 3. Run all cells
```
Runtime → Restart & Run All
```
⏱️ LSTM training takes ~15–20 minutes.

### 4. Fill Task 8 after trading
After StockGro trading closes, fill actual prices in Task 8:
```python
actual_prices = {
    'HDFCBANK':   [day1_close, day2_close],
    'INFY':       [day1_close, day2_close],
    'SUNPHARMA':  [day1_close, day2_close],
    'HINDUNILVR': [day1_close, day2_close],
    'MARUTI':     [day1_close, day2_close],
}
```

---

## Dependencies

```
yfinance >= 0.2.0
prophet
statsmodels
scikit-learn
tensorflow / keras
arch
seaborn
matplotlib
pandas
numpy
```

---

## ⚠️ Important Notes

- **No data leakage**: MinMaxScaler fitted on training data only
- **Two datasets**: Validation split (Dataset A) and full refit (Dataset B) are separate
- **LSTM non-determinism**: Rerunning may produce slightly different outputs due to random weight initialisation
- **yfinance compatibility**: MultiIndex column flattening and timezone stripping applied for newer yfinance versions

---

## 📁 Deliverables

- [x] `capstone_notebook.ipynb` — Complete notebook with all outputs
- [x] `report.pdf` — 10-page detailed report
- [ ] Optional dashboard

---

## 🏅 Verification
This project was submitted to and verified by the 
Consulting & Analytics Club, IIT Guwahati — May 2026.
