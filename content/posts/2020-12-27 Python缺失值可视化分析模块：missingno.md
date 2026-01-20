---
title: Pandas缺失值可视化分析模块：missingno
date: 2020-12-27 22:53:00
categories: ["python"]
tags: ["Pandas"]
---

missingno提供了一个灵活且易于使用的丢失数据可视化工具和实用程序的小型工具集，使您可以快速直观地概述数据集的完整性（或缺乏完整性）。只需pip install missingno就可以开始使用了

missingno包含了4种类型图示：
1. Metrix：msno.matrix无效矩阵是一种数据密集显示图，可以直观的看到各个column缺失数据分布情况，这种可视化图示最多可以容纳50个column，超过此范围的标签开始重叠或变得不可读。
    ![](https://raw.githubusercontent.com/eryk/blog_img/master/20201227231432.png)

2. Bar：msno.bar是按列对无效性数据的简单可视化：

   ![](https://raw.githubusercontent.com/eryk/blog_img/master/20201227231524.png)

3. Heatmap：数据列之前缺失相关性分析图，展示了一个变量的存在或不存在对另一个变量的存在有多强烈的影响

     ![](https://raw.githubusercontent.com/eryk/blog_img/master/20201227231600.png)

4. Dendrogram：通过树状图，您可以更全面地关联变量完成，揭示比Heatmap中可见的成对趋势更深的趋势。与metrix图一样，在这种配置下最多只能显示50个带标签的列。但是，树状图只需翻转到水平配置即可更优雅地处理超大型数据集。

     ![](https://raw.githubusercontent.com/eryk/blog_img/master/20201227231612.png)

### 参考

https://github.com/ResidentMario/missingno