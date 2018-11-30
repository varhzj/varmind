# Hotsport垃圾收集器整理

## 概述

HotSpot垃圾收集器的分类：

- 新生代：

    Serial，ParNew，ParallelScavenge，G1

- 老年代：

    CMS，SerialOld，ParallelOld，G1

## 各收集器特点

### Serial收集器

- 新生代收集器
- 单线程收集
- JDK1.3.1之前的新生代GC的唯一选择
- Client模式下一个很好的选择
- 可以和老年代CMS、SerialOld配合使用

### ParNew收集器

- 新生代收集器
- Serial收集器的多线程版本
- 是设置老年代CMS收集器（-XX:UseConcMarkSweepGC）后新生代默认使用的垃圾收集器
- 通过-XX:UseParNewGC指定
- 使用-XX:ParallelGCThreads参数限制垃圾收集的线程数
- 能与老年代CMS、SerialOld收集器配合使用

### ParallelScavenge收集器

- 新生代收集器
- 目标是达到一个可控制的吞吐量，吞吐量=运行用户代码的时间/(运行用户代码时间 + 垃圾收集时间)
- 停顿时间越短越适合与用户交互的程序，而高吞吐量则可以高效利用CPU时间，所以ParallelScavenge收集器适合在后台运算而不需要太多交互的场景
- 通过-XX:MacGCPauseMillis来控制GC停顿时间，-XX:GCTimeRatio设置吞吐量
- 通过设置-XX:+UseAdaptiveSizePolicy，不再需要手工设置新生代大小(-Xmn)、Eden和Survivor区的比例(-XX:SurvivorRatio)、晋升老年代的年龄(-XX:MaxTenuringThreshold)等细节参数
- 与老年代SerialOld和ParallelOld收集器配合使用

### SerialOld收集器

- Serial收集器的老年代版本，单线程，使用"标记-整理"算法
- 在JDK1.5以及之前的版本中与ParallelScavenge收集器搭配使用
- 作为CMS收集器的预备方案，在并发收集发生Concurrent Mode Failure时使用
- 可与新生代Serial、ParNew、ParallelScavenge配合使用(万金油~~)

### ParallelOld收集器

- ParallelScavenge收集器的老年代版本
- 与新生代ParallelScavenge配合使用，适用与注重吞吐量以及CPU资源敏感的场景

### CMS收集器

- 老年代收集器，以获取最短回收停顿时间为目标的收集器
- 主要过程

  - 初始标记:(StopTheWorld)
  - 并发标记
  - 重新标记:(StopTheWorld)
  - 并发清除

- 缺点

  - 基于"标记-清除"算法实现的收集器，会产生大量的空间碎片
  - 无法处理浮动垃圾。由于并发清除中程序还在运行，此时产生的垃圾在当次GC中无法清除，只能留待下一次GC进行收集
  - CPU资源敏感，会占用一部分的线程，使总吞吐量降低

### G1收集器

- 面向服务端应用的垃圾收集器，替换CMS
- 特点
  - 并行与并发
  - 分代收集
  - 空间整合：从整体是基于"标记-整理"算法实现，从局部上看是基于"复制"算法实现
  - 可预测的停顿

- 过程
  - 初始标记
  - 并发标记
  - 最终标记
  - 筛选回收
