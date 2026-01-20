---
title: Multiprocessing Pipe和Queue性能测试
date: 2019-04-22 10:47:00
categories: ["python"]
---

我的微信公众号：pyquant

![](https://raw.githubusercontent.com/eryk/blog_img/master/20190424095328.jpg)

# 背景

开发股票行情推送的引擎时遇到一个问题，在9:30开盘后的一段时间内行情消息总是堆积，尤其是开头15-20分钟，堆积的数据量会越来越多，经过debug发现是内部消息传输使用Queue性能问题导致了消息延迟，在stackoverflow上找到一个帖子对Queue的性能进行了测试和解释说明，下面先来介绍下Multiprocessing下的Queue和Pipe

# 介绍

当使用多个进程时，通常使用消息传递来进行进程之间的通信，为了不损耗性能也会尽量避免使用同步机制。对于消息传递：

	* Pipe适用于两个进程间的消息传递。
	* Queue适用于多个进程间的消息传递，适用于多生产者和消费者的模式。



# Pipe VS Queue

```python
from multiprocessing import Process, Pipe
import time

def reader_proc(pipe):
    ## Read from the pipe; this will be spawned as a separate Process
    p_output, p_input = pipe
    p_input.close()  # We are only reading
    while True:
        msg = p_output.recv()  # Read from the output pipe and do nothing
        if msg == 'DONE':
            break

def writer(count, p_input):
    for ii in range(0, count):
        p_input.send(ii)  # Write 'count' numbers into the input pipe
    p_input.send('DONE')

if __name__ == '__main__':
    for count in [10 ** 4, 10 ** 5, 10 ** 6]:
        # Pipes are unidirectional with two endpoints:  p_input ------> p_output
        p_output, p_input = Pipe()  # writer() writes to p_input from _this_ process
        reader_p = Process(target=reader_proc, args=((p_output, p_input),))
        reader_p.daemon = True
        reader_p.start()  # Launch the reader process

        p_output.close()  # We no longer need this part of the Pipe()
        _start = time.time()
        writer(count, p_input)  # Send a lot of stuff to reader_proc()
        p_input.close()
        reader_p.join()
        print("Sending {0} numbers to Pipe() took {1} seconds".format(count,
                                                                      (time.time() - _start)))

```

### Pipe输出结果

```shell
Sending 10000 numbers to Pipe() took 0.0744009017944336 seconds
Sending 100000 numbers to Pipe() took 0.7794349193572998 seconds
Sending 1000000 numbers to Pipe() took 7.425454139709473 seconds
```

```python
from multiprocessing import Process, Queue
import time
import sys

def reader_proc(queue):
    ## Read from the queue; this will be spawned as a separate Process
    while True:
        msg = queue.get()  # Read from the queue and do nothing
        if (msg == 'DONE'):
            break

def writer(count, queue):
    ## Write to the queue
    for ii in range(0, count):
        queue.put(ii)  # Write 'count' numbers into the queue
    queue.put('DONE')

if __name__ == '__main__':
    pqueue = Queue()  # writer() writes to pqueue from _this_ process
    for count in [10 ** 4, 10 ** 5, 10 ** 6]:
        ### reader_proc() reads from pqueue as a separate process
        reader_p = Process(target=reader_proc, args=((pqueue),))
        reader_p.daemon = True
        reader_p.start()  # Launch reader_proc() as a separate python process

        _start = time.time()
        writer(count, pqueue)  # Send a lot of stuff to reader()
        reader_p.join()  # Wait for the reader to finish
        print("Sending {0} numbers to Queue() took {1} seconds".format(count,
                                                                       (time.time() - _start)))

```

#### Queue 输出结果

```shell
Sending 10000 numbers to Queue() took 0.2558887004852295 seconds
Sending 100000 numbers to Queue() took 2.4320709705352783 seconds
Sending 1000000 numbers to Queue() took 23.602338075637817 seconds
```

让我们把结果整理成表格方便对比查看:

| 循环次数 |   Pipe |   Queue |
| -------: | -----: | ------: |
|    10000 | 0.0744 |  0.2558 |
|   100000 | 0.7794 |  2.4320 |
|  1000000 | 7.4254 | 23.6023 |

通过对比测试可以发现，Pipe性能大约为Queue的3倍，所以在仅有两端通信的情况下应该优先使用Pipe。

# 源码分析

通过阅读Queue的源码，我们可以发现，其实在Queue内部是用Lock来实现对Pipe的安全读写操作的。所以相比于Pipe会有额外的锁的开销。

```python
class Queue(object):

    def __init__(self, maxsize=0, *, ctx):
        if maxsize <= 0:
            # Can raise ImportError (see issues #3770 and #23400)
            from .synchronize import SEM_VALUE_MAX as maxsize
        self._maxsize = maxsize
        self._reader, self._writer = connection.Pipe(duplex=False) # 这里初始化了Pipe对象
        self._rlock = ctx.Lock()
        self._opid = os.getpid()
        if sys.platform == 'win32':
            self._wlock = None
        else:
            self._wlock = ctx.Lock()
        self._sem = ctx.BoundedSemaphore(maxsize)
        # For use by concurrent.futures
        self._ignore_epipe = False
        self._after_fork()
        if sys.platform != 'win32':
            register_after_fork(self, Queue._after_fork)

    def put(self, obj, block=True, timeout=None):
        if self._closed:
            raise ValueError(f"Queue {self!r} is closed")
        if not self._sem.acquire(block, timeout):
            raise Full

        with self._notempty:
            if self._thread is None:
                self._start_thread()
            self._buffer.append(obj)
            self._notempty.notify()

    def get(self, block=True, timeout=None):
        if self._closed:
            raise ValueError(f"Queue {self!r} is closed")
        if block and timeout is None:
            with self._rlock:
                res = self._recv_bytes()
            self._sem.release()
        else:
            if block:
                deadline = time.monotonic() + timeout
            if not self._rlock.acquire(block, timeout):
                raise Empty
            try:
                if block:
                    timeout = deadline - time.monotonic()
                    if not self._poll(timeout):
                        raise Empty
                elif not self._poll():
                    raise Empty
                res = self._recv_bytes()
                self._sem.release()
            finally:
                self._rlock.release()
        # unserialize the data after having released the lock
        return _ForkingPickler.loads(res)
```

# 参考

<https://stackoverflow.com/questions/8463008/multiprocessing-pipe-vs-queue>

