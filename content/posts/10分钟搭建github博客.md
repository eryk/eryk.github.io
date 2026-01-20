---
title: 10分钟搭建github博客
date: 2017-08-18 10:16:44
categories: ["其他"]
---

### 搭建github博客，参考如下步骤

    https://pages.github.com/

### 注册域名，修改域名DNS地址

找个域名服务商注册个域名，我使用的是godaddy，如果以后需要指向国内空间，建议在国内域名服务商注册。
    
注册好域名后，可以直接将域名cname到github博客地址，也可以使用dnspod.com的dns解析域名，在域名管理中把dns地址修改dnspod的dns：


```
    a.dnspod.com
    b.dnspod.com
    c.dnspod.com
```

然后需要在github上做个设置，把域名绑定到github账号上，否则直接访问域名会报404，具体设置步骤如下：

https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/   

### 使用hexo管理博客

Hexo是一个简单、快速、强大的基于 Github Pages 的博客发布工具，支持Markdown格式，有众多优秀插件和主题。

hexo安装步骤参考：

https://hexo.io/

推荐next博客主题，我的博客就是使用的这个主题，地址是：

https://github.com/iissnan/hexo-theme-next

### 设置_config.yml和github sshkey

```
deploy:
  type: git
  repo: https://github.com/YourgithubName/YourgithubName.github.io.git
  branch: master
```

hexo发布时会把更新提交到仓库，所以需要设置github sshkey

### 发布文章

基本命令如下：

进入blog目录下，执行如下命令创建一篇博客
```
hexo new post 文章标题
```
创建好后可以在markdown工具中编写文章，macbook下推荐用MWeb来写博客，MWeb有两种模式：文档库和外部模式，使用外部模式到blog目录下就可以编写了

编写好博客后发布：
```
hexo d -g
```

等待几秒中后刷新博客地址就可以看到更新了。

