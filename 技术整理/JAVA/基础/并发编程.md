
## 并发编程

Java 5 添加了一个新的包到 Java 平台，java.util.concurrent 包

**2. 阻塞队列 BlockingQueue**

BlockingQueue 通常用于一个线程生产对象，而另外一个线程消费这些对象的场景

###BlockingQueue 的方法

BlockingQueue 具有 4 组不同的方法用于插入、移除以及对队列中的元素进行检查。如果请求的操作不能得到立即执行的话，每个方法的表现也不同。这些方法如下

方法 | 抛异常 | 特定值 | 阻塞 | 超时
- | - | - | - | -
插入 | add(o) | offer(o) | put(o) | offer(o, timeout, timeunit)
移除 | remove(o) | poll(o) | take(o) | poll(timeout, timeunit)
检查 | element(o) | peek(o)

四组不同的行为方式解释：

抛异常：如果试图的操作无法立即执行，抛一个异常。
特定值：如果试图的操作无法立即执行，返回一个特定的值(常常是 true / false)。
阻塞：如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行。
超时：如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行，但等待时间不会超过给定值。返回一个特定值以告知该操作是否成功(典型的是 true / false)

无法向一个 BlockingQueue 中插入 null。如果你试图插入 null，BlockingQueue 将会抛出一个 NullPointerException

可以访问到 BlockingQueue 中的所有元素，而不仅仅是开始和结束的元素。比如说，你将一个对象放入队列之中以等待处理，但你的应用想要将其取消掉。那么你可以调用诸如 remove(o) 方法来将队列之中的特定对象进行移除。但是这么干效率并不高(译者注：基于队列的数据结构，获取除开始或结束位置的其他对象的效率不会太高)，因此你尽量不要用这一类的方法，除非你确实不得不那么

### BlockingQueue 的实现

BlockingQueue 是个接口，你需要使用它的实现之一来使用 BlockingQueue。java.util.concurrent 具有以下 BlockingQueue 接口的实现(Java 6)

- ArrayBlockingQueue ArrayBlockingQueue 是一个有界的阻塞队列，其内部实现是将对象放到一个数组里。有界也就意味着，它不能够存储无限多数量的元素
- DelayQueue DelayQueue 对元素进行持有直到一个特定的延迟到期。注入其中的元素必须实现 java.util.concurrent.Delayed 接口
- LinkedBlockingQueue LinkedBlockingQueue 内部以一个链式结构(链接节点)对其元素进行存储。如果需要的话，这一链式结构可以选择一个上限。如果没有定义上限，将使用 Integer.MAX_VALUE 作为上限 LinkedBlockingQueue 内部以 FIFO(先进先出)的顺序对元素进行存储
- PriorityBlockingQueue PriorityBlockingQueue 是一个无界的并发队列。它使用了和类 java.util.PriorityQueue 一样的排序规则
- SynchronousQueue 一个特殊的队列，它的内部同时只能够容纳单个元素。如果该队列已有一元素的话，试图向队列中插入一个新元素的线程将会阻塞，直到另一个线程将该元素从队列中抽走。同样，如果该队列为空，试图向队列中抽取一个元素的线程将会阻塞，直到另一个线程向队列中插入了一条新的元素

###阻塞双端队列BlockingDeque

BlockingDeque 类是一个双端队列，在不能够插入元素时，它将阻塞住试图插入元素的线程；在不能够抽取元素时，它将阻塞住试图抽取的线程。deque(双端队列) 是 “Double Ended Queue” 的缩写

####实现类 链阻塞双端队列 LinkedBlockingDeque

### 并发 Map(映射) ConcurrentMap

java.util.concurrent.ConcurrentMap 接口表示了一个能够对别人的访问(插入和提取)进行并发处理的 java.util.Map 实现类：

ConcurrentHashMap

- ConcurrentHashMap 和 java.util.HashTable 类很相似，但 ConcurrentHashMap 能够提供比 HashTable 更好的并发性能。在你从中读取对象的时候 ConcurrentHashMap 并不会把整个 Map 锁住。
- 在你向其中写入对象的时候，ConcurrentHashMap 也不会锁住整个 Map。它的内部只是把 Map 中正在被写入的部分进行锁定
- 另一个不同点是，在被遍历的时候，即使是 ConcurrentHashMap 被改动，它也不会抛 ConcurrentModificationException。尽管 Iterator 的设计不是为多个线程的同时使用

并发导航映射 ConcurrentNavigableMap

