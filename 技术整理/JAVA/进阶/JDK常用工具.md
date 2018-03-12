**java**
Java运行工具，用于运行.class字节码文件或.jar文件

**javap**
Java反编译工具，主要用于根据Java字节码文件反汇编为Java源代码文件

**jconsole**
图形化用户界面的监测工具，主要用于监测并显示运行于Java平台上的应用程序的性能和资源占用等信息

**jhat**
Java堆分析工具(Java Heap Analysis Tool)，用于分析Java堆内存中的对象信息

**jinfo**
Java配置信息工具(Java Configuration Information)，用于打印指定Java进程、核心文件或远程调试服务器的配置信息

**jmap**
jmap: Java内存映射工具(Java Memory Map)，主要用于打印指定Java进程、核心文件或远程调试服务器的共享对象内存映射或堆内存细节
当服务发生GC问题时，一般会使用jmap工具进行分析，jmap工具很强大，所以有必要了解它的方方面面。
- `jmap -histo[:live] <pid>`
  通过histo选项，打印当前java堆中各个对象的数量、大小
- `jmap -dump:[live,]format=b,file=<filename> <pid>`
  通过-dump选项，把java堆中的对象dump到本地文件，然后使用MAT进行分析
- `jmap -heap <pid>`
  通过-heap选项，打印java堆的配置情况和使用情况，还有使用的GC算法

**jps**
JVM进程状态工具(JVM Process Status Tool)，用于显示目标系统上的HotSpot JVM的Java进程信息

**jstack**
Java堆栈跟踪工具，主要用于打印指定Java进程、核心文件或远程调试服务器的Java线程的堆栈跟踪信息

**jstat**
JVM统计监测工具(JVM Statistics Monitoring Tool)，主要用于监测并显示JVM的性能统计信息，包括gc统计信息

**jcmd**
