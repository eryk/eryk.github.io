---
title: 使用Pandas开发一个均线策略
date: 2019-04-24 20:47:00
categories: ["策略研究"]
tags: ["Pandas"]
---

我的微信公众号：pyquant

![](https://raw.githubusercontent.com/eryk/blog_img/master/20190424095328.jpg)

大家好，这篇文章我将使用Pandas创建一个简单的均线交叉策略，以500ETF作为标的物进行回测

移动平均线可能是技术指标里的"hello world"了，常用的均线有5、10、20、60、120日均线，在其他时间周期上应用移动平均线指标也是类似方式。

移动平均线按时间周期长短分为：短期移动平均线，中期移动平均线，长期移动平均线；按计算方法分为：算术移动平均线，加权移动平均线，指数平滑移动平均线（EMA）

下面正式开始编写策略代码，我们使用jupyer作为研究环境，首先先导入依赖模块

```python
import pandas as pd
import numpy as np
import tushare as ts

%matplotlib inline
```

接下来使用tushare下载500ETF的历史数据，500ETF是从2013年开始上市交易的，这里将start参数设置为2013，这样可以获取500ETF的全部历史数据。

```python
etf500 = ts.get_k_data('510500',start='2013')
etf500.set_index(pd.to_datetime(etf500['date']),inplace=True) 
del etf500['date']
etf500.head()
```

输出结果如下，数据是从2013年3月15日开始的：

|       date |  open | close |  high |   low |    volume |   code |
| ---------: | ----: | ----: | ----: | ----: | --------: | -----: |
| 2013-03-15 | 0.967 | 0.970 | 0.985 | 0.955 | 3259273.0 | 510500 |
| 2013-03-18 | 0.955 | 0.954 | 0.972 | 0.953 |  936962.0 | 510500 |
| 2013-03-19 | 0.956 | 0.960 | 0.960 | 0.941 | 1080499.0 | 510500 |
| 2013-03-20 | 0.960 | 0.985 | 0.986 | 0.958 |  501195.0 | 510500 |
| 2013-03-21 | 0.985 | 0.995 | 0.996 | 0.981 |  698243.0 | 510500 |

继续画出收盘价格曲线，对500ETF走势有个大概的了解。

```python
etf500['close'].plot(grid=True, figsize=(8,5))
```

![](https://raw.githubusercontent.com/eryk/blog_img/master/20190423172643.png)

接下来是双均线策略的实现，我们使用20日均线和60日均线作为短期和长期均线，下面先分别计算20日和60日均线序列

```python
etf500['ma20'] = etf500['close'].rolling(20).mean()
etf500['ma60'] = etf500['close'].rolling(60).mean()
etf500[['close','ma20','ma60']].plot(grid=True, figsize=(14,5))
```

![](https://raw.githubusercontent.com/eryk/blog_img/master/20190423174737.png)

我们已经获取了两条移动平均线序列，接下来是根据均线来生成交易信号

策略信号会有两种状态： 

1. 买入信号，当20日均线向上穿过60日均线时持有多头仓位

2. 卖出信号，当20日均线向下穿过60日均线时平仓

```python
etf500['Stance'] = np.where(etf500['ma20'] - etf500['ma60'] > 0, 1, 0)
etf500['Stance'].value_counts()
```

最后一行统计持仓和空仓的天数，输出结果如下：

```python
1    761
0    724
Name: Stance, dtype: int64
```

```python
etf500['Stance'].plot(ylim=[-0.1,1.1])
```

下图显示持仓日期数据

![](https://raw.githubusercontent.com/eryk/blog_img/master/20190423223738.png)

接下来，我们根据持仓数据来计算持仓的收益

```python
etf500['Market Returns'] = np.log(etf500['close'] / etf500['close'].shift(1))
etf500['Strategy'] = etf500['Market Returns'] * etf500['Stance'].shift(1)
etf500[['Market Returns','Strategy']].cumsum().plot(grid=True,figsize=(8,5))
```

![](https://raw.githubusercontent.com/eryk/blog_img/master/20190423225315.png)

以上图片展示了市场回报率与策略回报曲线，可以看到20和60日双均线策略并没有跑赢500ETF，不过我们也可以测试下其他均线组合，也许会有不错的效果。

### 参考

https://www.pythonforfinance.net/2016/09/01/moving-average-crossover-trading-strategy-backtest-in-python/#more-15498>