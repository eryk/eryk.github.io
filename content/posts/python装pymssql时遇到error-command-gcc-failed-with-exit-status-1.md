---
title: 'python装pymssql时遇到error: command ''gcc'' failed with exit status 1'
date: 2017-08-17 16:15:54
categories: ["python"]
---
执行命令:

```shell
pip install pymssql
```

报错信息如下：

```
  Running setup.py install for pymssql ... error
    Complete output from command /home/lthpc/anaconda3/bin/python -u -c "import setuptools, tokenize;__file__='/tmp/pip-build-cpwncwyq/pymssql/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record /tmp/pip-4l_hdx9q-record/install-record.txt --single-version-externally-managed --compile:
    setup.py: platform.system() => 'Linux'
    setup.py: platform.architecture() => ('64bit', 'ELF')
    setup.py: platform.linux_distribution() => ('debian', 'stretch/sid', '')
    setup.py: platform.libc_ver() => ('glibc', '2.2.5')
    setup.py: Not using bundled FreeTDS
    setup.py: include_dirs = ['/usr/local/include']
    setup.py: library_dirs = ['/usr/local/lib']
    running install
    running build
    running build_ext
    cythoning _mssql.pyx to _mssql.c
    warning: _mssql.pyx:143:4: Exception already a builtin Cython type
    building '_mssql' extension
    creating build
    creating build/temp.linux-x86_64-3.6
    gcc -pthread -Wsign-compare -DNDEBUG -g -fwrapv -O3 -Wall -Wstrict-prototypes -fPIC -I/usr/local/include -I/home/lthpc/anaconda3/include/python3.6m -c _mssql.c -o build/temp.linux-x86_64-3.6/_mssql.o -DMSDBLIB
    _mssql.c:435:22: fatal error: sqlfront.h: No such file or directory
    compilation terminated.
    error: command 'gcc' failed with exit status 1
```
google得到以下解决办法：

https://stackoverflow.com/questions/17368964/trying-to-install-pymssql-on-ubuntu-12-04-using-pip

尝试执行命令安装FreeTDS依赖包：

> sudo apt-get install freetds-dev

如果安装过程出现依赖包版本的问题请先更新ubuntu软件源

> sudo apt-get update

再次执行pymssql，安装成功:

```shell
$ pip install pymssql
Collecting pymssql
  Using cached pymssql-2.1.3.tar.gz
Building wheels for collected packages: pymssql
  Running setup.py bdist_wheel for pymssql ... done
  Stored in directory: /home/lthpc/.cache/pip/wheels/c1/1e/75/bc600eb8a5c9ed77fb1edf15ae5a48b5b427b0390c9a7c9dff
Successfully built pymssql
Installing collected packages: pymssql
Successfully installed pymssql-2.1.3
```


