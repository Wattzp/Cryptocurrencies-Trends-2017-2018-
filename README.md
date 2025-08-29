# Cryptocurrencies Trends (2017-2018)
---
## Data Overview
  #### A paired snapshot of the top 1,300+ cryptocurrencies, captured on December 6, 2017 and January 6, 2018. Use these files to explore market-cap shifts, price volatility, and short-term momentum across the crypto ecosystem.


---
# Applying CRISP-DM to the Crypto Snapshots (Dec 2017 → Jan 2018)

Below we map each CRISP-DM phase to our paired crypto datasets (`coinmarketcap_06122017.csv` and `coinmarketcap_06012018.csv`).

## 1) Business Understanding

The primary goal is to understand how the top‐tier cryptocurrency market evolved over the one‐month window from December 6, 2017 to January 6, 2018.  

- Identify key questions  
  - Which coins gained or lost market share?  
  - How did volatility profiles shift across large vs. small caps?  
  - Can we detect early signals of emerging coins?  

- Define success criteria  
  - A clear ranking of month-over-month market-cap changes  
  - Volatility clusters that separate stable vs. high-risk assets  
  - Actionable insights for portfolio allocation or alerts  

## 2) Data Understanding

### Data Collection

We have two CSV snapshots, each with ~1,300 cryptocurrencies and these columns:  
- `id`, `name`, `symbol`, `rank`  
- `price_usd`, `market_cap_usd`, `24h_volume_usd`  
- `available_supply`, `total_supply`, `max_supply`  
- `percent_change_1h/24h/7d`, `last_updated`  

### Exploratory Data Analysis

- Use descriptive statistics (`.describe()`) on numeric fields to gauge ranges and medians.  
- Count missing values, especially in `max_supply` and `percent_change_*`.  
- Plot histograms of `price_usd`, `market_cap_usd`, and `percent_change_24h` to reveal skew and outliers.  
- Scatterplots of `market_cap_usd` vs. `24h_volume_usd` to identify liquidity anomalies.  

## 3) Data Preparation

### Data Cleaning

- Drop or impute rows where `market_cap_usd` ≤ 0 or missing.  
- Remove entries with null `max_supply` if needed, or fill via known coin specs.  
- Convert `last_updated` from UNIX timestamp to datetime.  
- Normalize text fields (`id`, `name`) for consistent merging.  

### Feature Engineering

- Add a `date` column (`2017-12-06` / `2018-01-06`) and concatenate into a single DataFrame.  
- Compute month-over-month return:  
  ```python
  delta = (cap_jan.market_cap_usd - cap_dec.market_cap_usd) / cap_dec.market_cap_usd
  ```
- Derive market-cap share (%) per coin each month.  
- Create volatility metrics (e.g., absolute 24h change, ratio of 7d/24h moves).  
- Flag “emerging” vs. “declining” coins based on custom thresholds.  

## 4) Data Development

Depending on desired insights, choose modeling approaches:

- Time series forecasting  
  - ARIMA or Prophet models on price_usd for Bitcoin & Ethereum  
- Clustering  
  - K-means on features like `market_cap_share`, `volatility_7d`, `volume_ratio` to segment coins  
- Classification  
  - Train a binary classifier to predict whether a coin’s market cap will grow > 20% next month  

## 5) Model Evaluation

- For forecasting models, compute RMSE and MAE on held-out data or via rolling windows.  
- For clustering, assess silhouette score and inspect cluster centroids for interpretability.  
- For classification, use precision, recall, and AUC to judge signal reliability.  
- Validate stability by back-testing on prior month pairs (e.g., Nov→Dec 2017).  

## 6) Model Deployment

- Package the final model or clustering logic into a reusable Python module.  
- Schedule a monthly ETL job:  
  1. Fetch new snapshot from CoinMarketCap API  
  2. Run preprocessing and feature pipeline  
  3. Generate leaderboard reports and trigger alerts for coins crossing growth thresholds  
- Expose results via:  
  - A dashboard (Plotly Dash or Streamlit) showing top movers and volatility clusters  
  - Automated email or Slack notifications for designated stakeholders.
