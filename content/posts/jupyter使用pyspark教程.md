---
title: jupyter使用pyspark教程
date: 2017-08-23 14:24:21
tags:
---

    启动jupyter遇到的问题
    ```
    Exception in thread "main" java.lang.NoSuchMethodError: scala.collection.immutable.HashSet$.empty()Lscala/collection/immutable/HashSet;
    ```
    原因是因为spark依赖的scala版本与系统的不匹配，我的系统环境中scala版本是2.11.11，spark2.2.0目录下jars的scala版本是2.11.8