java.util.concurrent.ConcurrentNavigableMap 是一个支持并发访问的 java.util.NavigableMap，它还能让它的子 map 具备并发访问的能力。所谓的 “子 map” 指的是诸如 headMap()，subMap()，tailMap() 之类的方法返回的 map。

    ConcurrentNavigableMap map = new ConcurrentSkipListMap();  
    map.put("1", "one");  
    map.put("2", "two");  
    map.put("3", "three");  
    ConcurrentNavigableMap headMap = map.headMap("2");

### 闭锁 CountDownLatch 倒数锁

java.util.concurrent.CountDownLatch 是一个并发构造，它允许一个或多个线程等待一系列指定操作的完成。

CountDownLatch 以一个给定的数量初始化。countDown() 每被调用一次，这一数量就减一。通过调用 await() 方法之一，线程可以阻塞等待这一数量到达零

### 栅栏 CyclicBarrier

java.util.concurrent.CyclicBarrier 类是一种同步机制，它能够对处理一些算法的线程实现同步。换句话讲，它就是一个所有线程必须等待的一个栅栏，直到所有线程都到达这里，然后所有线程才可以继续做其他事情

这两个比较相似

CountDownLatch | CyclicBarrier
-|-
减计数方式 | 加计数方式
计算为0时释放所有等待的线程 | 计数达到指定值时释放所有等待线程
计数为0时，无法重置 | 计数达到指定值时，计数置为0重新开始
调用countDown()方法计数减一，调用await()方法只进行阻塞，对计数没任何影响 | 调用await()方法计数加1，若加1后的值不等于构造方法的值，则线程阻塞
不可重复利用 | 可重复利用

### 交换机 Exchanger

两个线程通过一个 Exchanger 交换对象。
交换对象的动作由 Exchanger 的两个 exchange() 方法的其中一个完成

两个线程持有同一个 Exchanger对象，然后都调用exchange()方法

## 信号量 Semaphore

java.util.concurrent.Semaphore 类是一个计数信号量。这就意味着它具备两个主要方法：

- acquire()
- release()

计数信号量由一个指定数量的 "许可" 初始化。每调用一次 acquire()，一个许可会被调用线程取走。每调用一次 release()，一个许可会被返还给信号量。因此，在没有任何 release() 调用时，最多有 N 个线程能够通过 acquire() 方法，N 是该信号量初始化时的许可的指定数量。这些许可只是一个简单的计数器

#### Semaphore 用法 信号量主要有两种用途：

- 保护一个重要(代码)部分防止一次超过 N 个线程进入。
- 在两个线程之间发送信号

在线程之间发送信号

如果你将一个信号量用于在两个线程之间传送信号，通常你应该用一个线程调用 acquire() 方法，而另一个线程调用 release() 方法。如果没有可用的许可，acquire() 调用将会阻塞，直到一个许可被另一个线程释放出来。同理，如果无法往信号量释放更多许可时，一个 release() 调用也会阻塞

通过这个可以对多个线程进行协调。比如，如果线程 1 将一个对象插入到了一个共享列表(list)之后之后调用了 acquire()，而线程 2 则在从该列表中获取一个对象之前调用了 release()，这时你其实已经创建了一个阻塞队列。信号量中可用的许可的数量也就等同于该阻塞队列能够持有的元素个数

### Semaphore中的公平

没有办法保证线程能够公平地可从信号量中获得许可。也就是说，无法担保掉第一个调用 acquire() 的线程会是第一个获得一个许可的线程。如果第一个线程在等待一个许可时发生阻塞，而第二个线程前来索要一个许可的时候刚好有一个许可被释放出来，那么它就可能会在第一个线程之前获得许可。如果你想要强制公平，Semaphore 类有一个具有一个布尔类型的参数的构造子，通过这个参数以告知 Semaphore 是否要强制公平。强制公平会影响到并发性能，所以除非你确实需要它否则不要启用它

    Semaphore semaphore = new Semaphore(1, true);  

##执行器服务 ExecutorService

java.util.concurrent.ExecutorService 接口表示一个异步执行机制，使我们能够在后台执行任务。因此一个 ExecutorService 很类似于一个线程池。实际上，存在于 java.util.concurrent 包里的 ExecutorService 实现就是一个线程池实现

例子

    ExecutorService executorService = Executors.newFixedThreadPool(10);
    executorService.execute(new Runnable() {  
        public void run() {  
            System.out.println("Asynchronous task");  
        }  
    });  

    executorService.shutdown();

java.util.concurrent 包提供了 ExecutorService 接口的以下实现类：

- ThreadPoolExecutor
- ScheduledThreadPoolExecutor

ExecutorService 的创建依赖于你使用的具体实现。但是你也可以使用 Executors 工厂类来创建 ExecutorService 实例

    ExecutorService executorService1 = Executors.newSingleThreadExecutor();  
    ExecutorService executorService2 = Executors.newFixedThreadPool(10);  
    ExecutorService executorService3 = Executors.newScheduledThreadPool(10);

