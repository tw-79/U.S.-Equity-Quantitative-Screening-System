# U.S. Equity Quantitative Screening System

A Python-based multi-timeframe stock screening platform for U.S. equities. This system scans 400+ S&P 500 constituents and ETFs to identify actionable trade signals through MACD divergence detection, EMA support analysis, and RSI divergence scanning.

All market data is sourced from the [Polygon.io](https://polygon.io/) REST API.

------------------------------------------------------------------------

## Notebooks

### 1. `oversold_reversal_scanner_.ipynb`

**MACD-based oversold reversal scanner with backtesting**

Scans for bullish and bearish MACD divergence signals across 6 intraday and daily timeframes (30min, 1H, 2H, 3H, 4H, Daily), then validates signal quality through historical backtesting.

-   **Cell 1.1 — Core Functions:** Custom implementations of EMA, BARSLAST (vectorized bars-since-condition), variable-window highest/lowest value functions, and MACD divergence signal computation
-   **Cell 1.2 — Data Retrieval:** Polygon.io REST API wrapper with automatic pagination, intraday/daily data fetching, and OHLC resampling to build multi-timeframe data
-   **Cell 1.3 — Concurrent Scanner:** Scans 400+ tickers using `ThreadPoolExecutor` (30 threads), detects buy/sell signals across all timeframes, and exports a pivot table to CSV
-   **Cell 2.1 — Backtester:** Validates signals over 3 years of historical data, computing win rate, average return, max gain, max drawdown, and average days to peak for 3/5/8-day holding periods

### 2. `100_200_ema_rebound.ipynb`

**EMA-100/200 support-touch scanner (Daily & Weekly)**

Identifies stocks where the candle's lower wick touches the EMA but the closing price remains above it — a classic support confirmation signal.

-   **Daily scan:** Scans 400+ tickers against EMA-100 and EMA-200 on daily charts
-   **Weekly scan:** Same logic applied to weekly timeframe for longer-term trend confirmation
-   **Ticker search:** Utility to check whether specific tickers appear in scan results
-   Exports signals to CSV with checkmark indicators

### 3. `Intraday_RSI_Divergence_Scanner.ipynb`

**Real-time RSI divergence scanner with Telegram alerts**

Runs continuously during market hours (6:30 AM – 1:05 PM PT), scanning for RSI divergences on dual timeframes and pushing alerts via Telegram.

-   **Dual-timeframe analysis:** 3-min bars (RSI-7) + 30-min bars (RSI-14)
-   **Four divergence types detected:**
    -   Regular Bullish — Price new low + RSI no new low → reversal
    -   Regular Bearish — Price new high + RSI no new high → reversal
    -   Hidden Bullish — Higher low + RSI new low → uptrend continuation
    -   Hidden Bearish — Lower high + RSI new high → downtrend continuation
-   **ETF confluence detection:** Cross-validates QQQ/SPY signals against UVXY/SOXS for confirmation
-   **Pivot-point based detection** with configurable window size
-   **Telegram alerts:** Sends a concise summary （in Chinese) every X minutes during market hours, including 0-Bar/1-Bar signal status across 3-min (RSI-7) and 30-min (RSI-14) timeframes, ETF confluence results (QQQ/SPY vs. UVXY/SOXS), and individual stock signals — all formatted in Chinese for quick at-a-glance review

------------------------------------------------------------------------

## Tech Stack

-   **Language:** Python
-   **Data Source:** Polygon.io REST API
-   **Libraries:** Pandas, NumPy, pandas_ta, requests, schedule, pytz
-   **Concurrency:** `concurrent.futures.ThreadPoolExecutor`
-   **Alerts:** Telegram Bot API
-   **Environment:** Jupyter Notebook

## Setup

1.  Clone the repository:

    ``` bash
    git clone https://github.com/yourusername/equity-screening-system.git
    cd equity-screening-system
    ```

2.  Install dependencies:

    ``` bash
    pip install pandas numpy requests pandas_ta polygon-api-client schedule pytz
    ```

3.  Add your API keys in each notebook:

    -   `POLYGON_API_KEY` — your Polygon.io key
    -   `TELEGRAM_TOKEN` and `TELEGRAM_CHAT_ID` — for the intraday scanner alerts

4.  Open any notebook in Jupyter and run cells sequentially.

## Output

Scan results are exported as CSV files to `scan_results/` and `ema_data/` directories, organized by date.

## Disclaimer

This project is for educational and personal research purposes only. It does not constitute financial advice. Use at your own risk.
