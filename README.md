# U.S. Equity Quantitative Screening System

A Python-based stock screening and analysis platform for U.S. equities, combining technical signal detection with fundamental analysis. This system scans 400+ S&P 500 constituents and ETFs across multiple timeframes to identify actionable trade signals, and evaluates company fundamentals through a 6-factor scoring model with AI-powered summaries.

All market data is sourced from the [Polygon.io](https://polygon.io/) and [Yahoo Finance](https://finance.yahoo.com/) APIs.

---

## Notebooks

### 1. `oversold_reversal_scanner_.ipynb`
**MACD-based oversold reversal scanner with backtesting**

Scans for bullish and bearish MACD divergence signals across 6 intraday and daily timeframes (30min, 1H, 2H, 3H, 4H, Daily), then validates signal quality through historical backtesting.

- **Cell 1.1 — Core Functions:** Custom implementations of EMA, BARSLAST (vectorized bars-since-condition), variable-window highest/lowest value functions, and MACD divergence signal computation
- **Cell 1.2 — Data Retrieval:** Polygon.io REST API wrapper with automatic pagination, intraday/daily data fetching, and OHLC resampling to build multi-timeframe data
- **Cell 1.3 — Concurrent Scanner:** Scans 400+ tickers using `ThreadPoolExecutor` (30 threads), detects buy/sell signals across all timeframes, and exports a pivot table to CSV
- **Cell 2.1 — Backtester:** Validates signals over 3 years of historical data, computing win rate, average return, max gain, max drawdown, and average days to peak for 3/5/8-day holding periods

### 2. `100_200_ema_rebound.ipynb`
**EMA-100/200 support-touch scanner (Daily & Weekly)**

Identifies stocks where the candle's lower wick touches the EMA but the closing price remains above it — a classic support confirmation signal.

- **Daily scan:** Scans 400+ tickers against EMA-100 and EMA-200 on daily charts
- **Weekly scan:** Same logic applied to weekly timeframe for longer-term trend confirmation
- **Ticker search:** Utility to check whether specific tickers appear in scan results
- Exports signals to CSV with checkmark indicators

### 3. `Intraday_RSI_Divergence_Scanner.ipynb`
**Real-time RSI divergence scanner with Telegram alerts**

Runs continuously during market hours (6:30 AM – 1:05 PM PT), scanning for RSI divergences on dual timeframes and pushing alerts via Telegram.

- **Dual-timeframe analysis:** 3-min bars (RSI-7) + 30-min bars (RSI-14)
- **Four divergence types detected:**
  - Regular Bullish — Price new low + RSI no new low → reversal
  - Regular Bearish — Price new high + RSI no new high → reversal
  - Hidden Bullish — Higher low + RSI new low → uptrend continuation
  - Hidden Bearish — Lower high + RSI new high → downtrend continuation
- **ETF confluence detection:** Cross-validates QQQ/SPY signals against UVXY/SOXS for confirmation
- **Telegram Bot integration:** Delivers automated market summaries in Chinese every 2 minutes during trading hours for quick mobile monitoring
- **Pivot-point based detection** with configurable window size

#### Telegram Alert Example
![Telegram Alert](IMG_3522.jpeg)

### 4. `SixFactorAnalyzer.py`
**AI-powered fundamental analysis toolkit**

Evaluates stocks across 6 financial dimensions with automated scoring, then generates an AI-written analysis summary in Chinese via OpenAI or Anthropic APIs.

- **6-Factor Scoring Model:**
  1. **Leverage / Debt Structure** — Debt/Assets, Debt/Equity, current ratio, interest coverage
  2. **Net Cash Position** — Cash + short-term investments minus total debt, with multi-year trend tracking
  3. **Operating Cash Flow** — OCF/Net Income ratio, FCF margin, cumulative cash conversion
  4. **Capital Allocation** — ROE, ROA, ROIC, shareholder returns (dividends + buybacks), reinvestment rate
  5. **Sustainable Earnings** — Core profit percentage, non-recurring items, margin stability (coefficient of variation)
  6. **Tangible Book Value** — Goodwill/Equity ratio, intangibles exposure, price-to-tangible book
- **Automated grading:** Weighted scoring across all 6 factors (0–100 scale), with letter grades (A–F)
- **Risk & highlight detection:** Automatically flags key risks and strengths
- **AI-powered summary:** Sends financial data to OpenAI (GPT-4o-mini) or Anthropic (Claude) to generate a concise analysis in Chinese
- **Rule-based fallback:** Generates analysis in Chinese without AI when no API key is available
- **Data source:** Yahoo Finance via yfinance

---

## Tech Stack

- **Language:** Python, R
- **Data Sources:** Polygon.io REST API, Yahoo Finance (yfinance)
- **AI Integration:** OpenAI API (GPT-4o-mini), Anthropic API (Claude)
- **Libraries:** Pandas, NumPy, pandas_ta, requests, schedule, pytz, yfinance
- **Concurrency:** `concurrent.futures.ThreadPoolExecutor`
- **Alerts:** Telegram Bot API
- **Environment:** Jupyter Notebook

## Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/equity-screening-system.git
   cd equity-screening-system
   ```

2. Install dependencies:
   ```bash
   pip install pandas numpy requests pandas_ta polygon-api-client schedule pytz yfinance openai anthropic
   ```

3. Add your API keys:
   - `POLYGON_API_KEY` — your Polygon.io key (in each notebook)
   - `TELEGRAM_TOKEN` and `TELEGRAM_CHAT_ID` — for intraday scanner alerts
   - `OPENAI_API_KEY` or `ANTHROPIC_API_KEY` — for AI-powered fundamental analysis (set as environment variables)

4. Open any notebook in Jupyter and run cells sequentially, or run the fundamental analyzer directly:
   ```bash
   python SixFactorAnalyzer.py
   ```

## Output

- Technical scan results are exported as CSV files to `scan_results/` and `ema_data/` directories
- Fundamental analysis can be exported as JSON via `analyzer.to_json()`

## Disclaimer

This project is for educational and personal research purposes only. It does not constitute financial advice. Use at your own risk.
