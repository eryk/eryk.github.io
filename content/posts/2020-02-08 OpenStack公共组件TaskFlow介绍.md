---
title: OpenStack公共组件TaskFlow介绍
date: 2020-02-08 23:13:00
categories: ["python"]

---

TaskFlow 是一个以高度可用，易于理解和声明试方式来执行[作业，任务，流程]的库，可与OpenStack和其他项目一起使用。

# 基本概念

taskflow库在oslo项目中是一个实现比较复杂的项目，要弄清楚其实现原理，首先需要对其中的相关概念有所了解。所以，本文首先总结了taskflow中常用的一些基本概念，这些概念主要包括如下几个：

## Atom

Atom类是taskflow的最小单位，taskflow中其他类，包括Task等都需要继承这个类。一个Atom对象是一个命名对象，通过操作输入数据以执行一些促进整个流程发展的动作，或者产生一个处理结果等。它是一个抽象类，提供了两个抽象方法：execute()用于执行一个动作，revert()用于根据execute()执行结果和失败信息还原到任务执行之前的状态；除此之外，还分别为这两个方法提供了pre_execute()/post_execute()、pre_revert()/post_revert()方法用于定义在执行execute或revert操作前后执行的操作。

## Task

Task类是一个拥有执行和回滚操作的最小工作单元，表示一个任务流中的某一个任务。它是一个继承自Atom类的表示一个任务的父类，开发者可以执行定义一个继承自Task类的任务类，并重写execute()和revert()方法分别表示执行和回滚的操作。

