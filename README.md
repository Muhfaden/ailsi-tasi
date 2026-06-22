# AILSI — AI Liquidity Stress Index for the Saudi Equity Market (TASI)

A point-in-time, explainable, liquidity-aware **stress and tail-risk monitor** for Saudi
equities, grounded in the dollar-peg transmission mechanism, validated against established
stress indices, and tested for decision-usefulness through a cost-aware overlay backtest.

> **Not** a price predictor. AILSI is a risk-intelligence layer: it converts many global and
> Saudi macro-financial signals into one transparent 0–100 stress score, forecasts it, explains
> its drivers, and quantifies the downside risk to a hypothetical \$1M TASI position.

## The idea in one paragraph

Global financial-conditions indices (Chicago Fed NFCI, OFR FSI, the bank FCIs) ignore the Saudi
market, while local quant work stops at price forecasting. Yet because the riyal is pegged to the
US dollar (SAR 3.75 since 1986), SAMA effectively imports US monetary policy — so Fed tightening,
a stronger dollar, and widening global credit spreads transmit into Saudi funding conditions
almost mechanically. Layer oil-fiscal dynamics and post-2019 foreign-flow sensitivity on top, and
TASI is a market whose stress is heavily *imported* yet unmonitored by any purpose-built,
transparent, liquidity-aware gauge. AILSI fills that gap and proves the signal is worth acting on.

## Build steps (one notebook, grown over commits)

The whole project lives in `AILSI_TASI.ipynb`, built incrementally:

| Phase | Commit | Contents |
|-------|--------|----------|
| **PULL**    | 1 | Scaffold, reproducible FRED + yfinance ingest, raw cache, coverage map |
| **PROCESS** | 2 | Calendar align, monthly resample, feature engineering, liquidity proxies, point-in-time lagging, outliers/normalisation, 80/20 chronological split |
| **ANALYSE** | 3 | EDA: descriptive stats, correlations, rolling views, stress-event timeline |
|             | 4 | Build the AILSI (0–100) + TAVIX volatility gauge; validate vs known stress episodes |
| **AI**      | 5 | ARIMA, GARCH→VaR + CPI-stressed VaR, structural breaks, CPI decomposition |
|             | 6 | XGBoost (walk-forward CV + tuning), baselines, SHAP, calibration |
|             | 7 | Overlay backtest (economic value), benchmark validation, dashboard |

## How to run

- **Google Colab (recommended):** open `AILSI_TASI.ipynb`, *Runtime → Run all*. It pulls live data
  from FRED (no API key) and Yahoo Finance and caches it to `data/raw/`.
- **Locally:** `pip install -r requirements.txt`, then run the notebook. Same live-first behaviour.
- **Offline / CI:** if neither the network nor a cache is available, the notebook drops to a clearly
  labelled **synthetic** dataset so the pipeline still executes end-to-end. Synthetic runs print a
  loud warning and tag every artefact — never mistake them for real results.

## Repository layout

```
ailsi-tasi/
├── AILSI_TASI.ipynb     # the single, growing project notebook
├── README.md
├── requirements.txt
├── data/
│   ├── raw/             # cached raw pulls (parquet) — re-pullable, git-ignored
│   └── processed/       # cleaned, feature-engineered tables
└── artefacts/           # figures, model outputs, the index series
```

## Data sources

- **FRED** (St. Louis Fed) — global macro/financial series, via the public CSV endpoint (no API key).
- **Yahoo Finance** (`yfinance`) — `^TASI` and selected Saudi tickers.

Data choices are deliberately flexible; each is justified on economic grounds in the notebook.

## Honest scope

AILSI is a **nowcast + short-horizon early-warning** tool, not a crisis predictor. Its value is
timeliness, explainability, liquidity-awareness, and proven decision-usefulness — with limitations
(modest monthly sample, few independent stress regimes, latest-vintage macro lagged by release
delay rather than true ALFRED vintages, model-based volatility rather than option-implied) stated
plainly in the report.
