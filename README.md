# Tushare Stock Skill

A specialized Tushare skill for China A-share stock data retrieval, stock analysis, and trading observation.

## Overview

This repository packages a production-ready Tushare skill focused on China A-share equities. It supports natural-language routing to stock data endpoints, explicit endpoint fetches, stock-level analysis, and trading-observation workflows.

It is designed for users who want a single skill that can both retrieve structured market data and turn that data into practical stock insights.

## Listing Introduction

Tushare Stock Skill is a focused China A-share specialist. Instead of covering every market and macro surface that Tushare exposes, it concentrates on the workflows most useful for individual-stock research and trading observation: stock data retrieval, valuation and financial analysis, money-flow reading, leaderboard interpretation, and technical signal generation.

## Key Capabilities

- natural-language access to a broad Tushare A-share stock endpoint catalog
- explicit endpoint fetch mode for deterministic data retrieval
- valuation, financial-quality, growth, trend, and risk analysis
- trading-observation workflows covering price action, money flow, leaderboard activity, and technical indicators
- built-in handling for point thresholds and extra-permission requirements

## Why This Skill

- purpose-built for the Tushare stock-data surface rather than broad, unfocused financial tooling
- designed to be auditable: explicit credential model, clear network boundary, and limited runtime surface
- useful for both research-style requests and faster trading-observation workflows
- extensible technical-indicator registry for future customization

## Comparison With Other ClawHub Tushare Skills

Based on the public ClawHub listing summaries as of March 9, 2026:

- Compared with `lidayan/tushare-data`, which presents itself as a broad Tushare data wrapper for stocks, funds, futures, crypto, and fundamentals, this skill is more specialized. It is designed specifically for China A-share stock workflows and goes further on stock analysis and trading observation.
- Compared with `StanleyChanH/tushare-finance`, which highlights broad market coverage across A-shares, Hong Kong stocks, U.S. stocks, funds, futures, bonds, and macro indicators, this skill is narrower by design. It trades breadth for depth in A-share stock research and signal-oriented workflows.
- If you want one package for many asset classes and macro queries, those broader packages may fit better. If you want a sharper China A-share stock specialist with built-in valuation, risk, money-flow, leaderboard, and technical-analysis behavior, this package is the stronger fit.

## Coverage Highlights

- stock master data and listings
- daily, weekly, monthly, adjusted, realtime, and minute-market data
- daily basic metrics and valuation indicators
- financial statements, forecasts, dividends, audits, and financial indicators
- shareholder, pledge, repurchase, unlock, block trade, and holder-count datasets
- margin trading, securities lending, money flow, leaderboard, theme, and sector datasets
- technical indicators including moving averages, momentum, RSI, KDJ, Bollinger Bands, and MACD

## Requirements

- Python 3
- A valid `TUSHARE_TOKEN`
- Python packages listed in [`requirements.txt`](requirements.txt)

## Environment Variables

Required:

- `TUSHARE_TOKEN`: required for all Tushare API access

Optional:

- `TUSHARE_STOCK_ENV_FILE`: optional path to an env file containing `TUSHARE_TOKEN`
- `TUSHARE_POINTS`: optional override for access-gating logic; defaults to `5120`
- `TUSHARE_STOCK_CACHE_DIR`: optional cache directory; defaults to `/tmp/tushare_stock_skill`

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
python scripts/tushare_stock.py analyze --text "Analyze Kweichow Moutai valuation, financial quality, and trend"
```

List built-in indicators:

```bash
python scripts/tushare_stock.py indicators
```

## Repository Layout

- [`SKILL.md`](SKILL.md): skill instructions and usage guidance
- [`scripts/tushare_stock.py`](scripts/tushare_stock.py): main CLI entry point
- [`scripts/trading_analysis.py`](scripts/trading_analysis.py): technical indicator registry and calculations
- [`scripts/build_catalog.py`](scripts/build_catalog.py): catalog builder from official docs
- [`references/stock_endpoints.json`](references/stock_endpoints.json): machine-readable endpoint catalog
- [`references/stock_endpoints.md`](references/stock_endpoints.md): human-readable endpoint summary

## Operational Notes

- User-facing outputs are designed to be in Simplified Chinese.
- The skill avoids calling `ts.set_token(...)` so it does not intentionally write a token cache file.
- The published repository excludes local cache files and local runtime-specific configuration.

## Security Notes

- Required environment variables and runtime expectations are declared in the `SKILL.md` frontmatter metadata.
- `run`, `fetch`, and `analyze` call Tushare network endpoints through the official client library.
- `build_catalog.py` fetches Tushare documentation pages from `tushare.pro` to rebuild the local endpoint catalog.
- The skill reads only the declared token-related environment variables and an explicitly provided env file path if `TUSHARE_STOCK_ENV_FILE` is set.

## Positioning

This package is intended to be suitable for skill registries such as ClawHub: explicit about credentials, clear about network access, and focused on a narrow, auditable capability surface.

## Recommended Listing Summary

If you need a short marketplace summary, use:

> Specialized Tushare skill for China A-share stock data, valuation and risk analysis, money-flow reading, leaderboard interpretation, and trading observation with explicit credential and network boundaries.
