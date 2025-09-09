---
title: Spark技术原理
date: 2025-09-09 15:07:26
category: Tools
tags:
- Spark
- RDD
---

本文整理了 Spark 的核心机制，涵盖 RDD 的设计思想、存储方式、不可变性、Stage 概念，以及 Spark 相对于 MapReduce 的改进。

来源于 Spark 最核心的两篇学术论文：

1. **Zaharia, M., et al. (2012). *Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing*. NSDI.**
   这是 Spark 的“基础论文”，提出了 RDD 的概念。

2. **Zaharia, M., et al. (2010). *Spark: Cluster Computing with Working Sets*. HotCloud.**
   这是 Spark 的早期论文，主要论证了基于内存的数据处理框架可以比 MapReduce 更快。

<!--more-->

## 一、Spark 的技术原理

在 Spark 出现之前，Hadoop MapReduce 已被广泛用于大规模批处理。但是在若干场景下，MapReduce 的开销和表达能力变成瓶颈：

* **迭代式算法昂贵**：机器学习、图计算等需要多次扫描相同中间数据；MapReduce 每一步通常把中间结果写磁盘，I/O 成本极高。
* **交互式分析迟滞**：需要低延迟探索式查询时，MapReduce 高延迟不适合。
* **编程表达力有限**：MapReduce 编程模型主要围绕 map/reduce，两阶段模板不能自然表达复杂数据流。
* **资源利用与调度粒度粗**：每个作业开闭代价大，难以复用内存中已生成的数据。

### 1. 核心抽象：RDD（Resilient Distributed Dataset）

* **分布式集合**：RDD 是一个不可变、分区化的数据集合，可以分布在集群不同节点上。
* **容错机制**：RDD 不依赖于数据复制（replication）来实现容错，而是通过 **lineage（血缘记录）**。即每个 RDD 都知道自己是如何由上游数据转换而来的，一旦分区丢失，可以通过 lineage 重新计算。
* **操作**分为两类：

  * *Transformations*（转换）：如 `map`、`filter`、`join`，延迟执行（lazy evaluation）。
  * *Actions*（动作）：如 `count`、`collect`、`save`，触发计算。

### 2. 内存计算

* Spark 在执行时尽量将中间结果保存在 **内存** 中，避免频繁的磁盘读写。
* 对迭代式计算（机器学习、图计算）性能优势明显，因为可以反复访问缓存数据。

### 3. 调度与执行

* Spark 用 **有向无环图（DAG）** 表示计算流程。
* 与 MapReduce 的两个阶段（map → reduce）不同，Spark 的 DAG 可以包含多个转换，系统会根据数据依赖关系自动划分 stage 并优化调度。

## 二、RDD是什么？

* **逻辑数据集**：RDD 表示一个不可变、分区化的分布式数据集合（logical view），不是传统意义上总是把数据驻留在某个全局容器中。
* **不可变（immutable）与纯函数式转换**：所有转换（`map`、`filter` 等）产生新的 RDD，不修改原 RDD。
* **延迟求值**：transformations 是惰性的，action（如 `count`、`collect`）触发计算。
* **lineage（血缘）**：每个 RDD 保存如何从上游 RDD 生成自己的转换链（依赖信息），用于恢复和优化。
* **StorageLevel**：决定缓存策略（`MEMORY_ONLY`、`MEMORY_AND_DISK`、序列化等）。

### 1. 抽象层面：RDD 不是“存数据”的容器

* RDD（Resilient Distributed Dataset）这个名字容易让人误解，以为它像数据库表那样真正保存数据。
* **实际上，RDD 更像是“数据的逻辑视图 + 元数据”**，它自己并不直接保存数据，而是：

  * 保存数据的**分区信息**（partitions），
  * 记录如何从上游数据计算得到这些分区的**依赖关系（dependencies）** 和 **计算逻辑（compute 方法）**，
  * 保存数据在集群上的**存储策略**（storage level：内存、磁盘、序列化等）。

