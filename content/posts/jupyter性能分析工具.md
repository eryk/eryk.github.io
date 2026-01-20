---
title: jupyter性能分析工具
date: 2017-12-05 12:18:00
categories: ["其他"]
---

# magic functions:

1. %time: 记录script运行时间 
2. %timeit: 循环n次，记录平均执行时间
3. %run & %prun: 分析script的执行效率
4. %lprun: 分析一条语句在function中执行效率
5. %mprun: 查看script脚本使用内存量
6. %memit: 循环n次，记录平均内存使用量

# 安装
```
pip install jupyter_contrib_nbextensions
pip install line-profiler
pip install psutil
pip install memory_profiler
```

# jupyter load 插件

```
%load_ext line_profiler
%load_ext memory_profiler
```

# Time Profiling

## time & timeit
```python
In [7]: %time {1 for i in xrange(10*1000000)}
CPU times: user 0.72 s, sys: 0.16 s, total: 0.88 s
Wall time: 0.75 s

In [8]: %timeit 10*1000000
10000000 loops, best of 3: 38.2 ns per loop

In [9]: %timeit -n 1000 10*1000000
1000 loops, best of 3: 67 ns per loop
```

## %prun

```python
In [10]: from time import sleep

In [11]: def foo(): sleep(1)

In [12]: def bar(): sleep(2)

In [13]: def baz(): foo(), bar()

In [14]: %prun baz()
7 function calls in 3.001 seconds

Ordered by: internal time

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     2    3.001    1.500    3.001    1.500 {time.sleep}
     1    0.000    0.000    3.001    3.001 <ipython-input-17-c32ce4852c7d>:1(baz)
     1    0.000    0.000    2.000    2.000 <ipython-input-11-2689ca7390dc>:1(bar)
     1    0.000    0.000    1.001    1.001 <ipython-input-10-e11af1cc2c91>:1(foo)
     1    0.000    0.000    3.001    3.001 <string>:1(<module>)
     1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```

# Memory Profiling

## %mprun

```python
In [17]: %mprun -f foo foo(100000)
Filename: foo.py

Line #    Mem usage    Increment   Line Contents
================================================
     1    20.590 MB     0.000 MB   def foo(n):
     2    20.590 MB     0.000 MB       phrase = 'repeat me'
     3    21.445 MB     0.855 MB       pmul = phrase * n
     4    25.020 MB     3.574 MB       pjoi = ''.join([phrase for x in xrange(n)])
     5    25.020 MB     0.000 MB       pinc = ''
     6    43.594 MB    18.574 MB       for x in xrange(n):
     7    43.594 MB     0.000 MB           pinc += phrase
     8    41.102 MB    -2.492 MB       del pmul, pjoi, pinc
```

在jupyter中运行报如下错误：

```
ERROR: Could not find file <ipython-input-10-6aab78230286>
NOTE: %mprun can only be used on functions defined in physical files, and not in the IPython environment.
```

## %memit

```python
In [18]: %memit -r 3 [x for x in xrange(1000000)]
maximum of 3: 75.320312 MB per loop
```

# 参考

http://jupyter-contrib-nbextensions.readthedocs.io/en/latest/install.html#install-the-python-package

http://mortada.net/easily-profile-python-code-in-jupyter.html

https://www.dataquest.io/blog/jupyter-notebook-tips-tricks-shortcuts/

http://pynash.org/2013/03/06/timing-and-profiling/

http://nbviewer.jupyter.org/gist/jiffyclub/3062428

http://www.xavierdupre.fr/app/mlstatpy/helpsphinx/notebooks/completion_profiling.html

https://pypi.python.org/pypi/memory_profiler/0.41

https://github.com/rkern/line_profiler


