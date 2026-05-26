# ApocData · A-Share AI Data Skill

[![SafeSkill 50/100](https://img.shields.io/badge/SafeSkill-50%2F100_Use%20with%20Caution-orange)](https://safeskill.dev/scan/apocdata-apocdata-skill)
> Zero-auth, zero-dependency. Use `curl` to call 45+ A-share data endpoints
> (quotes / financials / fund flows / factors / announcements / macro).
> Compatible with Claude, ChatGPT, Qwen, Kimi, DeepSeek and any AI agent
> that supports tool calling.

The [`SKILL.md`](./SKILL.md) in this repository is a capability card that can
be loaded directly into AI agents, letting LLMs query A-share market data
and complete research tasks without any SDK.

---

## Table of Contents

- [Overview](#overview)
- [Data Service Platform](#data-service-platform)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
- [Endpoint Overview](#endpoint-overview)
- [Combined Analysis Example](#combined-analysis-example)
- [Editions & Capabilities](#editions--capabilities)
- [Notes & Compliance](#notes--compliance)

---

## Overview

ApocData focuses on AI-native access to China's A-share market, designed for
AI agents, quantitative research, investment content, and institutional
applications. We provide unified endpoints for quotes, financials, fund flows,
dragon-tiger boards, quantitative factors, macro indicators, and more.

---

## Data Service Platform

**Platform URL:** <https://data.tianqis.com>

The data service platform is the unified entry point for ApocData. It provides:

- **Open API (no auth)** — This is what the Skill connects to. No registration,
  no token required. Ideal for quick experiments and lightweight usage.
- **OpenAPI documentation & SDKs** — Python / TypeScript supported.
- **Platform editions (with API key)** — Higher quotas, deeper history,
  lower latency, and richer data fields. See [Editions & Capabilities](#editions--capabilities).

> The base URL used by this Skill: `https://data.tianqis.com/api/blade-dataplatform/open/data`

---

## Installation

### macOS / Linux

```bash
mkdir -p ~/.claude/skills/apocdata
curl -o ~/.claude/skills/apocdata/SKILL.md \
  https://raw.githubusercontent.com/ApocData/ApocData-skill/main/SKILL.md
```

### Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force -Path ~\.claude\skills\apocdata
Invoke-WebRequest `
  -Uri https://raw.githubusercontent.com/ApocData/ApocData-skill/main/SKILL.md `
  -OutFile ~\.claude\skills\apocdata\SKILL.md
```

### China users (Gitee mirror)

```bash
mkdir -p ~/.claude/skills/apocdata
curl -o ~/.claude/skills/apocdata/SKILL.md \
  https://gitee.com/apocdata/ApocData-skill/raw/main/SKILL.md
```

Restart Claude Code or Claude Desktop after installation. The Skill takes
effect automatically.

---

## Basic Usage

All endpoints are HTTP GET. Call them directly with `curl`:

```bash
BASE="https://data.tianqis.com/api/blade-dataplatform/open/data"

# Single stock quote
curl -s "$BASE/quote?symbol=000001"

# Stock basic info (incl. PE/PB/market cap)
curl -s "$BASE/stock?symbol=000001"
```

Full parameters, return fields, and example questions for each endpoint:
see [`SKILL.md`](./SKILL.md).

---

## Endpoint Overview

45 endpoints in total, grouped by data dimension:

### Real-time Quotes & K-line (7)

| Endpoint      | Description                                |
| ------------- | ------------------------------------------ |
| `quote`       | Latest change/volume/price for one stock   |
| `quotes`      | Batch quotes, up to 10 stocks              |
| `daily`       | Daily K history, ≤ 30 records              |
| `ranking`     | Market-wide gain/loss rankings             |
| `index-daily` | Index daily K                              |
| `hk-daily`    | Hong Kong daily K                          |
| `tech-factor` | Technical factors (MACD/KDJ/RSI/BOLL/MA)   |

### Fundamentals & Financials (5)

| Endpoint    | Description                              |
| ----------- | ---------------------------------------- |
| `stock`     | Stock basics (industry / cap / PE / PB)  |
| `financial` | Financials (ROE / revenue / net profit), ≤ 4 periods |
| `express`   | Earnings express reports                 |
| `dividend`  | Dividend and share allotment plans       |
| `cyq-perf`  | Chip distribution and profit ratio       |

### Shareholders & Governance (6)

| Endpoint        | Description                          |
| --------------- | ------------------------------------ |
| `holders`       | Top 10 shareholders / circulating    |
| `holder-number` | Historical shareholder count         |
| `share-float`   | Lock-up release records              |
| `repurchase`    | Share buyback plans and progress     |
| `block-trade`   | Block trade records                  |
| `survey`        | Institutional research visits        |

### Fund Flows (5)

| Endpoint      | Description                              |
| ------------- | ---------------------------------------- |
| `moneyflow`   | Stock fund flow and main net inflow      |
| `hsgt`        | HSGT (North/South bound) fund flow       |
| `sector-flow` | Sector / concept / regional fund flow    |
| `hk-hold`     | Stock holdings via HSGT                  |
| `margin`      | Margin trading and short selling summary |

### Dragon-Tiger & Sentiment (6)

| Endpoint           | Description                       |
| ------------------ | --------------------------------- |
| `dragon-tiger`     | Dragon-tiger list / stock history |
| `limit-list`       | Limit up / down / broken pools    |
| `limit-step`       | Consecutive limit-up ladder       |
| `hot-rank`         | EastMoney popularity ranking      |
| `hot-money`        | Famous hot-money directory        |
| `hot-money-detail` | Hot-money trade details           |

### Sectors & Concepts (4)

| Endpoint           | Description                  |
| ------------------ | ---------------------------- |
| `concepts`         | EastMoney concept directory  |
| `concept-stocks`   | Concept constituent stocks   |
| `ths-boards`       | THS industry / concept boards |
| `ths-board-stocks` | THS board constituents       |

### News & Announcements (2)

| Endpoint        | Description                              |
| --------------- | ---------------------------------------- |
| `news`          | Recent news (title / link / sentiment)   |
| `announcements` | Company announcements (AI summary + markdown) |

### Macro Economics (3)

| Endpoint           | Description                          |
| ------------------ | ------------------------------------ |
| `macro`            | Macro indicator history (GDP/CPI/PPI/PMI) |
| `macro/latest`     | Latest macro indicator values        |
| `macro/definition` | Macro indicator definitions          |

### Search & Utilities (5)

| Endpoint    | Description                            |
| ----------- | -------------------------------------- |
| `stocks`    | Search stocks by name / code           |
| `indexes`   | Search indexes by name / code          |
| `calendar`  | A-share trading calendar               |
| `st`        | ST / delisting risk status             |
| `factors`   | Quant factor registry (sanitized)      |

### Convertible Bonds (2)

| Endpoint            | Description                       |
| ------------------- | --------------------------------- |
| `convertible-bonds` | Convertible bond basic info       |
| `cb-price-chg`      | Conversion price change records   |

---

## Combined Analysis Example

When asked *"Analyze the valuation of 688017"*, the agent will call in sequence:

```bash
BASE="https://data.tianqis.com/api/blade-dataplatform/open/data"
curl -s "$BASE/stock?symbol=688017"          # PE/PB/market cap
curl -s "$BASE/quote?symbol=688017"          # current price
curl -s "$BASE/financial?symbol=688017"      # ROE/net profit
curl -s "$BASE/daily?symbol=688017&limit=30" # recent trend
```

---

## Editions & Capabilities

ApocData is organized into four editions to match different use cases.
All editions share the same API contract; capabilities are filtered by
edition whitelist. **This Skill defaults to the no-auth open endpoints.
Upgrade to a platform edition with API key for higher quotas and deeper data.**

### Edition Lineup

| SKU   | Edition      | Target Users                                          |
| ----- | ------------ | ----------------------------------------------------- |
| FREE  | Free         | Individuals / AI agent demos                          |
| PRO   | Professional | Investment research / content creators                |
| QUANT | Quant        | Quantitative research teams                           |
| ENT   | Enterprise   | Institutional embedding / private deployment          |

### Capability Comparison (selected)

| Capability         | Free        | Pro            | Quant   | Enterprise       |
| ------------------ | ----------- | -------------- | ------- | ---------------- |
| Daily calls        | 2,000       | 50,000         | 300,000 | Custom           |
| QPS limit          | 2           | 10             | 30      | Custom           |
| Snapshot latency   | 15 min      | 1 min          | ≤ 30s   | Custom (≤ 10s)   |
| Daily K depth      | 30 days     | 5 yrs + adj.   | Full    | Full             |
| Quant factors      | —           | 20             | Full    | Full + custom    |
| MCP Tools          | 8           | 14             | 18      | 18+ custom       |
| Availability SLO   | best-effort | 99.0%          | 99.5%   | 99.9% (contract) |
| Support            | Community   | Email 48h      | Email 24h | Dedicated channel + phone |

> All editions include OpenAPI docs, SDKs, status page, and usage dashboard.
> Full capability matrix and pricing details available upon request.

### Add-ons (stackable on existing editions)

| Add-on         | Name              | Description                          |
| -------------- | ----------------- | ------------------------------------ |
| ADD-HIST-10Y   | History extension | Daily K from 30 days to 10 years     |
| ADD-FACTOR-50  | Factor extension  | Pro factors: 20 → 70                 |
| ADD-MCP-PRO    | Agent pack        | Unlock all 18 MCP tools              |
| ADD-BULK       | Usage-based       | Overflow billing, no upgrade needed  |
| ADD-ENT-SLA    | Enterprise SLA    | 99.9% · dedicated bandwidth          |
| ADD-WHITELABEL | White-label       | Custom domain + docs                 |

> Pricing, upgrades, and business inquiries: **<drawsea@163.com>**

---

## Notes & Compliance

**Usage conventions**

- All endpoints are **read-only and authentication-free**. No registration
  or token required.
- `symbol` uses a **6-digit numeric code** (e.g., `688017`), no exchange suffix.
- Recommended request timeout: 10 seconds.
- Data source: ApocData, synchronized with A-share data ingestion cycles.

**Compliance**

- Quote data is delayed (not exchange-licensed real-time data).
- Derived data is cleaned and structured; redistribution is prohibited.
- News contains only titles and links; full content copyright belongs to the
  original publishers.
- Factors and signals are for research purposes only and **do not constitute
  any investment advice**.

---

Contact: <drawsea@163.com>

---

<br>

# ApocData · 天启至数 · A 股数据 Skill

> 免鉴权、零依赖，用 `curl` 直接调用 A 股行情 / 财务 / 资金 / 因子 / 公告等 45+ 数据接口，
> 支持 Claude、ChatGPT、通义千问、Kimi、DeepSeek 等所有支持工具调用的 AI Agent。

本仓库提供的 [`SKILL.md`](./SKILL.md) 是一份可直接装入 AI Agent 的能力卡片，
让大模型无需任何 SDK，即可查询 A 股市场数据并完成投研分析。

---

## 目录

- [产品简介](#产品简介)
- [数据服务平台](#数据服务平台)
- [安装](#安装)
- [基础用法](#基础用法)
- [接口能力总览](#接口能力总览)
- [综合分析示例](#综合分析示例)
- [版本与能力](#版本与能力)
- [注意事项与合规声明](#注意事项与合规声明)

---

## 产品简介

ApocData（天启至数）专注于为 AI Agent 提供 A 股市场数据服务，面向 AI Agent、
量化研究、投研内容与机构应用，提供行情、财务、资金流、龙虎榜、量化因子、
宏观经济等全维度数据接口。

---

## 数据服务平台

**平台网址：** <https://data.tianqis.com>

数据服务平台是 ApocData 的统一入口，提供：

- **免鉴权开放接口** —— 本 Skill 对接的就是这一组接口，无需注册、无需 Token，
  适合快速体验与轻量调用。
- **OpenAPI 文档与 SDK** —— 支持 Python / TypeScript。
- **平台版本（带 API Key）** —— 提供更高配额、更深历史、更低延迟与更多数据维度，
  详见下方 [版本与能力](#版本与能力)。

> 本 Skill 使用的开放接口 BASE 地址：`https://data.tianqis.com/api/blade-dataplatform/open/data`

---

## 安装

### macOS / Linux

```bash
mkdir -p ~/.claude/skills/apocdata
curl -o ~/.claude/skills/apocdata/SKILL.md \
  https://raw.githubusercontent.com/ApocData/ApocData-skill/main/SKILL.md
```

### Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force -Path ~\.claude\skills\apocdata
Invoke-WebRequest `
  -Uri https://raw.githubusercontent.com/ApocData/ApocData-skill/main/SKILL.md `
  -OutFile ~\.claude\skills\apocdata\SKILL.md
```

### 国内用户（Gitee 镜像）

```bash
mkdir -p ~/.claude/skills/apocdata
curl -o ~/.claude/skills/apocdata/SKILL.md \
  https://gitee.com/apocdata/ApocData-skill/raw/main/SKILL.md
```

重启 Claude Code 或 Claude Desktop 后自动生效。

---

## 基础用法

所有接口均为 HTTP GET，用 `curl` 直接调用：

```bash
BASE="https://data.tianqis.com/api/blade-dataplatform/open/data"

# 查单只股票行情
curl -s "$BASE/quote?symbol=000001"

# 查股票基本信息（含 PE/PB/市值）
curl -s "$BASE/stock?symbol=000001"
```

每个接口的完整参数、返回字段与示例问题，详见 [`SKILL.md`](./SKILL.md)。

---

## 接口能力总览

共 45 个接口，按数据维度分类如下：

### 实时行情与 K 线（7）

| 接口            | 说明                                  |
| ------------- | ----------------------------------- |
| `quote`       | 单只股票最新涨跌、量价                  |
| `quotes`      | 批量行情，最多 10 只                  |
| `daily`       | 日 K 历史，最近 N 条或日期区间，≤ 30 条 |
| `ranking`     | 全市场涨跌幅排行榜（涨幅 / 跌幅）        |
| `index-daily` | 指数日 K 行情                         |
| `hk-daily`    | 港股日 K 行情                         |
| `tech-factor` | 技术面因子（MACD / KDJ / RSI / BOLL / 均线） |

### 基本面与财务（5）

| 接口          | 说明                          |
| ----------- | --------------------------- |
| `stock`     | 股票基本信息（行业 / 市值 / PE / PB） |
| `financial` | 财务数据（ROE / 营收 / 净利润），≤ 4 期 |
| `express`   | 业绩快报                       |
| `dividend`  | 分红送配方案                    |
| `cyq-perf`  | 筹码分布与获利比例                 |

### 股东与公司治理（6）

| 接口              | 说明           |
| --------------- | ------------ |
| `holders`       | 十大股东 / 十大流通股东 |
| `holder-number` | 历史股东户数       |
| `share-float`   | 限售解禁记录       |
| `repurchase`    | 股票回购方案与进度   |
| `block-trade`   | 大宗交易记录       |
| `survey`        | 机构调研接待记录    |

### 资金流向（5）

| 接口            | 说明                |
| ------------- | ----------------- |
| `moneyflow`   | 个股资金流与主力净流入       |
| `hsgt`        | 沪深港通（北向 / 南向）资金流  |
| `sector-flow` | 行业 / 概念 / 地域板块资金流榜 |
| `hk-hold`     | 个股被沪深港通持股记录      |
| `margin`      | 两市融资融券交易汇总        |

### 龙虎榜与打板情绪（6）

| 接口                 | 说明           |
| ------------------ | ------------ |
| `dragon-tiger`     | 龙虎榜单 / 个股上榜历史 |
| `limit-list`       | 涨停 / 跌停 / 炸板池 |
| `limit-step`       | 连板天梯         |
| `hot-rank`         | 东方财富人气榜      |
| `hot-money`        | 知名游资名录        |
| `hot-money-detail` | 游资交易明细        |

### 板块与概念（4）

| 接口                 | 说明          |
| ------------------ | ----------- |
| `concepts`         | 东方财富概念板块目录   |
| `concept-stocks`   | 概念板块成分股     |
| `ths-boards`       | 同花顺行业 / 概念板块 |
| `ths-board-stocks` | 同花顺板块成分股    |

### 新闻与公告（2）

| 接口              | 说明                          |
| --------------- | --------------------------- |
| `news`          | 个股最近新闻（标题 / 链接 / 情绪）        |
| `announcements` | 公司公告（含 AI 摘要与 Markdown 全文） |

### 宏观经济（3）

| 接口                 | 说明                            |
| ------------------ | ----------------------------- |
| `macro`            | 宏观指标历史（GDP / CPI / PPI / PMI） |
| `macro/latest`     | 宏观指标最新值                      |
| `macro/definition` | 宏观指标定义与说明                   |

### 搜索与基础工具（5）

| 接口         | 说明                |
| ---------- | ----------------- |
| `stocks`   | 按名称 / 代码搜索股票，支持行业过滤 |
| `indexes`  | 按名称 / 代码搜索指数      |
| `calendar` | A 股交易日历           |
| `st`       | ST / 退市风险状态       |
| `factors`  | 量化因子注册表（脱敏）       |

### 可转债（2）

| 接口                  | 说明        |
| ------------------- | --------- |
| `convertible-bonds` | 可转债基本信息   |
| `cb-price-chg`      | 可转债转股价变动记录 |

---

## 综合分析示例

问「帮我分析一下 688017 的估值」时，Agent 会依次调用：

```bash
BASE="https://data.tianqis.com/api/blade-dataplatform/open/data"
curl -s "$BASE/stock?symbol=688017"          # PE/PB/市值
curl -s "$BASE/quote?symbol=688017"          # 当前股价
curl -s "$BASE/financial?symbol=688017"      # ROE/净利润
curl -s "$BASE/daily?symbol=688017&limit=30" # 近期走势
```

---

## 版本与能力

ApocData 按使用场景分为四档版本，所有版本共用同一套 API 契约，能力按版本白名单返回。
**本 Skill 默认对接免鉴权开放接口；如需更高配额或更深数据，可升级至带 API Key
的平台版本。**

### 版本一览

| SKU   | 版本   | 适用人群           |
| ----- | ----- | ----------------- |
| FREE  | 免费版 | 个人 / Agent 试用  |
| PRO   | 专业版 | 投研 / 内容博主    |
| QUANT | 量化版 | 量化研究团队       |
| ENT   | 企业版 | 机构嵌入 / 私有化   |

### 版本能力对比（节选）

| 能力项     | 免费版       | 专业版    | 量化版   | 企业版         |
| --------- | ----------- | -------- | ------- | ------------- |
| 日调用次数  | 2,000       | 50,000   | 300,000 | 定制          |
| QPS 上限   | 2           | 10       | 30      | 定制          |
| 实时快照延迟 | 15 分钟      | 1 分钟    | ≤ 30s   | 定制（≤ 10s） |
| 日 K 深度  | 30 交易日     | 5 年 + 复权 | 全量    | 全量          |
| 量化因子值  | —           | 20 个     | 全量    | 全量 + 定制    |
| MCP Tools | 8           | 14       | 18      | 18+ 定制       |
| 可用性 SLO | best-effort | 99.0%    | 99.5%   | 99.9%（合同） |
| 技术支持   | 社区         | 邮件 48h  | 邮件 24h | 专属群 + 电话  |

> 所有版本均含 OpenAPI 文档、SDK、状态页与用量看板。完整能力矩阵与价格请联系咨询。

### 附加包（可叠加现有版本）

| 附加包          | 名称       | 说明                       |
| -------------- | --------- | -------------------------- |
| ADD-HIST-10Y   | 历史扩展   | 日 K 从 30 天扩展到 10 年   |
| ADD-FACTOR-50  | 因子扩展   | 专业版因子 20 → 70 个       |
| ADD-MCP-PRO    | Agent 包   | 开放全部 18 个 MCP Tools    |
| ADD-BULK       | 按量调用   | 超额计费，无需升级          |
| ADD-ENT-SLA    | 企业 SLA   | 99.9% · 专属带宽            |
| ADD-WHITELABEL | 白标 / 域名 | 自定义域名 + 文档           |

> 价格、升级或商务咨询请联系：**<drawsea@163.com>**

---

## 注意事项与合规声明

**调用约定**

- 所有接口**只读、免鉴权**，无需注册或 Token。
- `symbol` 统一使用 **6 位数字代码**（如 `688017`），不带交易所后缀。
- 单次请求建议超时 10 秒。
- 数据来源：ApocData，与 A 股数据落库周期同步。

**合规说明**

- 行情为延迟数据，非交易所授权实时行情。
- 衍生数据为清洗后的结构化数据，禁止二次转售。
- 新闻仅含标题与链接，正文版权归原媒体所有。
- 因子与信号属研究用途，**不构成任何投资建议**。

---

联系方式：<drawsea@163.com>