换句话说：

> RDD = 数据分区描述 + lineage 依赖 + 计算方法 + 存储级别。

### 2. RDD 内部的数据结构（源码角度）

在 Spark 源码中，RDD 是一个抽象类（`org.apache.spark.rdd.RDD[T]`），核心字段有：

* **partitions: Array\[Partition]**

  * 描述 RDD 拥有多少个分区，每个分区是一个 `Partition` 对象（记录分区 ID、数据块的位置信息）。
  * 分区是最小的调度单位。

* **dependencies: Seq\[Dependency\[\_]]**

  * 记录 RDD 之间的依赖关系（窄依赖 / 宽依赖）。
  * 这是 lineage 的核心，用来在容错时回溯。

* **compute(partition: Partition, context: TaskContext): Iterator\[T]**

  * 每个 RDD 子类都必须实现 `compute` 方法，定义如何计算某个分区的数据。
  * 真正的数据不是提前存好的，而是 **在 action 触发时，才通过 compute 动态计算生成**。

* **storageLevel: StorageLevel**

  * 定义 RDD 的持久化策略（`MEMORY_ONLY`、`MEMORY_AND_DISK`、`DISK_ONLY`、序列化等）。
  * 当用户调用 `persist()` 或 `cache()` 时，Spark 会把计算得到的数据缓存在内存（或磁盘）。

* **preferredLocations(partition: Partition): Seq\[String]**

  * 指示数据分区的最佳执行位置（数据本地性），帮助 Spark 调度时减少网络传输。

常见 RDD 子类（举例，有助理解数据来源）：

* `HadoopRDD` / `NewHadoopRDD`：从 HDFS 或 Hadoop 输入格式读入。
* `MapPartitionsRDD` / `MappedRDD`：表示 map 或 mapPartitions 的结果（窄依赖）。
* `ShuffledRDD`：需要 shuffle 的中间结果（宽依赖）。
* `UnionRDD`、`FilteredRDD` 等。

### 3. 存储时的物理形态

* **默认**：RDD 只是保持计算逻辑与分区描述，数据是在执行时通过 `compute` 生成。
* **cache/persist**：当用户 `cache()` 或 `persist()`，计算出的分区数据会被存储在每个 Executor 的 **BlockManager** 中。BlockManager 管理内存块、磁盘块（和序列化字节数组）并与 Master 协调位置元数据。

当 RDD 被实际计算并缓存时，它的数据会以 **JVM 对象** 或 **序列化字节数组** 的形式存储：

* **MEMORY\_ONLY**

  * 数据直接存成 JVM 对象（如 Scala 的 Array、Java 的对象）。
  * 速度最快，但占用内存大。
* **MEMORY\_AND\_DISK**

  * 内存放不下的部分会溢写到磁盘，下次访问时从磁盘加载。
* **DISK\_ONLY**

  * 全部存到磁盘。
* **MEMORY\_ONLY\_SER / MEMORY\_AND\_DISK\_SER**

  * 数据序列化成字节数组存储，节省空间但 CPU 开销更大。

存储格式与 trade-off ：

* **JVM 对象（MEMORY\_ONLY）**：读取快，但占内存高，GC 成本需注意。
* **序列化存储（MEMORY\_ONLY\_SER）**：节省内存、提高网络传输效率，但序列化/反序列化消耗 CPU。推荐使用高效序列化（如 Kryo）。
* **内存 + 磁盘（MEMORY\_AND\_DISK）**：内存不足则溢出到磁盘。
* **落盘（DISK\_ONLY）**：适用于内存非常紧张或极大数据。

### 4. 小例子

假设有一段代码：

```scala
val rdd1 = sc.textFile("hdfs://data/log.txt")
val rdd2 = rdd1.map(_.split(" ")(0))   // 取出第一列
val rdd3 = rdd2.distinct()
rdd3.persist(StorageLevel.MEMORY_AND_DISK)
```

