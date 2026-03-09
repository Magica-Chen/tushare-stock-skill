---
name: tushare-stock
description: Specialized Tushare skill for China A-share stock data, stock analysis, and trading observation.
version: 1.0.3
homepage: https://github.com/Magica-Chen/tushare-stock-skill
metadata:
  openclaw:
    emoji: "📈"
    homepage: https://github.com/Magica-Chen/tushare-stock-skill
    requires:
      env:
        - TUSHARE_TOKEN
      bins:
        - python3
    optional:
      env:
        - TUSHARE_STOCK_ENV_FILE
        - TUSHARE_POINTS
        - TUSHARE_STOCK_CACHE_DIR
      pythonPackages:
        - pandas
        - tushare
        - requests
        - beautifulsoup4
      network:
        - api.waditu.com
        - tushare.pro
    primaryEnv: TUSHARE_TOKEN
---

# Tushare Stock Skill

A specialized Tushare skill for China A-share stock data retrieval, stock analysis, and trading observation.

## Use This Skill When

Use this skill for Tushare-powered China A-share workflows, including:

- stock master data and listings
- daily, weekly, monthly, adjusted, realtime, and minute-market data
- daily basic metrics and valuation indicators
- financial statements, forecasts, dividends, audits, and financial indicators
- major shareholder, pledge, repurchase, unlock, block trade, and holder-count datasets
- broker forecasts, chip-distribution data, technical-factor data, CCASS, and AH comparison data
- margin trading and securities lending datasets
- money-flow datasets
- leaderboard, limit-up/down, THS, DC, TDX, and KPL theme and sector datasets
- stock-level valuation, financial-quality, growth, trend, shareholder-activity, and risk analysis
- trading-observation requests such as price/volume structure, money flow, leaderboard behavior, moving averages, momentum, RSI, KDJ, Bollinger Bands, and MACD

Do not use this skill for ETF, index, fund, futures, options, macro, or news workflows.

## What It Provides

- natural-language routing across a broad Tushare A-share stock endpoint set
- explicit endpoint access for deterministic data pulls
- stock-level analysis covering valuation, financial quality, growth, trend, and risk
- trading-observation workflows covering price/volume structure, money flow, leaderboard activity, and technical indicators
- built-in handling for point thresholds and extra-permission gates

## Marketplace Positioning

This package is intentionally narrower and deeper than general-purpose Tushare wrappers. It focuses on China A-share stock workflows only, with stronger stock-analysis and trading-observation behavior instead of trying to cover every asset class or macro dataset in one package.

## Main Entry Point

Run the natural-language entry point with a Python 3 environment that has the required dependencies installed:

```bash
python scripts/tushare_stock.py run --text "<request>"
```

The script will:

- read `TUSHARE_TOKEN` from the environment
- read `TUSHARE_TOKEN` from a file only when `TUSHARE_STOCK_ENV_FILE` is explicitly provided
- act as the preferred entry point for Tushare access instead of ad hoc `import tushare as ts` snippets
- resolve stock names to `ts_code` where possible
- choose the best-fit Tushare stock endpoint from natural-language requests
- normalize common parameters such as date windows, quarter references, and minute frequencies
- explicitly gate endpoints that require more points or extra permissions
- switch to integrated analysis for requests about analysis, valuation, fundamentals, trend, or risk
- switch to trading observation for requests about trading setup, technical analysis, moving averages, momentum, RSI, KDJ, Bollinger Bands, or MACD
- use a fast trading-observation mode by default and only perform deeper leaderboard scans when explicitly requested
- return structured JSON with raw data first for query workflows and concise conclusions plus supporting data for analysis workflows
- avoid calling `ts.set_token(...)` so it does not intentionally create a local token cache file

## Installation

Install Python dependencies before using the skill:

```bash
pip install -r requirements.txt
```

Required environment variable:

- `TUSHARE_TOKEN`: required for all data and analysis commands

Optional environment variables:

- `TUSHARE_STOCK_ENV_FILE`: optional path to an env file that contains `TUSHARE_TOKEN`
- `TUSHARE_POINTS`: optional override for your current Tushare points balance; defaults to `5120`
- `TUSHARE_STOCK_CACHE_DIR`: optional cache directory; defaults to `/tmp/tushare_stock_skill`

## Security And Runtime Boundaries

- `run`, `fetch`, and `analyze` access Tushare network APIs.
- `build_catalog.py` fetches official Tushare documentation pages from `tushare.pro` to refresh the local endpoint catalog.
- The skill does not implicitly scan home-directory configuration files; file-based credentials must be provided explicitly through `TUSHARE_STOCK_ENV_FILE`.
- The skill reads only the declared environment variables and does not intentionally access unrelated credentials.

## Additional Commands

List the supported endpoint catalog:

```bash
python scripts/tushare_stock.py catalog
```

Call a specific endpoint directly:

```bash
python scripts/tushare_stock.py fetch --endpoint daily_basic --param ts_code=600519.SH --param start_date=20250101 --param end_date=20250301
```

Run explicit stock analysis:

```bash
python scripts/tushare_stock.py analyze --text "Analyze Kweichow Moutai valuation, financial quality, and trend"
```

List the built-in technical indicators:

```bash
python scripts/tushare_stock.py indicators
```

Refresh the endpoint catalog from the official documentation:

```bash
python scripts/build_catalog.py
```

## Output Behavior

- User-facing outputs are intended to be in Simplified Chinese.
- Query workflows return raw data first, followed by minimal explanation when useful.
- Analysis workflows return concise conclusions, key metrics, risk notes, and supporting data summaries.
- Trading-observation workflows prioritize trend, price/volume structure, money flow, leaderboard signals, and technical indicators.
- Fast mode prioritizes speed; deep mode adds leaderboard and institutional-seat scans when explicitly requested.
- Responses should clearly state the endpoints actually used and any point or permission constraints.
- When the request is ambiguous, ask a short clarification question in Chinese instead of guessing.

## Example Requests

- `Kweichow Moutai daily_basic metrics for the past year`
- `Institutional leaderboard activity in the last 30 days`
- `Analyze CATL valuation and growth`
- `Review China Merchants Bank fundamentals and trend`
- `What are the current risk signals for Kweichow Moutai?`
- `Show a trading observation view for Kweichow Moutai`
- `Show a fast trading observation view for Kweichow Moutai`
- `Run a deep trading observation on Kweichow Moutai with leaderboard and institutional-seat data`
- `Analyze CATL moving averages, RSI, and Bollinger Bands`
- `Technical analysis for Kweichow Moutai`

## References

- Machine-readable catalog: `references/stock_endpoints.json`
- Human-readable summary: `references/stock_endpoints.md`
- Technical-indicator registry: `scripts/trading_analysis.py`
- Official index: <https://tushare.pro/document/2?doc_id=14>

## Extension Model

- To add new technical indicators, extend `scripts/trading_analysis.py`.
- Register each new indicator with `@register_indicator(...)` so the main skill and `indicators` command pick it up automatically.
