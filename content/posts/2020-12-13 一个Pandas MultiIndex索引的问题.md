---
title: Pandas MultiIndex索引使用问题
date: 2020-12-13 16:54:00
categories: ["python"]
---

关于MultiIndex的资料并不太多，[官方文档](https://pandas.pydata.org/pandas-docs/stable/user_guide/advanced.html)链接请点这里查看。

最近在使用MultiIndex时遇到的一个问题，先描述一下数据格式。我使用MultiIndex DataFrame来存储股票未来N天的收益率，变量命名为returns，数据样例如下，columns 1d和5d分别表示未来1天和5天的股票收益率，index为MultiIndex，第一级索引为日期，第二级索引为股票代码。

```
                              1d        5d
2005-01-04 000001.XSHE -0.009202  0.010736
           000002.XSHE  0.036053  0.045541
           000004.XSHE  0.020896  0.068657
           000005.XSHE  0.023474  0.098592
           000006.XSHE  0.014286  0.178571
...                          ...       ...
2020-12-11 688600.XSHG       NaN       NaN
           688777.XSHG       NaN       NaN
           688788.XSHG       NaN       NaN
           688981.XSHG       NaN       NaN
           689009.XSHG       NaN       NaN
```

对returns用start='2020-01-01'、end='2020-12-11'起止时间做了截断处理，代码片段如下：

```
returns_index = returns.index.get_level_values(level=0)
returns = returns.loc[(returns_index > pd.to_datetime(start)) & (returns_index < pd.to_datetime(end))]
```

这样获取的数据是从2020年开始的，但是使用returns.index.levels[0]发现数据还是从2005年开始的

```
returns.index.levels[0]
DatetimeIndex(['2005-01-04', '2005-01-05', '2005-01-06', '2005-01-07',
               '2005-01-10', '2005-01-11', '2005-01-12', '2005-01-13',
               '2005-01-14', '2005-01-17',
               ...
               '2020-11-30', '2020-12-01', '2020-12-02', '2020-12-03',
               '2020-12-04', '2020-12-07', '2020-12-08', '2020-12-09',
               '2020-12-10', '2020-12-11'],
              dtype='datetime64[ns]', length=3875, freq=None)
```

想了想，应该是由于index索引不可变，这里需要重建MultiIndex DataFrame，需要用到pandas.MultiIndex.remove_unused_levels() 方法先删除掉index里多余的信息，然后再重新创建MultiIndex DataFrame，代码如下：

```python
tmp = returns.index.remove_unused_levels()
tmp = returns.index.remove_unused_levels()
returns = pd.DataFrame(returns, tmp)
returns.index.levels[0]
```
```
DatetimeIndex(['2020-01-02', '2020-01-03', '2020-01-06', '2020-01-07',
               '2020-01-08', '2020-01-09', '2020-01-10', '2020-01-13',
               '2020-01-14', '2020-01-15',
               ...
               '2020-11-27', '2020-11-30', '2020-12-01', '2020-12-02',
               '2020-12-03', '2020-12-04', '2020-12-07', '2020-12-08',
               '2020-12-09', '2020-12-10'],
              dtype='datetime64[ns]', length=228, freq=None)
```

看输出结果已经获取到了正确的时间范围内的数据。