* `rdd1`：一个 **HadoopRDD**，分区信息来自 HDFS block。
* `rdd2`：一个 **MappedRDD**，保存了对 `rdd1` 的窄依赖，以及 `map` 的函数逻辑。
* `rdd3`：一个 **DistinctRDD**，依赖 `rdd2`（宽依赖）。
* `persist`：意味着当 `rdd3` 第一次计算出来时，会存到内存，不够就写磁盘。

✅ **总结一句话**：

* **逻辑层面**：RDD 是不可变的、分区化的逻辑数据集，核心由分区（partitions）、依赖（dependencies）、计算方法（compute）、存储策略（storage level）组成。
* **物理层面**：RDD 本身不存数据，数据要么来自上游数据源（如 HDFS），要么由 lineage 计算得出；如果调用 `cache/persist`，才会把数据真正存到内存/磁盘。

### 5. 什么叫“不可变”？

**RDD 的不可变性（immutability）** 不是随便加的一个约束，而是 Spark 设计中至关重要的原则。

* 当你对一个 RDD 做操作（比如 `map`、`filter`），你并不会在原有的 RDD 上修改数据。
* 相反，会生成一个新的 RDD（带有 lineage，指向上游 RDD 和转换函数）。
* 原始 RDD 始终保持不变。

👉 类似函数式编程里的“不可变对象”，所有的转换操作都是“生成新对象”。

#### （1）简化容错机制

* 如果 RDD 是可变的，每个节点可能会以不同方式修改它，一旦出错就很难恢复原始状态。
* 由于 RDD 是不可变的，Spark 可以放心地只存 lineage：

  * 出错时，重新从最初数据 + 操作链计算，结果必然一致。
* 这让 **lineage 容错机制** 成为可能。

#### （2）简化并行计算

* 在分布式环境下，多个 task 可能同时访问同一个 RDD。
* 不可变性保证了不会有写冲突（write conflict）。
* 不需要加锁或复杂的同步机制，降低了并发编程的难度。

#### （3）提升调度与优化的可预测性

* 因为 RDD 的内容不会被随意修改，Spark 调度器可以对整个 DAG 进行全局优化。
* 每个 stage 的依赖关系是确定的、纯函数式的，这让任务划分、数据本地性调度、重算等更可控。

#### （4）更安全的缓存（Cache/Persist）

* 如果 RDD 是可变的，缓存的数据可能随时被别的任务修改，导致结果不一致。
* 不可变性保证了：一旦缓存，数据就不会变，后续访问结果始终一致。
* 这就是 Spark `cache()` / `persist()` 可以放心使用的根本原因。

#### （5）函数式编程思想的延续

* Spark 的 API 设计深受 Scala 和函数式编程影响。
* 不可变数据结构（immutable collections）+ 纯函数（map、filter） → 提供声明式编程模型。
* 开发者只描述“做什么”，而不用关心“怎么执行”，更容易表达复杂的数据流逻辑。

### 6. RDD 的局限与 Spark 演进（DataFrame / Dataset）

* **表达能力与优化空间**：RDD 提供自由度高但缺乏全局语义。Spark 后来引入 DataFrame/Dataset + Catalyst 优化器与 Tungsten 执行层，在 SQL/结构化数据上能做更深度的算子融合、物理计划优化、代码生成，从而获得更好性能。
  * DataFrame：一张“分布式表”，类似数据库表，行列结构清晰，每列有数据类型。
  * Dataset：在 DataFrame 的基础上，提供了编译时类型检查和更强的面向对象支持。
  * 这两者让 Spark 可以对“表结构”和“查询计划”做更高级的优化（不像 RDD 只是黑盒函数）。
* **什么时候还用 RDD？**：低层控制（自定义序列化、精细分区策略、复杂非结构化流处理）或学习/研究原理时仍然有价值。但生产 ETL/SQL/ML 工作流推荐首选 DataFrame / Dataset。

