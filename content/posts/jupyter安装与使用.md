---
title: 使用https方式连接jupyter
date: 2017-08-09 09:15:37
categories: ["python"]
tags: ["jupyter"]
---

生成jupyter配置文件:

> jupyter notebook --generate-config

注意：generate前是两个"-"，不知道为什么markdown里是两个，显示的时候成一个了

生成好的配置文件位置在：

> ~/.jupyter/jupyter_notebook_config.py

生成自签名SSL证书：
> cd ~/.jupyter
> openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout notebook_cert.key -out notebook_cert.pem

生成一个登录密码的hash:
> python -c "import IPython;print(IPython.lib.passwd())"

修改jupyter_notebook_config.py配置文件中的如下选项：

```
c.NotebookApp.certfile = u'/home/xxxx/.jupyter/notebook_cert.pem'
c.NotebookApp.keyfile = u'/home/xxxx/.jupyter/notebook_cert.key'
c.NotebookApp.password = u'sha1:991ec9cd2f39:522598e19891bab1ecaa3a9072e71f45811af9f2'
```

重启jupyter，使用浏览器访问：https://your_domain_or_IP:8080

### 参考
http://jupyter-notebook.readthedocs.io/en/latest/public_server.html#notebook-server-security

http://www.linuxdiyf.com/linux/22884.html


