# G1 垃圾收集器(Garbage-First Garbahe Collection)

## 1. 介绍

> The Java™ programming language is widely used in large server applications. These applications are characterized by large amounts of live heap data and considerable thread-level parallelism, and are often run on high-end multiprocessors. Throughput is clearly important for such applications, but they often also have moderately stringent (thoughsoft) real-time constraints, e.g. in telecommunications, call-processing applications (several of which are now implemented in the Java language), delays of more than a fraction of a second in setting up calls are likely to annoy customers.

Java程序广泛的用于大型服务器应用开发。这些应用拥有巨大的堆空间和较高的线程并行度，并且运行在高性能多核设备上。很明显，吞吐量对这些应用至关重要，但它们通常还有适度严格的实时限制，比如在电话系统中，呼叫处理程序建立请求延迟超过零点几秒就会让消费者感到不快。

> The Java language specication mandates some form of garbage collection to reclaim unused storage. Traditional "stop-world" collector implementations will affect an application's responsiveness, so some form of concurrent and/or incremental collector is necessary. In such collectors, lower pause times generally come at a cost in throughput. Therefore, we allow users to specify a soft real-time goal, stating their desire that collection consume no more than x ms of any y ms time slice. By making this goal explicit, the collector can try to keep collection pauses as small and infrequent as necessary for the application, but not so low as to decrease throughput or increase footprint unnecessarily. This paper describes the Garbage-First collection algorithm, which attempts to satisfy such a soft real-time goal while maintaining high throughput for programs with large heaps and high allocation rates, running on large multi-processor machines.

java将回收未使用空间的任务委托给垃圾收集程序。传统的“stop-world”型收集器实现将会严重影响程序的响应能力，所以某种形式的并发渐进式收集器显得很有必要。在这一类收集器中，往往要在吞吐量上付出一定代价来换取低延时。因此，我们允许用户特别指定一个近实时的目标，申明用户期望收集器在yms的时间片段内耗费不超过xms的时间来完成收集。通过设定目标，收集器可以尝试在必要的前提下将延时维持在既短又低的频率下，同时也不会太短以至于降低吞吐量或者增加不必要的操作。本论文描述一种垃圾优先回收算法，使得运行在大型多核设备上的程序在大堆空间和高分配频率下既保持高吞吐量，又能尝试满足软实时的目标。

> The Garbage-First collector achieves these goals via several techniques. The heap is partitioned into a set of equalsized heap regions, much like the train cars of the Mature-Object Space collector of Hudson and Moss. However, whereas the remembered sets of the Mature-Object Space collector are unidirectional, recording pointers from older regions to younger but not vice versa, Garbage-First remembered sets record pointers from all regions (with some exceptions, described in sections 2.4 and 4.6). Recording all references allows an arbitrary set of heap regions to be chosen for collection. A concurrent thread processes log records created by special mutator write barriers to keep remembered sets up-to-date, allowing shorter collections.

G1垃圾回收算法通过以下几种技术实现上述目标：

> Garbage-First uses a snapshot-at-the-beginning (henceforth SATB) concurrent marking algorithm. This provides periodic analysis of global reachability, providing completeness, the property that all garbage is eventually identied. The concurrent marker also counts the amount of live data in each heap region. This information informs the choice of which regions are collected: regions that have little live data and more garbage yield more ecient collection, hence the name \Garbage-First". The SATB marking algorithm also has very small pause times.

> Garbage-First employs a novel mechanism to attempt to achieve the real-time goal. Recent hard real-time collectors have satised real-time constraints by making collection interruptible at the granularity of copying individual objects, at some time and space overhead. In contrast, Garbage-First copies objects at the coarser granularity of heap regions. The collector has a reasonably accurate model of the cost of collecting a particular heap region, as a function of quickly-measured properties of the region. Thus, the collector can choose a set of regions that can be collected within a given pause time limit (with high probability). Further, collection is delayed if necessary (and possible) to avoid violating the real-time goal. Our belief is that abandoning hard real-time guarantees for this softer best-effort style may yield better throughput and space usage, an appropriate tradeo for many applications.

## 2. 数据结构

### 2.1 堆空间布局/Regios/分配方法

### 2.2 Remembered Set

### 2.3 擦除暂停

### 2.4 垃圾优先

### 2.5 并发标记

#### 2.5.1 标记数据结构

#### 2.5.2 初始标记暂停/并发标记

#### 2.5.3 并发标记写栅栏

#### 2.5.4 最终标记暂停

#### 2.5.5 生存数据计数和清理

### 2.6 清除暂停和标记

### 2.7 流行对象处理

## 3. 算法

### 3.1 用户输入

### 3.2 确保软实时目标

#### 3.2.1 预测回收暂停时间

#### 3.2.2 计划暂停以满足软实时目标

### 3.3 回收集合选择

### 3.4 擦除暂停开始

## 4. 性能评估

### 4.1 评估应用

### 4.2 软实时目标比较

### 4.3 吞吐量

### 4.4 并行度

### 4.5 容量处理

### 4.6 remember set Obverhead

## 5. 相关工作

## 6. 结论和将来的工作