Catalyst 是 Spark SQL 的 **查询优化器**，它的作用类似数据库里的优化器。工作分四层：

* **解析**：把 SQL / DataFrame 操作翻译成逻辑计划（Logical Plan）。
* **规则优化**：应用一系列规则（比如常量折叠、谓词下推、投影剪裁、算子融合），得到更优的逻辑计划。
* **物理计划生成**：根据数据大小和集群情况选择具体算子实现，比如选择 broadcast join 还是 shuffle join。
* **代码生成（Codegen）**：将最终的物理计划转化为高效的 JVM 字节码，避免解释执行带来的函数调用开销。

这样，Spark 就能像数据库一样，自动为 SQL/DataFrame 查询生成更高效的执行方式。

Catalyst 负责“计划优化”，而 **Tungsten 专注于“执行效率”**：

* **内存管理优化**：绕过 JVM 的对象存储和 GC，使用二进制格式直接管理内存。
* **缓存效率**：利用 CPU Cache Line，压缩存储，减少内存访问开销。
* **向量化执行**：批量处理数据，减少函数调用开销。
* **代码生成（Whole-Stage Codegen）**：把一系列算子融合成一段连续的 Java 方法，避免频繁的函数切换（类似手工写的 for 循环一样高效）。

## 三、基于Lineage实现容错机制

* RDD 是不可变的，且对每个 RDD 保存生成它的依赖链（lineage）。
* 当某个分区数据丢失（executor 崩溃、磁盘坏块等），Spark 不依赖副本去恢复，而是根据 lineage 重新计算该分区所需的上游分区并重放计算。

### 1. 传统方式的容错问题

在分布式系统里，节点和数据分区经常会丢失。两种常见的容错手段是：

* **复制（Replication）**：比如 HDFS，通常存 3 份副本，任何一份坏掉还能用别的副本。优点是恢复快，缺点是存储开销大（3 倍以上）。
* **检查点（Checkpointing）**：定期把中间状态保存下来，出错时从检查点恢复。优点是安全，缺点是频繁 I/O 成本高。

MapReduce 主要依赖复制来保证容错，每一步计算的输出写入 HDFS，带来了大量的磁盘读写。

### 2. Spark 的改进：Lineage 血缘机制

Spark 提出了一种更轻量的思路：

* **RDD 是不可变的**。当你对一个 RDD 做 `map` 或 `filter` 时，不会改变原数据，而是生成一个新的 RDD。
* **RDD 记录“怎么来的”而不是“结果是什么”**。

  * 每个 RDD 都保存了自己是由哪个上游 RDD，通过什么转换算子生成的（例如 `RDD2 = RDD1.map(f)`）。
  * 这条“转换链”就是 **lineage（血缘）**。
* **容错时重算**：

  * 如果某个分区丢失，Spark 不会去找副本，而是回溯 lineage，重新执行丢失分区所需的那部分计算。
  * 因为 lineage 是逻辑依赖关系图（DAG），系统只需重算必要的分区，而不是整个数据集。

### 3. 原理举例

假设有这样一条计算链：

```txt
RDD1 (HDFS 原始数据)
   ↓ map
RDD2
   ↓ filter
RDD3
   ↓ reduceByKey
RDD4 (最终结果)
```

如果在计算 `RDD4` 时，某个分区丢了：

1. Spark 会查看 `RDD4` 的 lineage，发现它来自 `RDD3.reduceByKey`。
2. 然后回溯到 `RDD3`，再发现它来自 `RDD2.filter`。
3. 继续回溯到 `RDD2.map`，最后到 `RDD1`（原始数据还在 HDFS，有副本保障）。
4. Spark 就只需要重新从 `RDD1` 的相关分区开始，沿着 lineage 重新执行 map → filter → reduceByKey，恢复丢失的分区。

> 注意：只需 **重算丢失的那几个分区**，而不是整个数据集。

