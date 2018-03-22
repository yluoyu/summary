
### 频繁GC问题或内存溢出问题
一、使用jps查看线程ID
二、使用`jstat -gc 3331 250 20` 查看gc情况，一般比较关注PERM区的情况，查看GC的增长情况
三、使用`jstat -gccause`：额外输出上次GC原因
四、使用`jmap -dump:format=b,file=heapDump 3331`生成堆转储文件
五、使用`jhat`或者可视化工具（Eclipse Memory Analyzer 、IBM HeapAnalyzer）分析堆情况
六、结合代码解决内存溢出或泄露问题

### 死锁问题
一、使用`jps`查看线程ID
二、使用`jstack 3331`：查看线程情况


**java**
Java运行工具，用于运行.class字节码文件或.jar文件

**javap**
Java反编译工具，主要用于根据Java字节码文件反汇编为Java源代码文件

**jconsole**
图形化用户界面的监测工具，主要用于监测并显示运行于Java平台上的应用程序的性能和资源占用等信息

**jhat**
Java堆分析工具(Java Heap Analysis Tool)，用于分析Java堆内存中的对象信息
命令格式：`jhat [ options ] [ pid ]`
-flag  输出，修改，JVM命令行参数

### **jinfo**
Java配置信息工具(`Java Configuration Information`)，用于打印指定Java进程、核心文件或远程调试服务器的配置信息。
可以`输出`并`修改`运行时的java 进程的opts。用处比较简单，用于输出JAVA系统参数及命令行参数


### **`jmap`**
jmap: Java内存映射工具(Java Memory Map)，主要用于打印指定Java进程、核心文件或远程调试服务器的共享对象内存映射或堆内存细节。
打印出某个java进程（使用pid）内存内的所有`对象`的情况（如：产生那些对象，及其数量）
当服务发生GC问题时，一般会使用jmap工具进行分析，jmap工具很强大，所以有必要了解它的方方面面。
- `jmap -histo[:live] <pid>`
  通过histo选项，打印当前java堆中各个对象的数量、大小
- `jmap -dump:[live,]format=b,file=<filename> <pid>`
  通过-dump选项，把java堆中的对象dump到本地文件，然后使用MAT进行分析
- `jmap -heap <pid>`
  通过-heap选项，打印java堆的配置情况和使用情况，还有使用的GC算法
`例`：以二进制形式输入当前堆内存映像到文件data.hprof中
`jmap -dump:live,format=b,file=data.hprof 1796 `

#### 实例个数以及占用内存大小

```
C:\Users\Administrator>jmap -histo 4284  > d:/log.txt
```

```
num     #instances         #bytes  class name
----------------------------------------------
  1:       1496092      127664200  [C
  2:        157665       46778984  [I
  3:        100289       25426744  [B
  4:        736941       17686584  java.util.HashMap$Node
  5:         74396       11077256  [Ljava.util.HashMap$Node;
  6:        192701       10228688  [J
  7:        564943        9039088  java.lang.String
```
```
num：序号
instances：实例数量
bytes：占用空间大小
class name：类名称
```
#### 堆信息

```
C:\Users\Administrator>jmap -heap 4284
Attaching to process ID 4284, please wait...
Debugger attached successfully.
Client compiler detected.
JVM version is 25.0-b70

using parallel threads in the new generation.
using thread-local object allocation.
Concurrent Mark-Sweep GC

Heap Configuration:
   MinHeapFreeRatio         = 40
   MaxHeapFreeRatio         = 70
   MaxHeapSize              = 1073741824 (1024.0
   NewSize                  = 419430400 (400.0MB
   MaxNewSize               = 419430400 (400.0MB
   OldSize                  = 654311424 (624.0MB
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 104857600 (100.0MB
   CompressedClassSpaceSize = 52428800 (50.0MB)
   MaxMetaspaceSize         = 104857600 (100.0MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
New Generation (Eden + 1 Survivor Space):
   capacity = 377487360 (360.0MB)
   used     = 346767024 (330.7028045654297MB)
   free     = 30720336 (29.297195434570312MB)
   91.8618901570638% used
Eden Space:
   capacity = 335544320 (320.0MB)
   used     = 317925456 (303.1973419189453MB)
   free     = 17618864 (16.802658081054688MB)
   94.74916934967041% used
From Space:
   capacity = 41943040 (40.0MB)
   used     = 28841568 (27.505462646484375MB)
   free     = 13101472 (12.494537353515625MB)
   68.76365661621094% used
To Space:
   capacity = 41943040 (40.0MB)
   used     = 0 (0.0MB)
   free     = 41943040 (40.0MB)
   0.0% used
concurrent mark-sweep generation:
   capacity = 654311424 (624.0MB)
   used     = 190777600 (181.939697265625MB)
   free     = 463533824 (442.060302734375MB)
   29.157002766927082% used

32953 interned Strings occupying 2775672 bytes.
```
#### 即将垃圾回收对象个数
```
C:\Users\Administrator>jmap -finalizerinfo 4284
Attaching to process ID 4284, please wait...
Debugger attached successfully.
Client compiler detected.
JVM version is 25.0-b70
Number of objects pending for finalization: 0
```

