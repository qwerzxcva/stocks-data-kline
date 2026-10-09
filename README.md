# stocks-data-kline (A 股/全球指数 K 线历史)

## 用途
A 股 (5,400+ 只) + 主要指数/ETF 的历史 K 线数据（日线/周线/月线），用于复盘、回测、技术分析。

## 数据来源
- 腾讯 `web.ifzq.gtimg.cn/appstock/app/fqkline/get` 前复权接口
- 东财指数 K 线（历史部分）

## 目录结构
```
meta/
  universe.csv            # 全市场代码清单 (code|name|market)
  collector_progress.json # 采集器进度 (运行时)
data/
  bulk/                   # 批量分片产出 (collector 原始输出, 日/周/月各 10 分片)
  bycode/
    day/{code}.csv        # 前复权日线 (~800 根)
    week/{code}.csv       # 前复权周线 (~320 根)
    month/{code}.csv      # 前复权月线 (~320 根)
  index/                  # 指数 K 线 (sh000001 等历史日线/周线/月线/yearly)
    {code}.txt            # 指数日/周/月
    history/              # 各指数多年历史 (含 yearly.csv)
    global/               # 全球指数实时报价 (quotes.txt) + K 线 JSON (WAF 限制, 按需补充)
  watchlist/
    all.txt               # watchlist 60 根日线 (tencent fqkline)
```

## CSV 格式
```
# code sh600000 tencent fqkline daily, ~800 bars
date,open,close,high,low,volume
2023-06-15,17.12,17.38,17.53,16.80,233154
```

## 采集
运行: `python3 ~/workspace/stocks-tools/tools/fetch_all_market.py` → 写 `data/bulk/`, 再由 `split_allmarket.py` 拆分到 `data/bycode/`。
WAF 会间歇拦截, 脚本内置指数退避等待。采集速度受限 (单线程 2.5s/请求), 预计完成一轮需 30-60 分钟 (5k 只×5 频率)。