### 4. 为什么高效？

* **省存储**：不需要存 3 份副本，只要保存 lineage 信息即可。
* **省 I/O**：不用每步都落盘，只有在需要 checkpoint 或最终结果时才写磁盘。
* **重算局部**：出错时只重算丢失的分区，而不是整个任务。
* **适合大数据场景**：大多数大数据任务是“确定性转换”的（pure function），lineage 保证了重算结果和原来一致。

### 5. 特殊情况：宽依赖和窄依赖

* **窄依赖（Narrow Dependency）**：每个子 RDD 分区依赖于上游 RDD 的少数几个分区（如 `map`、`filter`），恢复时只需重算少量数据。
* **宽依赖（Wide Dependency）**：子 RDD 的每个分区依赖上游 RDD 的多个分区（如 `groupByKey`、`reduceByKey`），恢复时可能要重算较大范围数据。

Lineage 很长或依赖中包含大量宽依赖时，重算代价会变高。Spark 提供两种缓解方式：

* **Checkpoint（检查点）**：把 RDD 写入可靠存储（如 HDFS），并截断 lineage。适合长迭代或多阶段计算的中间结果持久化。
* **缓存（persist）**：把数据放进 BlockManager，避免每次都重复计算（不是容错的替代，但能减少重算频次）。

✅ **总结一句话**：
Spark 通过 **lineage** 记录 RDD 的生成逻辑，避免了昂贵的副本存储；一旦分区丢失，就沿着 lineage 重算必要的数据分区，从而实现了 **轻量、高效的容错机制**。

## 四、Stage与Task

用户通过 transformations 构建一棵有向无环图（DAG），节点是 RDD，边是依赖。Spark 的 DAGScheduler 会把这个逻辑 DAG 划分成若干物理阶段（stages）。

* **Stage**：一段可以在不进行 shuffle 的情况下连续执行的转换链（由窄依赖组成）。遇到 **宽依赖**（需要 shuffle）时，Spark 会把 DAG 在该点切分为多个 Stage。
* 因此：多个连续的 `map`/`filter` 可以形成一个 Stage；带 `reduceByKey`、`groupByKey`、`join` 的节点会触发 shuffle，成为 Stage 边界。

### 1. RDD 转换 vs Stage

* **RDD 转换（transformation）**

  * 比如 `map`、`filter`、`reduceByKey`，每个转换都会生成一个新的 RDD。
  * 转换只是逻辑上的描述（DAG 中的一个节点）。

* **Stage**

  * 是 Spark 执行调度的**物理执行单元**，由一组可以并行计算的任务（tasks）组成。
  * 一个 Stage 对应于 DAG 中一段连续的、**只有窄依赖（narrow dependency）** 的 RDD 转换链。
  * 当遇到 **宽依赖（wide dependency）** 时（例如 `reduceByKey`、`groupByKey`、`join`），Spark 必须执行 **shuffle**，这会触发 Stage 的划分。

👉 换句话说：
**RDD 转换是逻辑层面的数据依赖，Stage 是物理层面的执行划分。**

### 2. Stage 划分规则

Spark 根据 **RDD 依赖关系** 来划分 Stage：

* **窄依赖 (Narrow Dependency)**

  * 子 RDD 的每个分区依赖于父 RDD 的少数几个分区（比如 `map`、`filter`）。
  * 这些操作可以在同一个 Stage 内完成。

* **宽依赖 (Wide Dependency)**

  * 子 RDD 的每个分区依赖父 RDD 的多个分区（比如 `reduceByKey`、`groupByKey`）。
  * 必须经过 **shuffle**，不同节点之间需要交换数据。
  * 发生 shuffle 的地方，就是 Stage 的边界。

### 3. 举个例子

