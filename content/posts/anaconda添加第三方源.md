---
title: anaconda添加第三方源
date: 2017-08-19 15:42:36
categories: ["python"]
tags: ["Anaconda"]
---

Anaconda 是一个用于科学计算的 Python 发行版，支持 Linux, Mac, Windows, 包含了众多流行的科学计算、数据分析的 Python 包。

Anaconda 安装包可以到 https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/ 下载。

TUNA 还提供了 Anaconda 仓库的镜像，运行以下命令:

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/

conda config --set show_channel_urls yes
```
说明：

conda-forge: 
> A community led collection of recipes, build infrastructure and distributions for the conda package manager.