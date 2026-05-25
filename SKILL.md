# 天启数据云 A 股数据 Skill

> 免鉴权，零依赖，直接用 curl 调用，支持 Claude / OpenAI / 通义千问等所有 Agent。

## 安装（两步，无需 pip）

```bash
mkdir -p ~/.claude/skills/FF-data
curl -o ~/.claude/skills/FF-data/SKILL.md \
  https://raw.githubusercontent.com/Gavin-2075/FFdata/main/SKILL.md
```

重启 Claude Code 后自动生效。

---

## 基础用法

所有接口均为 HTTP GET，用 curl 直接调用：

```bash
BASE="https://data.tianqis.com/api/blade-dataplatform/open/data"

# 查单只股票行情
curl -s "$BASE/quote?symbol=000001"

# 查股票基本信息（含 PE/PB/市值）
curl -s "$BASE/stock?symbol=000001"
```

---

## 工具列表

### 1. 实时行情 `quote`

查单只股票最新涨跌、量价。

```bash
curl -s "$BASE/quote?symbol=688017"
# 返回: symbol, name, trade_date, open, high, low, close,
#       pre_close, change, pct_chg, volume, amount
```

**示例问题**：「688017 今天涨了多少？」「茅台现在什么价格？」

---

### 2. 批量行情 `quotes`

最多同时查 10 只股票。

```bash
curl -s "$BASE/quotes?symbols=000001,600519,000858"
```

**示例问题**：「帮我看下茅台、五粮液、平安银行今天的涨跌」

---

### 3. 日K历史 `daily`

最近 N 条日K，或按日期区间查询，均最多 30 条。

```bash
# 最近 N 条
curl -s "$BASE/daily?symbol=000001&limit=30"
# 按日期区间（start/end 为 YYYYMMDD，需成对传入）
curl -s "$BASE/daily?symbol=000001&start=20260101&end=20260331"
# 返回: trade_date, open, high, low, close, volume, amount, pct_chg
```

**示例问题**：「平安银行最近 30 天走势」「平安银行 2026 年 1 月的日K」

---

### 4. 股票基本信息 `stock`

行业、市值、PE、PB、上市日期。

```bash
curl -s "$BASE/stock?symbol=688017"
# 返回: symbol, name, market, industry, pe, pb, total_mv, circ_mv
```

**示例问题**：「688017 的估值怎么样？PE 是多少？」

---

### 5. 股票搜索 `stocks`

按名称/代码关键词搜索，支持行业过滤。

```bash
curl -s "$BASE/stocks?q=银行&industry=银行&limit=20"
curl -s "$BASE/stocks?q=688017"
```

**示例问题**：「帮我找所有银行股」「搜索新能源相关股票」

---

### 6. 财务数据 `financial`

ROE、营收、净利润等，最多 4 期。

```bash
curl -s "$BASE/financial?symbol=000001&limit=4"
# 返回: report_period, report_type, roe, revenue, net_profit,
#       grossprofit_margin, eps, bps, debt_to_assets
```

**示例问题**：「平安银行的 ROE 怎么样？」「最近 4 期净利润趋势」

---

### 7. 最近新闻 `news`

最多 10 条，最多 168 小时内。

```bash
curl -s "$BASE/news?symbol=000001&hours=72&limit=10"
# 返回: title, publish_time, source, url, sentiment
```

**示例问题**：「平安银行最近有什么新闻？」「茅台近 3 天有没有利好？」

---

### 8. 十大股东 `holders`

最新期十大股东 / 十大流通股东。

```bash
curl -s "$BASE/holders?symbol=000001&holderCategory=top10_float"
# 返回: holder_name, hold_amount, hold_ratio, hold_change, holder_type
```

**示例问题**：「平安银行十大流通股东是哪些机构？」

---

### 9. ST 状态 `st`

是否 ST / 退市风险，正常股返回 null。

```bash
curl -s "$BASE/st?symbol=000001"
```

**示例问题**：「这只股票有退市风险吗？」

---

### 10. 宏观指标历史 `macro`

GDP / CPI / PPI / PMI，最多 12 条。

```bash
curl -s "$BASE/macro?type=CPI&limit=12"
# 返回: period, value, yoy_growth, mom_growth
```

**示例问题**：「最近 12 个月 CPI 走势」

---

### 11. 宏观指标最新值 `macro/latest`

```bash
curl -s "$BASE/macro/latest?type=PMI"
# 返回: period, value, unit, yoy_growth
```

**示例问题**：「现在 PMI 是多少？」「最新 GDP 增速」

---

### 12. 公司公告 `announcements`

按股票查最近公告（标题、AI 摘要、Markdown 完整正文、公告日期、类型、链接），最多 5 条。