```scala
val rdd1 = sc.textFile("hdfs://data/log.txt")
val rdd2 = rdd1.map(_.split(" ")(0))      // 窄依赖
val rdd3 = rdd2.filter(_.nonEmpty)        // 窄依赖
val rdd4 = rdd3.map((_, 1))
val rdd5 = rdd4.reduceByKey(_ + _)        // 宽依赖（触发 shuffle）
val rdd6 = rdd5.mapValues(_ / 2)          // 窄依赖
rdd6.saveAsTextFile("hdfs://data/out")
```

* **Stage 1**：`rdd1 → rdd2 → rdd3 → rdd4`（全部是窄依赖，可以流水线式执行）
* **Stage 2**：`rdd5`（`reduceByKey` 引发 shuffle，需要新的 Stage）
* **Stage 3**：`rdd6` → 输出（窄依赖）

所以最终 DAG 会被拆成 **3 个 Stage**。

### 4. Stage 和 Task 的关系

* **Stage**：一组逻辑上可以并行执行的任务集合。
* **Task**：在一个具体分区上的计算单元。

  * 一个 Stage 会被分解为多个 Task，每个 Task 处理一个分区的数据。
  * Task 在 Executor 上并行执行。Stage 的并行度等于参与 Stage 的分区数。

举例：

* 如果 Stage 有 100 个分区，就会产生 100 个 Task，分布到不同的 Executor 上执行。

### 5 为什么 Shuffle 代价昂贵？

Shuffle 涉及 **网络传输**（跨节点数据移动）、**磁盘 I/O**（map 端写本地文件，reduce 端读取）、**序列化/反序列化**、以及 **合并/排序**（sort-based shuffle）。这些都显著增加延迟与资源消耗：

* 使用 `reduceByKey`（在 map 端进行 combine）代替 `groupByKey`，减少数据发送量。
* 通过合理的 `partitionBy` 与自定义 Partitioner 控制数据分布（避免数据倾斜）。
* 对小表使用 **广播 Join（broadcast join）**，避免 shuffle 大表。
* 合理使用 `mapPartitions`、避免频繁创建对象来减少序列化开销。


## 五、总结：与 MapReduce 相比的改进

### 1. 数据存储与迭代计算

* **MapReduce**：每一步的中间结果都会写入 HDFS，后续步骤需要重新从磁盘加载。适合单次批处理，但对迭代算法（如 PageRank、K-means）效率低下。
* **Spark**：将中间结果保存在内存中（也可选择落盘），极大提高了迭代计算和交互式计算性能。

### 2. 编程模型

* **MapReduce**：程序员必须围绕“map + reduce”两种操作来设计任务，表达能力有限。
* **Spark**：提供了更丰富的 API，包括 `map`、`filter`、`reduceByKey`、`join` 等算子，并支持 DAG 计算图，编程抽象更自然。

### 3. 容错机制

* **MapReduce**：通过数据复制（replication，通常是 3 份 HDFS 副本）保证容错，但带来存储开销。
* **Spark**：通过 lineage 记录计算逻辑，只需在故障时重算丢失分区，不必复制全部数据，存储和恢复更高效。

### 4. 性能

* **MapReduce**：磁盘 I/O 成本高，延迟大。
* **Spark**：内存计算 + DAG 优化，大幅提升性能。在论文实验中，Spark 在迭代式任务上可比 Hadoop MapReduce 快 10\~100 倍。

### 5. 应用场景

* **MapReduce**：适合大规模批处理，如日志分析、数据清洗。
* **Spark**：既能做批处理，又能做迭代计算、流处理（Spark Streaming）、SQL 查询（Spark SQL）、机器学习（MLlib）、图计算（GraphX），生态更丰富。

### 6. 总结

* Spark 的核心创新是 **RDD 抽象**，用 lineage 提供了高效的容错方式。
* Spark 将 **内存计算 + DAG 执行模型** 引入分布式计算，大幅减少了磁盘 I/O 的开销。
* 相比 MapReduce，Spark 在 **迭代计算、交互式分析、多场景支持** 等方面有了质的提升。
