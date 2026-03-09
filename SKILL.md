---
name: tushare-stock
description: Tushare skill for China A-share stock data access, analysis, and trading observation.
version: 1.1.0
homepage: https://github.com/Magica-Chen/tushare-stock-skill
metadata:
  openclaw:
    emoji: "📈"
    homepage: https://github.com/Magica-Chen/tushare-stock-skill
    requires:
      env:
        - TUSHARE_TOKEN
        - TUSHARE_STOCK_ENV_FILE
        - TUSHARE_POINTS
        - TUSHARE_STOCK_CACHE_DIR
      bins:
        - python3
    primaryEnv: TUSHARE_TOKEN
---

# Tushare Stock Skill

A professional Tushare skill for China A-share data retrieval, stock analysis, and trading observation.

## Use This Skill When

Use this skill for Tushare-powered China A-share workflows, including:

- 股票基础资料和上市清单
- 日线、周线、月线、复权、实时、分钟行情
- 每日指标和估值类指标
- 财务报表、业绩预告、分红、审计、财务指标
- 十大股东、质押、回购、解禁、大宗交易、股东人数
- 券商盈利预测、筹码分布、技术因子、CCASS、AH 比价
- 融资融券、转融通
- 资金流向
- 龙虎榜、涨跌停、THS/DC/TDX/KPL 题材和板块数据
- 个股估值、财务质量、成长性、趋势、股东动作、风险提示等综合分析
- 偏交易观察的请求，例如量价、资金面、龙虎榜、均线、动量、RSI、KDJ、布林线、MACD

不要用于 ETF、指数、基金、期货、期权、宏观、新闻类请求。

## What It Provides

- natural-language routing across a broad Tushare A-share stock endpoint set
- explicit endpoint access for deterministic data pulls
- stock-level analysis covering valuation, financial quality, growth, trend, and risk
- trading-observation workflows covering price/volume structure, money flow, leaderboard activity, and technical indicators
- built-in handling for point thresholds and extra-permission gates

## Main Entry Point

使用已安装依赖的 Python 3 环境运行自然语言入口：

```bash
python scripts/tushare_stock.py run --text "<用户请求>"
```

脚本会：

- 从环境变量读取 `TUSHARE_TOKEN`
- 如果未直接设置 `TUSHARE_TOKEN`，则仅在显式提供 `TUSHARE_STOCK_ENV_FILE` 时才会从该文件读取 `TUSHARE_TOKEN`
- 所有 Tushare 请求都应优先调用本脚本，不要临时手写 `import tushare as ts` + `ts.pro_api()` 的独立片段
- 在可能时把股票名称解析为 `ts_code`
- 从自然语言中选择最合适的股票接口
- 规范化日期区间、季度表达、分钟频率等常见参数
- 对积分不足或需要额外权限的接口做显式拦截
- 对“分析/估值/基本面/趋势/风险”等分析型请求自动切到综合分析
- 对“交易观察/技术分析/均线/动量/RSI/KDJ/布林线/MACD”等请求自动切到交易观察
- 交易观察默认走快档，只看趋势、量价、资金流和技术指标，不逐日扫描龙虎榜
- 只有明确提到“龙虎榜 / 机构席位 / 游资 / 深度交易观察”时，才切到深档并补扫龙虎榜
- 返回结构化 JSON；查询类默认原始数据优先，分析类返回结论与关键支撑数据
- 技能不再调用 `ts.set_token(...)`，避免额外写入本地 token 缓存文件

## Security And Runtime Boundaries

- 运行 `run` / `fetch` / `analyze` 时会访问 Tushare API。
- 运行 `build_catalog.py` 时会抓取 Tushare 官方文档页面 `tushare.pro` 以刷新本地接口目录。
- 技能默认不再扫描用户家目录下的配置文件；如需使用文件型凭证，必须显式设置 `TUSHARE_STOCK_ENV_FILE`。
- 技能只会读取其声明的环境变量，不会主动读取其他无关凭证。

## Additional Commands

列出支持的接口目录：

```bash
python scripts/tushare_stock.py catalog
```

直接调用指定接口：

```bash
python scripts/tushare_stock.py fetch --endpoint daily_basic --param ts_code=600519.SH --param start_date=20250101 --param end_date=20250301
```

显式触发个股综合分析：

```bash
python scripts/tushare_stock.py analyze --text "分析贵州茅台的估值、财务质量和趋势"
```

查看当前内置技术指标目录：

```bash
python scripts/tushare_stock.py indicators
```

根据官方文档刷新接口目录：

```bash
python scripts/build_catalog.py
```

## Output Behavior

- 面向用户的说明一律使用简体中文。
- 查询类默认先给原始数据，再给必要的简短解释。
- 分析类默认给出简短结论、关键指标、风险提示和支撑数据摘要。
- 交易观察类优先输出趋势、量价、资金流、龙虎榜和技术指标信号。
- 快档交易观察优先保证速度；深档交易观察才补龙虎榜与机构席位扫描。
- 明确说明实际使用的接口和积分/权限限制。
- 请求不明确时，先用中文追问一句，不要盲猜。

## Example Requests

- `贵州茅台近一年每日指标`
- `最近30天龙虎榜机构交易`
- `分析宁德时代的估值和成长性`
- `看看招商银行基本面和趋势`
- `贵州茅台有什么风险提示`
- `看看贵州茅台交易观察`
- `看看贵州茅台快档交易观察`
- `深度看看贵州茅台交易观察，带龙虎榜和机构席位`
- `分析宁德时代均线、RSI 和布林线`
- `贵州茅台技术分析`

## References

- 机器可读目录：`references/stock_endpoints.json`
- 人类可读摘要：`references/stock_endpoints.md`
- 技术指标注册表：`scripts/trading_analysis.py`
- 官方索引：<https://tushare.pro/document/2?doc_id=14>

## Extension Model

- 想新增技术指标时，优先编辑 `scripts/trading_analysis.py`
- 新增一个计算函数并用 `@register_indicator(...)` 注册后，主技能和 `indicators` 命令会自动识别
