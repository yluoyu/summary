-  JVM的内存结构
- JVM方法栈的工作过程，方法栈和本地方法栈有什么区别
  - 任何本地方法接口都会使用某种本地方法栈。当线程调用Java方法时，虚拟机会创建一个新的栈帧并压入Java栈。然而当它调用的是本地方法时，虚拟机会保持Java栈不变，不再在线程的Java栈中压入新的帧，虚拟机只是简单地动态连接并直接调用指定的本地方法
- JVM的栈中引用如何和堆中的对象产生关联
- 可以了解一下逃逸分析技术
- GC的常见算法，CMS以及G1的垃圾回收过程，CMS的各个阶段哪两个是Stop the world的，CMS会不会产生碎片，G1的优势
- 标记清除和标记整理算法的理解以及优缺点
- eden survivor区的比例，为什么是这个比例，eden survivor的工作过程
- JVM如何判断一个对象是否该被GC，可以视为root的都有哪几种类型
- 强软弱虚引用的区别以及GC对他们执行怎样的操作
- Java类加载的过程
- 双亲委派模型的过程以及优势
- 常用的JVM调优参数
- dump文件的分析
