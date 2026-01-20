---
title: 'pandas使用pivot时遇到Index contains duplicate entries, cannot reshape'
date: 2017-08-10 16:54:53
categories: ["python"]
tags: ["pandas"]
---

原始数据样例如下：
```
date        code    count
20170801    000001      10
20170802    000002      20
20170803    000001      30
```

使用pivot处理数据，命令如下：
> df.pivot(index='date',columns='code',values='count')

执行后报错信息：
> ValueError: Index contains duplicate entries, cannot reshape

说明column有重复信息，使用如下命令检查重复列内容：

```
df = df.sort_values(['date','code','count'],ascending=[1,1,0])
df = df[(df['code'] == df['code'].shift(1)) | (df['code'] == df['code'].shift(-1))]
```

发现code列有哪些重复数据