```bash
curl -s "$BASE/announcements?symbol=000001&limit=5"
# 返回: title, summary, content, ann_date, publish_time, category,
#       importance, sentiment, keywords, source, url
# 说明: content 为 Markdown 格式公告全文，单条可能较长，故上限 5 条
```

**示例问题**：「平安银行最近发布了哪些公告？」「茅台最新公告的具体内容是什么？」

---

### 13. 交易日历 `calendar`

查询区间内 A 股交易日列表，跨度最多 366 天。

```bash
curl -s "$BASE/calendar?start=20260101&end=20260331"
# 不传参默认查当年 1 月 1 日至今
curl -s "$BASE/calendar"
# 返回: ["20260102","20260105","20260106", ...]  按 YYYYMMDD 升序
```

**示例问题**：「2026 年 3 月有哪些交易日？」「最近一个交易日是哪天？」

---

### 14. 量化因子注册表 `factors`

平台全部已启用的量化因子清单，脱敏返回（不含计算公式/权重/打分）。

```bash
curl -s "$BASE/factors"
# 返回: rule_id, name, event_type_label, scope, enabled, version, updated_at
```

**示例问题**：「平台支持哪些量化因子？」「有没有和涨停/事件相关的因子？」

---

### 15. 涨跌幅排行榜 `ranking`

全市场按当日涨跌幅排序，涨幅榜 / 跌幅榜。

```bash
# 涨幅榜（默认）
curl -s "$BASE/ranking?direction=gain&limit=20"
# 跌幅榜
curl -s "$BASE/ranking?direction=loss&limit=20"
# 返回: symbol, name, trade_date, close, change, pct_chg, volume, amount
# direction=gain（涨幅榜）/ loss（跌幅榜），limit 最多 50
```

**示例问题**：「今天涨得最多的 10 只股票」「今日跌幅榜前 20」

---

### 16. 宏观指标定义 `macro/definition`

查宏观指标的含义、单位、统计频率。

```bash
curl -s "$BASE/macro/definition?type=CPI"
# 返回: indicator_code, indicator_name, unit, frequency, description
# type 可选 GDP/CPI/PPI/PMI
```

**示例问题**：「CPI 这个指标是什么意思？」「PMI 多久更新一次？」

---

### 17. 个股资金流 `moneyflow`

个股超大单/大单/中单/小单买卖额与主力净流入。

```bash
curl -s "$BASE/moneyflow?symbol=000001&limit=10"
# 返回: trade_date, buy_elg_amount, sell_elg_amount, buy_lg_amount, sell_lg_amount,
#       buy_md_amount, sell_md_amount, buy_sm_amount, sell_sm_amount, net_mf_amount
```

**示例问题**：「平安银行最近主力是流入还是流出？」

---

### 18. 沪深港通资金流 `hsgt`

北向（陆股通）、南向（港股通）每日资金流。

```bash
curl -s "$BASE/hsgt?limit=10"
# 返回: trade_date, hgt, sgt, ggt_ss, ggt_sz, north_money, south_money
```

**示例问题**：「最近北向资金是买还是卖？」「今天北向净流入多少？」

---

### 19. 板块资金流榜 `sector-flow`

最新交易日行业/概念/地域板块的资金流排行。

```bash
curl -s "$BASE/sector-flow?type=行业&limit=20"
# type 可选 行业 / 概念 / 地域
# 返回: trade_date, name, pct_change, net_amount, net_amount_rate, rank
```

**示例问题**：「今天哪个行业资金流入最多？」「概念板块资金流排行」

---

### 20. 龙虎榜 `dragon-tiger`

某交易日龙虎榜单，或某个股的上榜历史。

```bash
# 当日榜单（date 缺省取最新交易日）
curl -s "$BASE/dragon-tiger?date=20260518&limit=30"
# 某个股上榜历史
curl -s "$BASE/dragon-tiger?symbol=000001"
# 返回: trade_date, ts_code, name, close, pct_change, turnover_rate, amount,
#       l_buy, l_sell, net_amount, reason
```

**示例问题**：「今天龙虎榜有哪些股票？」「平安银行上过龙虎榜吗？」

---

### 21. 涨跌停池 `limit-list`

某交易日的涨停 / 跌停 / 炸板个股。

```bash
curl -s "$BASE/limit-list?kind=U&date=20260518&limit=30"
# kind=U 涨停 / D 跌停 / Z 炸板，date 缺省取最新交易日
# 返回: trade_date, ts_code, name, industry, close, pct_chg, amount,
#       first_time, last_time, open_times, up_stat, limit_times, limit
```

**示例问题**：「今天有多少只涨停？」「最近一个交易日的跌停股」

---

### 22. 连板天梯 `limit-step`

某交易日连板个股，按连板数排列。

```bash
curl -s "$BASE/limit-step?limit=30"
# date 缺省取最新交易日
# 返回: trade_date, ts_code, name, nums（连板数）
```