#### 堆内存dump
```
C:\Users\Administrator>jmap -dump:format=b,file=D:/chu 4284
Dumping heap to D:\chu ...
Heap dump file created
```
我们可以用jhat命令来查看此dump文件的内容：
```
C:\Users\Administrator>jhat -J-Xmx500m D:/chu
Reading from D:/haha...
Dump file created Thu May 28 09:46:36 CST 2015
Snapshot read, resolving...
Resolving 3915212 objects...
Chasing references, expect 783 dots.......................
..........................................................
..........................................................
..........................................................
..................
Eliminating duplicate references..........................
..........................................................
..........................................................
..........................................................
...............
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
```
然后在浏览器中输入：`http://localhost:7000`来查看堆中的对象信息

### **jps**
JVM进程状态工具(JVM Process Status Tool)，用于显示目标系统上的HotSpot JVM的Java进程信息
用来查看基于HotSpot JVM里面所有进程的具体状态, 包括进程ID，进程启动的路径等等。与unix上的ps类似，用来显示本地有权限的java进程，可以查看本地运行着几个java程序，并显示他们的进程号。使用jps时，不需要传递进程号做为参数
命令格式：`jps [ options ] [ hostid ]`
- `-m` 输出传递给main方法的参数，如果是内嵌的JVM则输出为null。
- `-l` 输出应用程序主类的完整包名，或者是应用程序JAR文件的完整路径。
- `-v` 输出传给JVM的参数



### **`jstack`**
Java堆栈跟踪工具，主要用于打印指定Java进程、核心文件或远程调试服务器的Java线程的堆栈跟踪信息
- `-l long listings`，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况
- `-m mixed mode`，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）

`jstack pid`
```
Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x00007f979c0356b8 (object 0x000000079596a5e0, a java.lang.Object),
  which is held by "Thread-0"
"Thread-0":
  waiting to lock monitor 0x00007f979c037e98 (object 0x000000079596a5f0, a java.lang.Object),
  which is held by "Thread-1"

Java stack information for the threads listed above:
```

### **`jstat`**
JVM统计监测工具(JVM Statistics Monitoring Tool)，主要用于监测并显示JVM的性能统计信息，包括gc统计信息。
对Java应用程序的资源和性能进行`实时的`命令行的监控，包括了对Heap size和垃圾回收状况的监控
命令格式：`jstat [ option  pid [interval [ s | ms ] [count] ] ]`
常用参数说明：
- 新生代垃圾回收统计  `jstat -gcnew 7172`
- 新生代内存统计     `jstat -gcnewcapacity 7172`
- 老年代垃圾回收统计  `jstat -gcold 7172`
- 老年代内存统计     `jstat -gcoldcapacity 7172`
- 元数据空间统计     `jstat -gcmetacapacity 7172`
- 总结垃圾回收统计   `jstat -gcutil 7172`

#### 类加载统计
```
C:\Users\Administrator>jstat -class 2060
Loaded  Bytes  Unloaded  Bytes     Time
15756 17355.6        0     0.0      11.29
```
```
Loaded:加载class的数量
Bytes：所占用空间大小
Unloaded：未加载数量
Bytes:未加载占用空间
Time：时间
```

#### 垃圾回收统计
```
C:\Users\Administrator>jstat -gc 2060
 S0C    S1C    S0U    S1U      EC       EU        OC         OU          MC     MU    CCSC      CCSU   YGC     YGCT    FGC    FGCT     GCT
20480.0 20480.0  0.0   13115.3 163840.0 113334.2  614400.0   436045.7  63872.0 61266.5  0.0    0.0      149    3.440   8      0.295    3.735
```
```
S0C：第一个幸存区的大小
S1C：第二个幸存区的大小
S0U：第一个幸存区的使用大小
S1U：第二个幸存区的使用大小
EC：伊甸园区的大小
EU：伊甸园区的使用大小

OC：老年代大小
OU：老年代使用大小
MC：方法区大小(待考证 应该是元空间大小)
MU：方法区使用大小
CCSC:压缩类空间大小
CCSU:压缩类空间使用大小

YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间
```



#### 堆内存统计
```
C:\Users\Administrator>jstat -gccapacity 2060
 NGCMN    NGCMX     NGC     S0C     S1C       EC      OGCMN      OGCMX       OGC         OC          MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC
204800.0 204800.0 204800.0 20480.0 20480.0 163840.0   614400.0   614400.0   614400.0   614400.0      0.0    63872.0  63872.0      0.0      0.0      0.0    149     8
```
```
NGCMN：新生代最小容量
NGCMX：新生代最大容量
NGC：当前新生代容量
S0C：第一个幸存区大小
S1C：第二个幸存区的大小
EC：伊甸园区的大小

OGCMN：老年代最小容量
OGCMX：老年代最大容量
OGC：当前老年代大小
OC:当前老年代大小

MCMN:最小元数据容量
MCMX：最大元数据容量
MC：当前元数据空间大小
CCSMN：最小压缩类空间大小
CCSMX：最大压缩类空间大小
CCSC：当前压缩类空间大小

YGC：年轻代gc次数
FGC：老年代GC次数
```


**jcmd**
