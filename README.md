# Fear and Greed Index — Trading Performance Analysis
 
This notebook explores how the Crypto Fear and Greed Index relates to real trading outcomes. It merges market sentiment data with historical trade records, builds core performance KPIs, and produces a cost-of-trading breakdown by token size.
 
---
 
## Table of Contents
 
- [Project Overview](#project-overview)
- [Datasets](#datasets)
- [Setup and Requirements](#setup-and-requirements)
- [Notebook Structure](#notebook-structure)
  - [Part 1 — Data Preparation](#part-1--data-preparation)
  - [Part 2 — KPI Building](#part-2--kpi-building)
  - [Part 3 — Fear vs Greed Analysis](#part-3--fear-vs-greed-analysis)
  - [Part 4 — Trader Classification](#part-4--trader-classification)
  - [Part 5 — Cost of Trading by Token Size](#part-5--cost-of-trading-by-token-size)
- [Key Findings](#key-findings)
- [Recommended Actions](#recommended-actions)
---
 
## Project Overview
 
The goal of this analysis is to understand whether market sentiment (Fear vs Greed) affects trading behaviour and profitability, and whether the size of a trade influences its cost efficiency and win rate.
 
The analysis answers three core questions:
 
1. Do traders perform better during Fear periods or Greed periods?
2. Which traders are frequent and which are consistent winners?
3. Does a larger trade size lead to better or worse cost efficiency?
---
 
## Datasets
 
| File | Description |
|---|---|
| `fear_greed_index.csv` | Daily Fear and Greed Index scores with sentiment classification |
| `historical_data.csv` | Individual trade records including PnL, fees, token size, direction, and timestamps |
 
The two datasets are merged on a common `Date` column after timestamp alignment.
 
---
 
## Setup and Requirements
 
```bash
pip install pandas numpy matplotlib
```
 
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```
 
---
 
## Notebook Structure
 
### Part 1 — Data Preparation
 
- Loads both CSV files and checks their shape
- Identifies and reports missing values in each dataset
- Standardises the date format across both tables
  - `fear_greed_index.csv` date column is renamed to `Date` and parsed
  - `historical_data.csv` timestamps are parsed from `DD-MM-YYYY HH:MM` format and floored to the day
- Merges the two datasets on `Date` using an inner join
```python
merged_data = pd.merge(df1, df2, on='Date', how='inner')
```
 
---
 
### Part 2 — KPI Building
 
Five core performance metrics are calculated from the merged dataset:
 
| KPI | Description |
|---|---|
| Average daily PnL | Total PnL summed per account per day, then averaged across all days |
| Win rate | Percentage of trades where `Closed PnL > 0` |
| Average trade size | Mean value of `Size Tokens` across all trades |
| Average trades per day | Mean number of trades executed on any given day |
| Long/Short ratio | Number of long positions divided by number of short positions |
 
---
 
### Part 3 — Fear vs Greed Analysis
 
Trades are filtered to only those occurring on days classified as either `Fear` or `Greed`. Three metrics are then computed for each sentiment group:
 
- Average PnL per trade
- Win rate (percentage of profitable trades)
- Maximum drawdown (worst single trade loss)
Results are plotted as three separate bar charts — one per metric — to avoid scale distortion.
 
**Finding:** Trading performance is weaker during Greed periods. Win rate drops by approximately 3.6 percentage points and average PnL falls by around 21% compared to Fear periods. Drawdowns are also significantly larger during Greed, indicating higher risk exposure.
 
---
 
### Part 4 — Trader Classification
 
Traders are classified along two dimensions using median-based thresholds:
 
**Frequency classification**
 
- Traders who execute more trades than the median are labelled `Frequent`
- All others are labelled `Infrequent`
**Consistency classification**
 
- Traders whose win rate exceeds the median win rate are labelled `Consistent Winner`
- All others are labelled `Inconsistent`
Results are displayed as side-by-side bar charts with count labels on each bar.
 
---
 
### Part 5 — Cost of Trading by Token Size
 
Each trade's cost is calculated from the `Fee` column (fixed dollar amount per trade). Trades are bucketed into four equal-frequency groups by `Size Tokens`:
 
| Bucket | Meaning |
|---|---|
| Small | Bottom 25% of trades by token size |
| Medium | 25th to 50th percentile |
| Large | 50th to 75th percentile |
| Very Large | Top 25% of trades by token size |
 
The following metrics are calculated per bucket:
 
- Average fee paid
- Average gross PnL (before fees)
- Average net PnL (after fees)
- Fee as a percentage of trade size (`Size USD`)
- Total number of trades
- Win rate on net PnL
Four charts are produced:
 
1. Average fee by token size bucket
2. Gross PnL vs Net PnL side by side
3. Fee as a percentage of trade value (line chart)
4. Win rate by token size vs the 50% baseline
---
 
## Key Findings
 
**Sentiment**
 
- Fear periods produce better average PnL, higher win rates, and smaller drawdowns than Greed periods
- Traders should be more cautious when the market sentiment indicator reads Greed
**Cost of trading**
 
- Small trades carry the highest flat fee burden ($2.05 average vs $0.48 for Large trades)
- Fees have a negligible impact on absolute profit — the gap between gross and net PnL is minimal across all size buckets
- Very Large trades face a disproportionate cost spike: fee as a percentage of trade size jumps from 0.02% to 0.08%, a 4x increase
- Win rate improves as trade size increases (38% for Small, 43% for Very Large), but no bucket exceeds the 50% profitability baseline
---
 
## Recommended Actions
 
**1. Set a minimum trade size**
 
Large trades offer the best fee efficiency at $0.48 average cost and a 42.6% win rate. Small trades cost four times more in fees for worse outcomes. A minimum trade size policy would eliminate the Small bucket penalty.
 
**2. Investigate the Very Large fee spike**
 
Very Large trades jump from 0.02% to 0.08% cost as a percentage of trade value. Before scaling capital into large positions, the root cause needs to be identified — whether it is exchange slippage, a tiered fee structure, or liquidity constraints.
 
**3. Prioritise strategy review over fee optimisation**
 
No trade size bucket achieves a win rate above 50%. Fees are not the primary drag on performance. The focus should be on improving trade selection, particularly during Greed periods when outcomes are measurably worse.
 
---
 
## File Structure
 
```
.
├── fear_greed_index.csv        # Sentiment index data
├── historical_data.zip         # Trade history data
├── Fear_greed_analysis.ipynb   # Main analysis notebook
└── README.md                   # This file
```
 
