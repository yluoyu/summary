## Java 内存模型

## Jvm命令行参数
- **标准选项 以 - 为前缀  -help 可以看到所有的标准选项**
- **非标准选项 以 -X 为前准 不强制要求所有jvm都实现 -X 参数可以看到 所有的非标准选项**
- **非稳定选项 以 -XX 为前缀 主要给开发者调试使用 在后续版本可能会废除，带有布尔标记的非稳定选项，选项前的+或-表示true和false，用于开启对应的特性或者使用默认值**

## GC collector

### 1.新生代 young generation
#### 1.1 Serial

       复制算法
       最简单的collector，只有一个thread负责GC 并且，在执行GC的时候，会暂停整个程序（所谓的“stop-the-world”)
  对应日志
```
    0.844: [GC 0.844: [DefNew: 17472K->2176K(19648K), 0.0188339 secs] 17472K->2375K(63360K), 0.0189186 secs] [Times: user=0.01 sys=0.00, real=0.02 secs]
```

#### 1.2 Parallel Scavenge

    复制算法
    使用multi-thread来处理GC，当然，在执行的时候，仍然会“stop-the-world”，好处在于，暂停的时间更短
    对应日志:
    2017-09-18T15:51:48.174-0800: 15567.067: [GC (Allocation Failure) [PSYoungGen: 270528K->672K(259584K)] 343867K->74043K(370688K), 0.0614831 secs] [Times: user=


#### 1.3 ParNew

    基本上和Parallel Scavenge非常相似，唯一的区别，在于它做了强化能够和CMS一起使用
    对应日志：
    0.834: [GC 0.834: [ParNew: 13184K->1600K(14784K), 0.0092203 secs] 13184K->1921K(63936K), 0.0093401 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]


### 2.老生代 tenured generation

#### 2.1 Serial Old

    单线程 采用 mark-sweep-compact （标记-压缩）回收方法


#### 2.2 Parallel Old

    多线程 采用 mark-sweep-compact （标记-压缩）回收方法

#### 2.3 CMS

    全称“concurrent-mark-sweep”（并发标记清除） ，它是最并发，暂停时间最低的collector，之所以称为concurrent，是因为它在执行GC任务的时候，GC thread是和application thread一起工作的，基本上不需要暂停application thread 新生代一般配合 ParNew
    特点：尽可能降低停顿 会影响系统整体吞吐量和性能 清理不彻底

    主要过程
    1.初始标记（会产生全局停顿） 标记根可以直接关联到的对象
    2.并发标记（和用户线程一起） 主要标记过程，标记全部对象
    3.重新标记 （会产生全局停顿）由于并发标记时，用户线程依然运行，因此在正式清理前，再做修正
    4.并发清除（和用户线程一起） 基于标记结果，直接清理对象
    对应日志
    1.666: [CMS-concurrent-mark-start]
    1.699: [CMS-concurrent-mark: 0.033/0.033 secs] [Times: user=0.25 sys=0.00, real=0.03 secs]
    1.699: [CMS-concurrent-preclean-start]
    1.700: [CMS-concurrent-preclean: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
    1.700: [GC[YG occupancy: 1837 K (14784 K)]1.700: [Rescan (parallel) , 0.0009330 secs]1.701: [weak refs processing, 0.0000180 secs] [1 CMS-remark: 28122K(49152K)] 29959K(63936K), 0.0010248 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
    1.702: [CMS-concurrent-sweep-start]
    1.739: [CMS-concurrent-sweep: 0.035/0.037 secs] [Times: user=0.11 sys=0.02, real=0.05 secs]
    1.739: [CMS-concurrent-reset-start]

    这里就能很明显的看出，为什么CMS要使用标记清除而不是标记压缩，如果使用标记压缩，需要多对象的内存位置进行改变，这样程序就很难继续执行。但是标记清除会产生大量内存碎片，不利于内存分配



### 3 配套使用

在设定JVM参数的时候，很少有人去分别制定young generation和tenured generation的collector，而是提供了几套选择方案：

**-XX:+UseSerialGC**

    相当于”Serial” + “SerialOld”，这个方案直观上就应该是性能最差的

**-XX:+UseParallelGC**

    相当于” Parallel Scavenge” + “SerialOld”，也就是说，在young generation中是多线程处理，但是在tenured generation中是单线程

**UseParallelOldGC**

    相当于” Parallel Scavenge” + “ParallelOld”，都是多线程并行处理

**-XX:+UseConcMarkSweepGC**

    相当于"ParNew" + "CMS" + "Serial Old"，即在young generation中采用ParNew，多线程处理；在tenured generation中使用CMS，
    以求得到最低的暂停时间，但是，采用CMS有可能出现”Concurrent Mode Failure”（这个后面再说），
    如果出现了，就只能采用”SerialOld”模式了


### 4 G1和安全点

**G1收集器**

> G1是目前技术发展的最前沿成果之一，HotSpot开发团队赋予它的使命是未来可以替换掉JDK1.5中发布的CMS收集器，使用G1收集器时，Java堆的内存布局与其他收集器有很大差别，它将整个Java堆划分为多个大小相等的独立区域（Region），虽然还保留有新生代和老年代的概念，但新生代和老年代不再是物理隔阂了，它们都是一部分（可以不连续）Region的集合

**安全点**

> 安全点的选定基本上是以程序“是否具有让程序长时间执行的特征”为标准进行选定的——因为每条指令执行的时间都非常短暂，程序不太可能因为指令流长度太长这个原因而过长时间运行，“长时间执行”的最明显特征就是指令序列复用，例如方法调用、循环跳转、异常跳转等，所以具有这些功能的指令才会产生安全点

    接下来的问题就在于，如何让程序在需要GC时都跑到安全点上停顿下来，大多数JVM的实现都是采用主动式中断的思想。

> 主动式中断的思想是当GC需要中断线程的时候，不直接对线程操作，仅仅简单地设置一个标志，各个线程执行时主动去轮询这个标志，发现中断标志为真时就自己中断挂起，轮询标志的地方和安全点是重合的，另外再加上创建对象需要分配内存的地方


### 执行模式

    Just In Time，简称JIT，可以理解为动态编译。运行时才可以确定具体要编译的代码
    还有一种，叫Ahead Of Time，简称AOT，可以理解为静态编译，即编译器在编译阶段就确定所有的运行代码

    Android在5.0以后彻底抛弃Dalvik转投ART，最大的一点变化就是把JIT换成了AOT。原来的APP在每次打开时虚拟机才会编译，每次打开都编译一次，所以响应速度慢就是这个原因。而ART则是在安装时，编译器就已经将所有的二进制代码编译完成，并储存在本地。这样每次打开直接读取本地二进制文件就可以了，所以存储空间占的更大，用空间换时间

## 四种引用 --  强引用 软引用 弱引用 虚引用

**强引用**

>只要某个对象有强引用与之关联，JVM必定不会回收这个对象，即使在内存不足的情况下，JVM宁愿抛出OutOfMemory错误也不会回收这种对象

**软引用（SoftReference)**

>软引用是用来描述一些有用但并不是必需的对象，在Java中用java.lang.ref.SoftReference类来表示。对于软引用关联着的对象，只有在内存不足的时候JVM才会回收该对象。因此，这一点可以很好地用来解决OOM的问题，并且这个特性很适合用来实现缓存
软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被JVM回收，这个软引用就会被加入到与之关联的引用队列中

**弱引用（WeakReference)**

>弱引用也是用来描述非必需对象的，当JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象。在java中，用java.lang.ref.WeakReference类来表示

__ThreadLocal__的实现中使用WeakReference

**虚引用（PhantomReference）**

>虚引用和前面的软引用、弱引用不同，它并不影响对象的生命周期。在java中用java.lang.ref.PhantomReference类表示。如果一个对象与虚引用关联，则跟没有引用与之关联一样，在任何时候都可能被垃圾回收器回收。

__虚引用主要用来跟踪对象被垃圾回收器回收的活动__
>虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列 （ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中
