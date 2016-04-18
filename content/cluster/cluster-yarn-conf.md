---
title: YARN 资源分配的配置参数
date: 2016-04-18 14:20:11
tag: cluster
---
[TOC]

### 内存资源

#### NodeManager
- `yarn.nodemanager.resource.memory-mb`，节点最大可用内存，默认 8096 MB (按需调整为 16G)   
- `yarn.nodemanager.vmem-pmem-ratio`，虚拟内存率，任务每使用1MB物理内存，最多可使用虚拟内存量，默认为 2.1   
- `yarn.nodemanager.pmem-check-enabled`，是否启动一个线程检查每个任务正使用的物理内存量，如果任务超出分配值，则直接将其杀掉，默认是true   
- `yarn.nodemanager.vmem-check-enabled`，是否启动一个线程检查每个任务正使用的虚拟内存量，如果任务超出分配值，则直接将其杀掉，默认是true

在 Centos/RHEL 6 下，由于虚拟内存的分配策略比较激进，可以调高 `yarn.nodemanager.vmem-pmem-ratio` 或者关闭 `yarn.nodemanager.vmem-check-enabled`。见[这个问题](http://stackoverflow.com/questions/21005643/container-is-running-beyond-memory-limits)。

#### ResourceManager
- `yarn.scheduler.minimum-allocation-mb`，单个任务可申请的最少物理内存量，默认是1024（MB），如果一个任务申请的物理内存量少于该值，则该对应的值改为这个数
- `yarn.scheduler.maximum-allocation-mb`，单个任务可申请的最多物理内存量，默认是8192（MB）。

#### ApplicationMaster
- `mapreduce.map.memory.mb`，分配给 Map Container的内存大小，运行时按需指定
- `mapreduce.reduce.memory.mb`，分配给 Reduce Container的内存大小，运行时按需指定
- `mapreduce.map.java.opts`，运行 Map 任务的 jvm 参数，如 -Xmx，-Xms 等选项
- `mapreduce.reduce.java.opts`，运行 Reduce 任务的 jvm 参数，如-Xmx，-Xms等选项

### CPU 资源
- `yarn.nodemanager.resource.cpu-vcores`，该节点上 YARN 可使用的虚拟 CPU 个数，默认是8
- `yarn.scheduler.minimum-allocation-vcores`，单个任务可申请的最小虚拟CPU个数, 默认是1
- `yarn.scheduler.maximum-allocation-vcores`，单个任务可申请的最多虚拟CPU个数，默认是32

### 参考

- [Hadoop YARN中内存和CPU两种资源的调度和隔离](http://dongxicheng.org/mapreduce-nextgen/hadoop-yarn-memory-cpu-scheduling/)
- [Yarn 内存分配管理机制及相关参数配置](http://blog.csdn.net/suifeng3051/article/details/45477773)