**ExecutorService 使用**

- execute(Runnable)
- submit(Runnable)
- submit(Callable)
- invokeAny(...)
- invokeAll(...)

**线程池执行者 ThreadPoolExecutor**

ThreadPoolExecutor 包含的线程池能够包含不同数量的线程。池中线程的数量由以下变量决定：

- corePoolSize
- maximumPoolSize

当一个任务委托给线程池时，如果池中线程数量低于 corePoolSize，一个新的线程将被创建，即使池中可能尚有空闲线程。如果内部任务队列已满，而且有至少 corePoolSize 正在运行，但是运行线程的数量低于 maximumPoolSize，一个新的线程将被创建去执行该任务。

但是，除非你确实需要显式为 ThreadPoolExecutor 定义所有参数，使用 java.util.concurrent.Executors 类中的工厂方法之一会更加方便，正如 ExecutorService 小节所述。

**定时执行者服务 ScheduledExecutorService**

java.util.concurrent.ScheduledExecutorService 是一个 ExecutorService， 它能够将任务延后执行，或者间隔固定时间多次执行。 任务由一个工作者线程异步执行，而不是由提交任务给 ScheduledExecutorService 的那个线程执行

java.util.concurrent 包里对它的某个实现类。ScheduledExecutorService 具有以下实现类：ScheduledThreadPoolExecutor

一旦你创建了一个 ScheduledExecutorService，你可以通过调用它的以下方法

- schedule (Callable task, long delay, TimeUnit timeunit)
- schedule (Runnable task, long delay, TimeUnit timeunit)
- scheduleAtFixedRate (Runnable, long initialDelay, long period, TimeUnit timeunit)
- scheduleWithFixedDelay (Runnable, long initialDelay, long period, TimeUnit timeunit)

**使用 ForkJoinPool 进行分叉和合并**

ForkJoinPool 在 Java 7 中被引入。它和 ExecutorService 很相似，除了一点不同。ForkJoinPool 让我们可以很方便地把任务分裂成几个更小的任务，这些分裂出来的任务也将会提交给 ForkJoinPool

ForkJoinPool 是一个特殊的线程池，它的设计是为了更好的配合 分叉-和-合并 任务分割的工作。ForkJoinPool 也在 java.util.concurrent 包中，其完整类名为 java.util.concurrent.ForkJoinPool。

1.创建一个 ForkJoinPool

    ForkJoinPool forkJoinPool = new ForkJoinPool(4);

2.就像提交任务到 ExecutorService 那样，把任务提交到 ForkJoinPool。你可以提交两种类型的任务。一种是没有任何返回值的(一个 "行动")，另一种是有返回值的(一个"任务")。这两种类型分别由 RecursiveAction 和 RecursiveTask 表示。接下来介绍如何使用这两种类型的任务，以及如何对它们进行提交。

**RecursiveAction**

RecursiveAction 是一种没有任何返回值的任务。它只是做一些工作，比如写数据到磁盘，然后就退出了。一个 RecursiveAction 可以把自己的工作分割成更小的几块，这样它们可以由独立的线程或者 CPU 执行。你可以通过继承来实现一个 RecursiveAction。示例如下

    import java.util.ArrayList;  
import java.util.List;  
import java.util.concurrent.RecursiveAction;  

public class MyRecursiveAction extends RecursiveAction {  

    private long workLoad = 0;  

    public MyRecursiveAction(long workLoad) {  
        this.workLoad = workLoad;  
    }   
    @Override  
    protected void compute() {  
        //if work is above threshold, break tasks up into smaller tasks  
        if(this.workLoad > 16) {  
            System.out.println("Splitting workLoad : " + this.workLoad);  
            List<MyRecursiveAction> subtasks =  
                new ArrayList<MyRecursiveAction>();   
            subtasks.addAll(createSubtasks());    
            for(RecursiveAction subtask : subtasks){  
                subtask.fork();  
            }    
        } else {  
            System.out.println("Doing workLoad myself: " + this.workLoad);  
        }  
    }  

    private List<MyRecursiveAction> createSubtasks() {  
        List<MyRecursiveAction> subtasks =  
            new ArrayList<MyRecursiveAction>();  

        MyRecursiveAction subtask1 = new MyRecursiveAction(this.workLoad / 2);  
        MyRecursiveAction subtask2 = new MyRecursiveAction(this.workLoad / 2);   
        subtasks.add(subtask1);  
        subtasks.add(subtask2);    
        return subtasks;  
    }   
    }

