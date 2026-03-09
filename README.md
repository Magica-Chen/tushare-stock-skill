# Tushare Stock Skill

A reusable Tushare skill for A-share stock data access and analysis.

## Overview

This repository packages a Tushare-based skill focused on China A-share equities. It supports natural-language routing to stock data endpoints, explicit endpoint fetches, fundamental analysis, and trading-observation workflows.

The skill is designed to:

- query stock master data, daily/weekly/monthly bars, adjusted bars, minute data, daily basic metrics, and a broad set of stock-reference datasets
- perform stock-level analysis across valuation, financial quality, growth, trend, risk, and trading observation
- provide technical indicator analysis including moving averages, momentum, RSI, KDJ, Bollinger Bands, and MACD
- recognize access gating such as point thresholds and extra-permission requirements

## Requirements

- Python 3
- A valid `TUSHARE_TOKEN`
- Python packages listed in [`requirements.txt`](requirements.txt)

## Quick Start

Install dependencies:

```bash
pip install -r requirements.txt
```

Provide your token through one of these methods:

- export `TUSHARE_TOKEN` in the environment
- set `TUSHARE_STOCK_ENV_FILE` to an external env file containing `TUSHARE_TOKEN=...`

The skill does not implicitly scan home-directory config files for credentials. If you prefer file-based credentials, point `TUSHARE_STOCK_ENV_FILE` to a file you control.

Run a natural-language request:

```bash
python scripts/tushare_stock.py run --text "贵州茅台近一年每日指标"
```

Run an explicit endpoint fetch:

```bash
python scripts/tushare_stock.py fetch --endpoint daily_basic --param ts_code=600519.SH --param start_date=20250101 --param end_date=20250301
```

Run a stock analysis request:

```bash
python scripts/tushare_stock.py analyze --text "分析贵州茅台的估值、财务质量和趋势"
```

## Repository Layout

- [`SKILL.md`](SKILL.md): skill instructions and usage guidance
- [`scripts/tushare_stock.py`](scripts/tushare_stock.py): main CLI entry point
- [`scripts/trading_analysis.py`](scripts/trading_analysis.py): technical indicator registry and calculations
- [`scripts/build_catalog.py`](scripts/build_catalog.py): catalog builder from official docs
- [`references/stock_endpoints.json`](references/stock_endpoints.json): machine-readable endpoint catalog
- [`references/stock_endpoints.md`](references/stock_endpoints.md): human-readable endpoint summary

## Notes

- User-facing outputs are designed to be in Simplified Chinese.
- The skill avoids calling `ts.set_token(...)` so it does not intentionally write a token cache file.
- The published repository excludes local cache files and local runtime-specific configuration.

## Security Notes

- Required environment variables and runtime expectations are declared in the `SKILL.md` frontmatter metadata.
- `run`, `fetch`, and `analyze` call Tushare network endpoints through the official client library.
- `build_catalog.py` fetches Tushare documentation pages from `tushare.pro` to rebuild the local endpoint catalog.
- The skill reads only the declared token-related environment variables and an explicitly provided env file path if `TUSHARE_STOCK_ENV_FILE` is set.