![Task outline.](https://docs.openstack.org/taskflow/latest/_images/tasks.png)

#### Task的两种类型:

* Task: 对于继承和创建自己的子类很有用。
* FunctorTask: 对于将现有function包装到任务对象中很有用，但是不能应用在engine中

## Retry

Retry类也是一个继承自Atom的抽象类，它主要定义了当有错误发生时，如何进行重试操作。其也包含也不同的类型，将会在接下来的部分进行详细介绍。继承重试的子类必须提供on_failure()函数来对故障进行处理。

#### 为避免重复创建常见的重试模式，提供了以下常见的重试子类：

* AlwaysRevert: 始终还原subflow.

* AlwaysRevertAll: 始终还原整个flow

* Times: 对subflow重试指定次数

* ForEach: 允许在每次发生故障时为subflow提供不同的值（使其有可能通过更改subflow输入来解决故障）

* ParameterizedForEach: 和ForEach类似，但是从存储中获取值.

  ![Inheritance diagram of taskflow.atom, taskflow.task, taskflow.retry.Retry, taskflow.retry.AlwaysRevert, taskflow.retry.AlwaysRevertAll, taskflow.retry.Times, taskflow.retry.ForEach, taskflow.retry.ParameterizedForEach](https://docs.openstack.org/taskflow/latest/_images/inheritance-18bf8f4505f8b8f3ecd1914b3ff8965622138a35.png)

关于重试的策略，taskflow通过一个枚举类型的Decision定义了三种策略：

* REVERT：仅回滚失败Flow对象周围或关联的子流Flow对象。该策略在回滚子流Flow对象之前，会首先咨询其父Atom对象以确定父Atom对象是否使用不同的重试策略。该策略允许安全的嵌套具有不同重试策略的Flow对象。如果父Atom对象中没有定义重试策略，则默认只回滚关联子流Flow对象中的Atom对象。当然，你可以通过defer_revert参数改变默认行为，当其设置为True，表示REVERT策略将继承父Atom的策略，如果父Atom对象没有重试策略，则它也将被回滚。
* REVERT_ALL：不管失败Flow对象的父Atom对象的策略如何，都将回滚整个流程。
* RETRY：重试该失败的Flow/Task对象。

## Flow

Flow类是一个用来关联所有相关Task类，并规定这些Task类的执行和回滚顺序的抽象类。而oslo中为Flow提供了三种实现方式：graph_flow表示图流，linear_flow表示线性流，unordered_flow表示无序流。关于这三种类型的流实现会在之后进行详细分析。

![Inheritance diagram of taskflow.flow, taskflow.patterns.linear_flow, taskflow.patterns.unordered_flow, taskflow.patterns.graph_flow](https://docs.openstack.org/taskflow/latest/_images/inheritance-d843124115abb8d565dc2aea882f408d4b57072b.png)

* linear_flow：线性流，该类型的Flow对象将按照Task/Flow加入的顺序来依次执行，按照加入的倒序依次回滚。
* graph_flow：图流，该类型的Flow对象会按照给加入的Task/Flow显示指定的依赖关系或通过其间的provides/requires属性隐含的依赖关系执行和回滚。
* unordered_flow：无序流，该类型的Flow对象所加入的Task/Flow会按照任意顺序执行或回滚。

要弄清楚这三种类型的Flow对象，首先需要了解oslo定义的Flow基类的构成。在oslo定义的Flow基类中，主要包含以下几个重要的属性和方法：

* name：表示初始化Flow对象时，为其指定的名称，并不能唯一表示一个Flow对象。
* retry：表示与该Flow对象关联的重试控制器。
* provides：表示该Flow对象提供的一组符号名称。
* requires：表示该Flow对象所需要的一组"unsatisfied"符号名称。
* add(*items)：该方法用于为该Flow对象添加一个或一组Task/Flow对象。
* iter_links()：迭代Flow对象的子节点之间的依赖关系链接。例如在迭代一个三元组(A, B, meta)时，就是迭代一个从子节点A（一个Atom对象或一个Subflow）指向子节点B（一个Atom对象或一个Subflow）的链接；换句话说，也就代表了子流B依赖于子流A，或者子流B需要子流A；而meta代表了这个依赖关系链接的元数据，是一个字典。
* iter_nodes()：迭代Flow对象中的所有节点。例如在迭代一个二元组（A, meta）时，A（一个Atom对象或一个Subflow）是当前Flow对象的子流或子任务；meta同样代表了这个链接的元数据，是一个字典。
  

## Engine

Engine类是一个表示真正运行Atom对象的抽象类，它的实现类主要用于载入（load）一个Flow对象，然后驱动这个Flow对象的Task对象开始运行。Engine的实现也有多种不同的形式，这也会在接下来的部分进行详细介绍。

taskflow在具体实现Task/Flow管理时，首先定义了一个Engine抽象类，所有实现都需要继承这个抽象类。这个抽象类定义了如下重要属性和方法：

* notifier：一个通知对象，它会分发与Engine对象中包含的Flow对象相关的事件通知。
* atom_notifier：一个通知对象，它会分发与Engine对象中包含的Atom对象相关的事件通知。
* options：相关数据结构传递给Engine对象的选项。
* storage：Engine对象的存储单元。
* statistics：Engine对象收集的运行时统计数据字典。当Engine没有运行时，这个值为空；在Engine正在运行时或已经运行之前，它可能会存储一些对正在运行或运行完成时有用的或包含信息的键值对。
* compile()：该方法可以将Engine对象中包含的Flow对象编译成Engine对象内部表示形式。这个内部表示形式就是Engine对象实际用于运行的流的形式。
* reset()：将Engine对象重置为PENDING状态。如果一个Flow以FAILURE、SUCCESS、REVERTED状态结束运行（即调用Engine对象的run()方法之后），或由于某种状态使得其处于某种中间状态，此时可以调用reset()方法进行重置，然后进行重试操作。
* prepare()：在Engine对象编译完所有包含的Flow对象之后，且在Flow运行之前执行该方法，为流程的执行进行一些准备操作。
* validate()：在Engine对象编译完所有包含的Flow对象之后，且在Flow运行之前执行该方法，为流程的执行进行一些验证操作。
* run()：运行Engine对象中的Flow流程。
* suspend()：该方法尝试暂停Engine对象。如果一个Engine对象正在执行某个Atom对象，则执行该方法会将这个Atom对象之后的所有正要运行的工作都暂停，并将这个Engine对象的状态变为暂停状态，以便之后进行恢复操作。

taskflow在具体实现Engine时，都需要给上述属性和方法重新赋值或进行覆写操作，以实现一个完整的管理流程Flow/Task对象的Engine类。在taskflow中，目前实现了三种策略的Engine类，而在这三种策略中，有两种是面向行为的action_egine类：SerialActionEngine、ParallelActionEngine；另一种是面向多进程的worker_base类：WorkerBaseActionEngine。这三种类型的Engine类的异同点如下所示：

* SerialActionEngine：这是一个以串行方式运行任务的Engine类，也就是说所有的任务都会在调用engine.run()方法的线程中顺序执行。
* ParallelActionEngine：这是一个以并行方式运行任务的Engine类，即可以在多个线程中运行Engine对象中的任务。在这种策略中，taskflow定义了对应的多个ParallelThreadTaskExecutor创建运行任务的线程
* WorkerBaseActionEngine：这是一个可以将任务调度到不同worker（即进程）中执行的Engine类。