**示例问题**：「今天最高几连板？」「现在的连板天梯」

---

### 23. 分红送配 `dividend`

个股历史分红送配方案。

```bash
curl -s "$BASE/dividend?symbol=000001&limit=10"
# 返回: end_date, ann_date, div_proc, stk_div, cash_div, cash_div_tax,
#       record_date, ex_date, pay_date
```

**示例问题**：「平安银行的分红方案」「茅台今年分红多少？」

---

### 24. 指数列表搜索 `indexes`

按名称/代码搜索指数。

```bash
curl -s "$BASE/indexes?q=沪深300"
curl -s "$BASE/indexes?market=CSI&limit=20"
# 返回: ts_code, name, market, publisher, category, base_date, base_point
```

**示例问题**：「沪深300 的指数代码是多少？」「有哪些中证指数？」

---

### 25. 指数日K `index-daily`

指数日K行情（用指数 tsCode 查询，可先用 `indexes` 查代码）。

```bash
curl -s "$BASE/index-daily?tsCode=000300.SH&limit=30"
# 常用：000001.SH 上证指数、000300.SH 沪深300、399006.SZ 创业板指
# 返回: trade_date, open, high, low, close, pre_close, change, pct_chg, vol, amount
```

**示例问题**：「沪深300 最近 30 天走势」「上证指数昨天涨了多少？」

---

### 26. 业绩快报 `express`

个股业绩快报（营收、净利润、EPS、ROE）。

```bash
curl -s "$BASE/express?symbol=000001&limit=4"
# 返回: end_date, ann_date, revenue, n_income, total_profit, diluted_eps,
#       diluted_roe, yoy_net_profit, total_assets
```

**示例问题**：「平安银行最新业绩快报」「这家公司净利润同比增长多少？」

---

### 27. 融资融券 `margin`

两市融资融券交易汇总。

```bash
curl -s "$BASE/margin?exchange=SSE&limit=10"
# exchange 可选 SSE/SZSE/BSE，不传则全部
# 返回: trade_date, exchange_id, rzye（融资余额）, rzmre（融资买入额）,
#       rqye（融券余额）, rzrqye（融资融券余额）
```

**示例问题**：「最近两融余额是多少？」「融资余额在增加吗？」

---

### 28. 大宗交易 `block-trade`

个股大宗交易记录（成交价、折溢价、买卖营业部）。

```bash
curl -s "$BASE/block-trade?symbol=000001&limit=10"
# 返回: trade_date, price, vol, amount, buyer, seller
```

**示例问题**：「平安银行最近有大宗交易吗？」

---

### 29. 股东户数 `holder-number`

个股历史股东户数。

```bash
curl -s "$BASE/holder-number?symbol=000001&limit=10"
# 返回: ann_date, end_date, holder_num
```

**示例问题**：「这只股票股东户数是增是减？」

---

### 30. 限售解禁 `share-float`

个股限售股解禁记录。

```bash
curl -s "$BASE/share-float?symbol=000001&limit=10"
# 返回: ann_date, float_date, float_share, float_ratio, holder_name, share_type
```

**示例问题**：「平安银行近期有解禁吗？解禁多少？」

---

### 31. 股票回购 `repurchase`

个股回购方案与进度。

```bash
curl -s "$BASE/repurchase?symbol=000001&limit=10"
# 返回: ann_date, proc, vol, amount, high_limit, low_limit
```

**示例问题**：「这家公司在回购股票吗？」

---

### 32. 东财概念板块 `concepts`

东方财富概念板块目录（最新交易日，含当日热度/领涨股）。

```bash
curl -s "$BASE/concepts?q=AI&limit=30"
# 返回: theme_code, name, pct_change, hot, z_t_num, main_change,
#       lead_stock, lead_stock_code
```

**示例问题**：「有哪些 AI 相关概念板块？」

---

### 33. 概念成分股 `concept-stocks`

某概念板块的成分股（themeCode 可从 `concepts` 获取）。

```bash
curl -s "$BASE/concept-stocks?themeCode=000894.DC&limit=50"
# 返回: ts_code, name, industry, reason, hot_num
```

**示例问题**：「光通信概念里有哪些股票？」

---

### 34. 同花顺板块 `ths-boards`

同花顺行业/概念板块指数。

```bash
curl -s "$BASE/ths-boards?q=机器人&limit=30"
# 返回: ts_code, name, count（成分数）, exchange, list_date, type
```

**示例问题**：「同花顺有哪些机器人板块？」

---

### 35. 同花顺板块成分 `ths-board-stocks`

某同花顺板块的成分股（tsCode 可从 `ths-boards` 获取）。

```bash
curl -s "$BASE/ths-board-stocks?tsCode=886108.TI&limit=50"
# 返回: con_code, con_name
```

**示例问题**：「这个板块的成分股有哪些？」