**RecursiveTask**

RecursiveTask 是一种会返回结果的任务。它可以将自己的工作分割为若干更小任务，并将这些子任务的执行结果合并到一个集体结果。可以有几个水平的分割和合并。以下是一个 RecursiveTask 示例

    import java.util.ArrayList;  
    import java.util.List;  
    import java.util.concurrent.RecursiveTask;  


    public class MyRecursiveTask extends RecursiveTask<Long> {   
    private long workLoad = 0;  

    public MyRecursiveTask(long workLoad) {  
        this.workLoad = workLoad;  
    }  

    protected Long compute() {  

        //if work is above threshold, break tasks up into smaller tasks  
        if(this.workLoad > 16) {  
            System.out.println("Splitting workLoad : " + this.workLoad);   
            List<MyRecursiveTask> subtasks =  
                new ArrayList<MyRecursiveTask>();  
            subtasks.addAll(createSubtasks());    
            for(MyRecursiveTask subtask : subtasks){  
                subtask.fork();  
            }    
            long result = 0;  
            for(MyRecursiveTask subtask : subtasks) {  
                result += subtask.join();  
            }  
            return result;  

        } else {  
            System.out.println("Doing workLoad myself: " + this.workLoad);  
            return workLoad * 3;  
        }  
    }  

    private List<MyRecursiveTask> createSubtasks() {  
        List<MyRecursiveTask> subtasks =  
        new ArrayList<MyRecursiveTask>();   
        MyRecursiveTask subtask1 = new MyRecursiveTask(this.workLoad / 2);  
        MyRecursiveTask subtask2 = new MyRecursiveTask(this.workLoad / 2);   
        subtasks.add(subtask1);  
        subtasks.add(subtask2);  
        return subtasks;  
    }  
    }

除了有一个结果返回之外，这个示例和 RecursiveAction 的例子很像。MyRecursiveTask 类继承自 RecursiveTask<Long>，这也就意味着它将返回一个 Long 类型的结果。

MyRecursiveTask 示例也会将工作分割为子任务，并通过 fork() 方法对这些子任务计划执行。

此外，本示例还通过调用每个子任务的 join() 方法收集它们返回的结果。子任务的结果随后被合并到一个更大的结果，并最终将其返回。对于不同级别的递归，这种子任务的结果合并可能会发生递归

你可以这样规划一个 RecursiveTask：

    MyRecursiveTask myRecursiveTask = new MyRecursiveTask(128);  
    long mergedResult = forkJoinPool.invoke(myRecursiveTask);  
    System.out.println("mergedResult = " + mergedResult);

### 锁 Lock

java.util.concurrent.locks.Lock 是一个类似于 synchronized 块的线程同步机制。但是 Lock 比 synchronized 块更加灵活、精细

首先创建了一个 Lock 对象。之后调用了它的 lock() 方法。这时候这个 lock 实例就被锁住啦。任何其他再过来调用 lock() 方法的线程将会被阻塞住，直到锁定 lock 实例的线程调用了 unlock() 方法。最后 unlock() 被调用了，lock 对象解锁了，其他线程可以对它进行锁定了。

Java Lock 实现

java.util.concurrent.locks 包提供了以下对 Lock 接口的实现：

- ReentrantLock

一个 Lock 对象和一个 synchronized 代码块之间的主要不同点是：

- synchronized 代码块不能够保证进入访问等待的线程的先后顺序
- 你不能够传递任何参数给一个 synchronized 代码块的入口。因此，对于 synchronized 代码块的访问等待设置超时时间是不可能的事情
- synchronized 块必须被完整地包含在单个方法里。而一个 Lock 对象可以把它的 lock() 和 unlock() 方法的调用放在不同的方法里

Lock 接口具有以下主要方法：

- lock()
- lockInterruptibly()
- tryLock()
- ryLock(long timeout, TimeUnit timeUnit)
- unlock()

**读写锁 ReadWriteLock**

实现类 ReentrantReadWriteLock

**原子性布尔 AtomicBoolean**

- get()
- set()
- getAndSet()
- compareAndSet() 允许你对 AtomicBoolean 的当前值与一个期望值进行比较，如果当前值等于期望值的话，将会对 AtomicBoolean 设定一个新值。compareAndSet() 方法是原子性的，因此在同一时间之内有单个线程执行它

**原子性整型 AtomicInteger**

额外提供了下面几个方法：

- addAndGet()
- getAndAdd()
- decrementAndGet()
- getAndDecrement()

**原子性引用型 AtomicReference**

创建泛型 AtomicReference

    AtomicReference<String> atomicStringReference =  new AtomicReference<String>();  
