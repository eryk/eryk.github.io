---
title: Quantopian单因子分析工具：Alphalens
date: 2020-11-20 09:22:00
categories: ["量化交易系统"]
---

Quantopian是国外著名的量化交易平台，早期聚宽就是仿照这个网站开发的，算是这类平台的鼻祖了，可惜Quantopian最近刚宣布要停止运营了。Quantopian开发了许多优秀的开源项目，其中比较著名的有zipline、pyfolio和alphalens，zipline是事件驱动的回测引擎，Alphalens与Zipline开源回溯测试库以及Pyfolio配合使用，Pyfolio提供金融投资组合的绩效和风险分析。

Alphalens主要功能是alpha因子的相关性统计数据和图表展示，包括：

- Returns Analysis
- Information Coefficient Analysis
- Turnover Analysis
- Grouped Analysis

下面通过一个例子来了解下Alphalens的主要用法，最重要的函数有两个:

* get_clean_factor_and_forward_returns：数据预处理，将因子数据，价格数据和组映射格式化为包含对齐的时间戳和资产代码的MultiIndex索引的DataFrame。返回的数据将被格式化为适合Alphalens函数的格式。因子数据格式是MultiIndex索引，level0是时间，level1是资产代码，数据值只有一列为因子值，数据格式如下图所示：

  ![](https://raw.githubusercontent.com/eryk/blog_img/master/20201121235400.png)

* create_full_tear_sheet: 生成全面的单因子分析和评估数据。

通过tushare获取A股股票数据，并将结果处理成get_clean_factor_and_forward_returns参数要求的格式

```python
import pandas as pd
import tushare as ts

pro = ts.pro_api()
# 此接口获取的数据为未复权数据，回测建议使用后复权数据，这里为批量获取股票数据做了简化
df = pro.daily(ts_code='000001.SZ,600000.SH', start_date='20200101', end_date='20201231')
df.index = pd.to_datetime(df['trade_date'])
df.index.name = None
df.sort_index(inplace=True)
# MultiIndex，level0为日期，level1为股票代码，assets为get_clean_factor_and_forward_returns所需的因子数据格式
assets = df.set_index([df.index, df['ts_code']], drop=True)
# column为股票代码，index为日期，值为股票收盘价
close = df.pivot_table(index='trade_date',columns='ts_code',values='close')
close.index = pd.to_datetime(close.index)
```

生成的assets格式入下图所示，红框为index：

![](https://raw.githubusercontent.com/eryk/blog_img/master/20201122003628.png)

生成的close格式如下图所示：

![](https://raw.githubusercontent.com/eryk/blog_img/master/20201122003811.png)

接下来使用alphalens工具对数据进行分析，这里我们使用股票涨跌幅(字段名为pct_chg)作为因子数据

```python
from alphalens.utils import get_clean_factor_and_forward_returns
from alphalens.tears import create_full_tear_sheet
# 需要将pct_chg做shift处理，否则将使用未来数据
ret = get_clean_factor_and_forward_returns(assets[['pct_chg']].shift(2),close)
create_full_tear_sheet(ret, long_short=False)
```

使用默认参数的情况下，ret将包含未来1、5、10日收益率，factor因子值，这里对应pct_chg列，factor_quantile为因子分组结果，默认会将因子分成5组，ret结果如图所示:

![](https://raw.githubusercontent.com/eryk/blog_img/master/20201122004722.png)

调用create_full_tear_sheet会生成因子分析结果，包含分位数统计信息、收益率信息、分组平均收益率柱状图、所有收益率分布图、单信号组合构建收益率图等，截取部分数据图如下：

![](https://raw.githubusercontent.com/eryk/blog_img/master/20201122005223.png)

![](https://raw.githubusercontent.com/eryk/blog_img/master/20201122005156.png)

以上就是样例全部内容，可以看到alphalens功能非常丰富，还需要继续深入学习了解。

### 参考

http://quantopian.github.io/alphalens/

https://www.quantopian.com/posts/alphalens-a-new-tool-for-analyzing-alpha-factors