---

### 36. 沪深港通持股 `hk-hold`

个股被沪深港通（北向）持股记录。

```bash
curl -s "$BASE/hk-hold?symbol=000001&limit=10"
# 返回: trade_date, name, ratio（持股占比）, vol（持股量）
```

**示例问题**：「北向资金持有平安银行多少？」

---

### 37. 港股日K `hk-daily`

港股日K行情。

```bash
curl -s "$BASE/hk-daily?tsCode=00700.HK&limit=30"
# 返回: trade_date, open, high, low, close, pre_close, change, pct_chg, vol, amount
```

**示例问题**：「腾讯港股最近 30 天走势」

---

### 38. 可转债列表 `convertible-bonds`

可转债基本信息（按债券或正股搜索）。

```bash
curl -s "$BASE/convertible-bonds?q=超声"
curl -s "$BASE/convertible-bonds?stkCode=688535.SH"
# 返回: ts_code, bond_short_name, stk_code, stk_short_name, issue_size,
#       value_date, maturity_date, coupon_rate, conv_price
```

**示例问题**：「华海诚科有没有发行可转债？」

---

### 39. 可转债转股价变动 `cb-price-chg`

可转债转股价的历史调整记录。

```bash
curl -s "$BASE/cb-price-chg?tsCode=127026.SZ&limit=10"
# 返回: publish_date, change_date, convert_price_initial,
#       convertprice_bef, convertprice_aft
```

**示例问题**：「超声转债下修过转股价吗？」

---

### 40. 人气榜 `hot-rank`

东方财富人气榜（个股热度排名）。

```bash
curl -s "$BASE/hot-rank?type=A股市场&limit=30"
# type 可选 A股市场 / ETF基金 / 港股市场 / 美股市场
# 返回: trade_date, ts_code, ts_name, rank, pct_change, current_price
```

**示例问题**：「今天 A 股人气榜前 10 是哪些？」

---

### 41. 技术面因子 `tech-factor`

个股技术指标（MACD/KDJ/RSI/BOLL/均线等，前复权）。

```bash
curl -s "$BASE/tech-factor?symbol=000001&limit=1"
# 返回: trade_date, close, pct_chg, turnover_rate, pe_ttm, pb,
#       macd_qfq, kdj_k_qfq, kdj_d_qfq, rsi_qfq_6, boll_upper_qfq,
#       ma_qfq_5, ma_qfq_20, cci_qfq, wr_qfq 等
```

**示例问题**：「平安银行现在的 MACD 和 KDJ 怎么样？」

---

### 42. 机构调研 `survey`

个股机构调研接待记录。

```bash
curl -s "$BASE/survey?symbol=600101&limit=5"
# 返回: surv_date, rece_place, rece_mode, rece_org, comp_rece
```

**示例问题**：「这家公司最近接待了哪些机构调研？」

---

### 43. 游资名录 `hot-money`

知名游资席位名录。

```bash
curl -s "$BASE/hot-money?limit=50"
# 返回: name（游资名）, orgs（关联营业部）
```

**示例问题**：「有哪些知名游资？」

---

### 44. 游资交易明细 `hot-money-detail`

某交易日游资买卖明细，或某个股的游资记录。

```bash
# 当日明细（date 缺省取最新交易日）
curl -s "$BASE/hot-money-detail?date=20260518&limit=30"
# 某个股的游资记录
curl -s "$BASE/hot-money-detail?symbol=600730"
# 返回: trade_date, ts_code, ts_name, buy_amount, sell_amount,
#       net_amount, hm_name, hm_orgs
```

**示例问题**：「今天游资都在买什么？」

---

### 45. 筹码分布 `cyq-perf`

个股筹码分布与获利比例。

```bash
curl -s "$BASE/cyq-perf?symbol=000001&limit=5"
# 返回: trade_date, his_low, his_high, cost_5pct, cost_50pct, cost_95pct,
#       weight_avg（加权平均成本）, winner_rate（获利比例）
```

**示例问题**：「平安银行现在的获利盘比例是多少？」「主力成本大概在哪？」

---

## 综合分析示例

问「帮我分析一下 688017 的估值」时，依次调用：

```bash
BASE="https://data.tianqis.com/api/blade-dataplatform/open/data"
curl -s "$BASE/stock?symbol=688017"       # PE/PB/市值
curl -s "$BASE/quote?symbol=688017"       # 当前股价
curl -s "$BASE/financial?symbol=688017"   # ROE/净利润
curl -s "$BASE/daily?symbol=688017&limit=30"  # 近期走势
```

---

## 注意事项

- 所有接口**只读、免鉴权**，无需注册或 token
- symbol 统一用 **6 位数字代码**（688017），不带交易所后缀
- 单次请求建议超时 10 秒
- 数据来源：天启数据云，与 A 股数据落库周期同步