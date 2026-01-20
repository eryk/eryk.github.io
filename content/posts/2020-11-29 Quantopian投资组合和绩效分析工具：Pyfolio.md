---
title: Quantopian投资组合和绩效分析工具：Pyfolio
date: 2020-11-29 09:53:00
categories: ["量化交易系统"]
---

### 介绍

pyfolio是由Quantopian开发的Python库，用于对金融投资组合进行绩效和风险分析。它与Zipline开源回溯测试库配合使用良好。
pyfolio的核心是所谓的“tear sheet ”，它由各种单独的图组成，这些图提供了交易算法性能的全面图像展示。

### 入门示例

这里以双均线策略来做演示，当20日均线上穿40日均线买入股票，当20日均线下穿40日均线则卖出股票，首先我们通过tushare获取股票历史数据

```python
import pandas as pd
import numpy as np
import tushare as ts
import pyfolio as pf

pro = ts.pro_api()
df = ts.pro_bar(ts_code='000001.SZ', adj='hfq', start_date='20150101', end_date='20191231')
df.index = pd.to_datetime(df['trade_date'])
df.sort_index(inplace=True)
```

接下来生成策略信号

```python
df['ma20'] = df['close'].rolling(20).mean()
df['ma40'] = df['close'].rolling(40).mean()
# 计算20日均线和40日均线的距离
df['diff'] = df['ma20'] - df['ma40']
# 当diff值大于0买入股票，当diff值小于0卖出股票
df['signal'] = np.where(df['diff'] > 0, 1, 0)
df['signal'] = np.where(df['diff'] < 0, 0, df['signal'])
# 计算策略每日收益，这里计算每日收益使用log函数，signal计算由于使用了当天的收盘价，所以需要shift 1天，否则会用到未来数据
df['strategy'] = np.log(df['close'] / df['close'].shift(1)) * df['signal'].shift(1)
# 计算资产收益率
df['equity'] = df['strategy'].cumsum() + 1
```

展示资产收益率曲线，这里对股价做了归一化处理以便数据展示，可以看到双均线策略不仅跑赢股价，并且资金曲线回撤相对较小。

```python
df['close_norm'] = df['close'] / float(df['close'][0])
df[['close_norm','equity']].plot(figsize=(12,6))
```

![](https://raw.githubusercontent.com/eryk/blog_img/master/1.png)

上图看到的信息有限，接下来是使用pyfolio工具对投资回报进一步分析，代码如下:

```python
pf.create_full_tear_sheet(df['strategy'], benchmark_rets=np.log(df['close'] / df['close'].shift(1)))
```

首先是返回汇总信息：

![](https://raw.githubusercontent.com/eryk/blog_img/master/2.png)

介绍常用的几个指标：

* Annual return：年化回报率
* Cumulative returns: 累计收益率，是策略从开始执行到结束的总资产收益率。
* Annual volatility：年化波动率
* Sharpe ratio：夏普比率，一种非常流行的风险指标。它表示每单位风险（通过标准差衡量）的超额收益（超过无风险利率）。
* Sortino ratio: 索提诺比率，Sharpe比率的修改版本，其中标准偏差由下行偏差代替。下行偏差仅衡量该系列的负波动性，严格来说是在称为最低可接受收益的预定水平以下。
* Maximum drawdown ：最大跌幅—指示峰和谷之间的最大跌幅（以％表示）
* Tail ratio：对daily return的分布选取95分位和5分位，然后相除取绝对值。本质的含义就是赚取的return比亏钱的大多少倍。
* Daily value at risk（daily Value-at-Risk ）
  每日风险价值-另一个非常流行的风险指标。在这种情况下，这表明在95％的情况下，将头寸（投资组合）再保留1天，损失不会超过2.3％。

下图从各个角度对资产回报进行更详细的图表展示

![](https://raw.githubusercontent.com/eryk/blog_img/master/3.png)

### 参考
https://quantopian.github.io/pyfolio/
https://zhuanlan.zhihu.com/p/118108419
https://towardsdatascience.com/the-easiest-way-to-evaluate-the-performance-of-trading-strategies-in-python-4959fd798bb3