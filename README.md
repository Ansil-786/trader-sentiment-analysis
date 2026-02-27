# Trader Performance vs Market Sentiment
**Primetrade.ai — Data Science Intern Assignment**  
Author: S Ansil | Date: 27 February 2026

---

## Overview

This project analyzes how Bitcoin market sentiment (Fear/Greed Index) influences trader behavior and performance on the Hyperliquid platform, using 211,218 historical trade records across 32 unique traders.

---

## Setup & How to Run

### Requirements
```
pandas
numpy
matplotlib
seaborn
```

Install dependencies:
```bash
pip install pandas numpy matplotlib seaborn
```

### Data Files Required
Place these two CSV files in the same directory as the notebook:
- `fear_greed_index.csv` — Bitcoin Fear/Greed index (2,644 rows)
- `historical_data.csv` — Hyperliquid trade history (211,224 rows)

### Run
```bash
jupyter notebook Tradingassignment_final.ipynb
```
Run all cells top-to-bottom. Charts will be saved as PNG files in the same directory.

---

## Methodology

### Data Preparation
- Loaded both datasets; verified zero missing values and zero duplicates
- Converted trade timestamps using `dayfirst=True` (format: `DD-MM-YYYY HH:MM`)
- Aligned both datasets at daily granularity via a left join on `date`
- Dropped 6 unmatched rows (< 0.003% of data) — no meaningful impact

### Metrics Engineered
| Metric | Definition |
|---|---|
| Win Flag | 1 if `Closed PnL > 0`, else 0 |
| Win Rate | Mean of win flags per trader |
| Avg Trade Size (USD) | Mean `Size USD` per trader |
| Trades per Day | `trade_count / unique_days` |
| Long Ratio | `BUY trades / total trades` |

### Trader Segments Defined
| Segment | Criteria |
|---|---|
| High Performer | Top 25% by total PnL |
| Frequent Trader | Trades/day > median |
| Consistent Winner | Win rate ≥ 55% |

---

## Key Findings

### 1. Sentiment materially impacts PnL
- Extreme Greed days yield ~67.9 USD mean PnL per trade vs ~34.5 USD on Extreme Fear — nearly **2×**
- Win rates peak during Extreme Greed; drawdown risk is highest on Fear days

### 2. Traders behave differently across sentiment regimes
- **Fear** → higher trade frequency (over-trading), smaller positions
- **Greed** → larger position sizes, slight short bias (profit-taking / fading euphoria)
- **Extreme Fear** → mild contrarian long bias (~51% BUY)

### 3. Top traders are sentiment-resilient
- High Performers maintain positive PnL across **every** sentiment regime including Extreme Fear
- "Others" only generate meaningful profit during Greed; they barely break even on Fear days
- Consistent Winners (WR ≥ 55%) outperform across all regimes

---

## Strategy Recommendations

### Strategy 1 — Sentiment-Gated Position Sizing
> *"Size up during Greed; size down during Fear."*

| Sentiment | Action | Segment |
|---|---|---|
| Extreme Greed (>75) | Scale size 1.5× | High Performers |
| Fear (<40) | Reduce size to 0.5–0.7× | All |
| Extreme Fear (<25) | Minimum size; contrarian longs only | High Performers only |

For **Others / Inconsistent traders**: add a hard rule — no new positions when the Fear/Greed Index is below 30.

### Strategy 2 — Frequency Discipline by Regime
> *"Trade less, not more, when the market is fearful."*

- Data shows traders over-trade on Fear days (high frequency, low PnL per trade)
- **Rule:** Cap daily trade quota at 50% of Greed-day average when index < 40
- **For Frequent traders:** Implement a sentiment-linked daily quota — strict on Fear, relaxed on Greed

---

## Output Charts
| File | Description |
|---|---|
| `chart_pnl_by_sentiment.png` | PnL distribution & mean PnL by sentiment |
| `chart_winrate_by_sentiment.png` | Win rate % by sentiment |
| `chart_trade_freq.png` | Avg trades/day by sentiment |
| `chart_longshort.png` | Long ratio by sentiment |
| `chart_position_size.png` | Avg position size (USD) by sentiment |
| `chart_seg_hp_sentiment.png` | High Performer vs Others across sentiment |
| `chart_seg_freq_sentiment.png` | Frequent vs Infrequent across sentiment |
| `chart_seg_wr_sentiment.png` | Consistent Winner vs Inconsistent across sentiment |
| `chart_heatmap_summary.png` | Summary heatmap: segment × sentiment |
