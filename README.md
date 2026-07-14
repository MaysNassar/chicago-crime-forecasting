
# **Chicago Crime Forecasting** 🔍

Time series forecasting of Chicago crime incidents (2001–2022) 
to support law enforcement resource allocation for the next 
6 months.

---

## Overview

This project builds SARIMA/ARIMA forecasting models for two crime types — **Theft** and **Narcotics** — using 22 years of Chicago Police Department incident data. The goal is to forecast monthly crime counts 6 months into the future and provide actionable recommendations to law enforcement stakeholders.

---

## Dataset

- **Source:** Chicago Crime Data 2001–2022
- **Size:** 7.7 million incidents
- **Key columns:** Date, Primary Type, Location Description, 
  Arrest, District
- **Granularity:** Transformed to monthly counts via resampling

---

## Methodology

### Theft
| Step | Decision | Justification |
|------|----------|---------------|
| Granularity | Monthly (`resample('ME').size()`) | Per assignment spec |
| Decomposition | Additive | Seasonal amplitude constant across trend |
| Stationarity | ADF p=0.585 → non-stationary | d=1 required |
| Seasonal differencing | D=1 | ACF showed slow decay at lags 12, 24, 36 — overriding nsdiffs (D=0) |
| Final model | SARIMA(1,1,1)(1,1,1)[12] | Best on all test metrics vs auto_arima |

### Narcotics
| Step | Decision | Justification |
|------|----------|---------------|
| Training data | Post-2015 only | 90% structural decline 2001–2022 due to CPD policy changes — full-series models produced R² < -6 |
| Stationarity | ADF p=0.215 → non-stationary | d=1 required |
| Final model | ARIMA(0,1,1)(0,0,1)[12] | Manual SARIMA(1,1,1)(1,0,1,12) failed to converge on full recent series |

---

## Model Comparison

### Theft
| Model | MAE | MAPE | R² |
|-------|-----|------|----|
| Manual SARIMA(1,1,1)(1,0,1,12) | 237.0 | 4.82% | 0.524 |
| **Manual SARIMA(1,1,1)(1,1,1,12)** | **177.6** | **3.57%** | **0.682** |
| Auto ARIMA(1,1,0)(1,0,2,12) | 211.2 | 4.27% | 0.587 |

### Narcotics (post-2015 data)
| Model | MAE | MAPE | R² |
|-------|-----|------|----|
| **Manual SARIMA(1,1,1)(1,0,1,12)** | **25.9** | **6.60%** | **-0.517** |
| Auto ARIMA(1,1,1)(0,0,0,12) | 46.1 | 13.01% | -1.594 |

---

## Key Findings

| Metric | Theft | Narcotics |
|--------|-------|-----------|
| Forecast (Jun 2023) | ~5,035 incidents | ~349 incidents |
| Net change (Jan→Jun) | +1,010 | +17 |
| Percent change | +25.10%* | +5.22% |

*Theft's 25% change reflects seasonal variation (winter trough 
→ summer peak), not a genuine upward trend.

---

## Forecast Visualizations

### 6-Month Crime Forecast: Jan–Jun 2023
<img width="1376" height="508" alt="تنزيل (1)" src="https://github.com/user-attachments/assets/75a9a3a8-76ac-40cf-9433-d7c3b30b38e1" />
The 6-month forecast (January–June 2023) points to one clear priority: Theft.

Theft is expected to rise ~25% from January to June, climbing from ~4,024 to ~5,000 incidents/month. This is a predictable seasonal surge that happens every summer. Recommendation: begin shifting patrol resources to high-theft areas in April–May, before the peak hits.

Narcotics is forecasted to remain flat at ~330–370 incidents/month. No reallocation of Narcotics resources is warranted based on this forecast. Note: the Narcotics model carries wide uncertainty and should be treated as a short-term stability signal only.




### Theft: Forecast vs Actual (Test Period)
<img width="954" height="398" alt="تنزيل" src="https://github.com/user-attachments/assets/646d54a4-2bab-49bf-b094-8c17aef8ad9d" />


---
🧭 Strategic Takeaway
Policymakers should focus resources where the data is strongest: theft prevention in spring/summer. Avoid overreacting to noisy narcotics data. Forecasts should guide decisions but be combined with on-the-ground intelligence for balanced, effective crime prevention.
## Recommendation

**Prioritize Theft resources heading into summer 2023.** The 
seasonal surge is predictable — patrol resources should be 
shifted to high-theft areas in April–May, before the peak hits.

**Narcotics resources should remain stable.** The flat forecast 
signals short-term stability, but the model carries wide 
uncertainty and should not be used for long-range planning.

---

## Tools & Libraries

![Python](https://img.shields.io/badge/Python-3.12-blue)
![pandas](https://img.shields.io/badge/pandas-2.2-lightgrey)
![statsmodels](https://img.shields.io/badge/statsmodels-0.14-lightgrey)
![pmdarima](https://img.shields.io/badge/pmdarima-2.1-lightgrey)

- **pandas** — data loading, resampling, time series handling
- **statsmodels** — SARIMA/ARIMA modeling
- **pmdarima** — auto_arima parameter search
- **matplotlib / seaborn** — visualization

---
