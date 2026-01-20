---
title: 使用pycharm远程部署运行python程序
date: 2021-03-06 16:55:00
categories: ["python"]
tags: ["PyCharm"]
---

通常我使用笔记本电脑编写程序代码，而数据采集是由一台服务器进行，为了方便开发调试，需要把程序部署到服务器上运行。这时候就用到了pycharm的远程部署调试功能。

### 1. 配置远程服务器

在pycharm中Perferences -> Build, Execution, Deployment -> Deployment菜单下添加远程服务器配置，Type选择SFTP这样可以通过ssh方式将代码传到服务器上

![image-20210306171219402](/Users/eryk/Library/Application Support/typora-user-images/image-20210306171219402.png)

填写服务器地址、端口（ssh服务端口默认是22）、用户名和密码，密码如果配置了免秘钥登录可以选择“OpenSSH config and authentication”方式，否则选择password输入密码就可以，设置好之后点Test Connection验证如果可以连通则会弹出如下截图：

![image-20210306171534805](/Users/eryk/Library/Application Support/typora-user-images/image-20210306171534805.png)

说明已经可以连上服务器了，默认部署在服务器登录用户的根目录下，如有需要部署在其他位置可以修改Root path参数。设置好之后点ok保存配置就完成了第一步。

### 2. 设置代码自动上传

![image-20210306172025658](/Users/eryk/Library/Application Support/typora-user-images/image-20210306172025658.png)

打开菜单栏中Tools -> Deployment，勾选“Automatic Upload(always)”，这样每次保存代码有更新都会上传到服务器上，始终保持服务器和本地代码一致。点击“Brown Remote Host” 可以在pycharm 右侧弹出标签栏中看到远程服务器上目录内容。

### 3. 配置远程Python Interpreter

在pycharm中Perferences -> Project: 项目名 -> Project Interpreter，在右侧点击添加，如下步骤1所示位置

![image-20210306173647677](/Users/eryk/Library/Application Support/typora-user-images/image-20210306173647677.png)

弹出“Add Python Interpreter” 对话框，在 “Existing server configuration” 中选择刚添加的服务器，如上图步骤2所示。然后点next，

接下来需要设置服务器上Python所在路径和程序自动同步的位置，这里建议和之前deploy保持一致。

![](https://raw.githubusercontent.com/eryk/blog_img/master/image-20210306175450467.png)

点击Finish就可以看到已经添加好了Remote Python，如下图所示

![](https://raw.githubusercontent.com/eryk/blog_img/master/image-20210306175627425.png)

### 4. 运行Python远程代码

编写测试代码，新建mian.py，代码内容是输出当前程序所在路径，测试一下程序是否在服务器上运行

```python
import os

print(os.system('pwd'))

```

![image-20210306180021381](https://raw.githubusercontent.com/eryk/blog_img/master/image-20210306180021381.png)

![image-20210306180103879](https://raw.githubusercontent.com/eryk/blog_img/master/image-20210306180103879.png)

设置“Edit Configuration” ，将Python interpreter设置为刚添加的远程服务器的python，运行代码，输出如下说明已经在远程服务器上运行成功了。

![image-20210306180258162](https://raw.githubusercontent.com/eryk/blog_img/master/image-20210306180258162.png)