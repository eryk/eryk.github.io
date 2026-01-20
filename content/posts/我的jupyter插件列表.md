---
title: 我的jupyter插件列表
date: 2017-08-13 16:25:19
categories: ["python"]
tags: ["jupyer"]
---

# jupyter notebook extensions 安装

## pip方式安装

> pip install jupyter_contrib_nbextensions

或者通过github master branch安装

> pip install https://github.com/ipython-contrib/jupyter_contrib_nbextensions/tarball/master

## Conda方式安装

> conda install -c conda-forge jupyter_contrib_nbextensions

## 通过github 仓库安装
```
git clone https://github.com/ipython-contrib/jupyter_contrib_nbextensions.git
pip install -e jupyter_contrib_nbextensions
```

# 常用插件列表

全部jupyter插件列表在这里： 
> http://jupyter-contrib-nbextensions.readthedocs.io/en/latest/nbextensions.html

我觉得有用的插件列表：

* Autopep8

    格式化代码插件
* Codefolding 和 Codefolding in Editor

    折叠代码插件
* Collapsible Headings

    Collapsible Headings icon
Allows notebook to have collapsible sections, separated by headings
* contrib_nbextensions_help_item：帮助
* Drag and Drop
    
    This extension allows dragging&dropping images from the desktop or other programs into a notebook.
* ExecuteTime：

    Display when each cell has been executed and how long it took
 
* Freeze

    Freeze cells (forbid editing and executing) or make them read-only

* highlighter

    markdown内容高亮
    
* Hinterland

    实时的自动补全，很不错

* Nbextensions dashboard tab & Nbextensions edit menu item

* ScrollDown

    自动向下滚动输出

* Snippets & Snippets Menu

    插入代码片段的，可以自定义
    
* Table of Contents (2)

    The toc2 extension enables to collect all running headers and display them in a floating window, as a sidebar or with a navigation menu. The extension is also draggable, resizable, collapsable, dockable and features automatic numerotation with unique links ids, and an optional toc cell.
    
* table_beautifier

    Add bootstrap styling to tables in markdown cells and in html/md output
    
* Toggle all line numbers

    Add a toolbar button and hotkey to toggle all cells' line numbers on or off
    
* Tree Filter

    An extension that allows you to filter by filename in the Jupyter notebook file tree (aka dashboard) page.

* Variable Inspector

    The Variable Inspector extension collects all defined variables and display them in a floating window.