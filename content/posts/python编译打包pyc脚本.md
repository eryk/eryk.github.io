---
title: python编译打包pyc脚本
date: 2017-10-27 12:18:00
categories: ["python"]
tags: ["pyc"]
---

```shell
cd $1
python3 -O -m compileall .
find . -name '*.pyc' -exec rename 's/.cpython-36.opt-1//' {} \;
find . -name '*.pyc' -execdir mv {} .. \;
find . -name '*.py' -type f -print -exec rm {} \;
find . -name '__pycache__' -exec rmdir {} \;
zip -r ../$1.zip ./*
```

转自：https://my.oschina.net/bfbd/blog/864310