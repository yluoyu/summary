
#### 单例
默认`@Scope("singleton")` 即单例模式

若想使用成员变量，有两种方式
- 使用@Scope("prototype")
- ThreadLocal 变量

Java里有个API叫做ThreadLocal，spring单例模式下用它来切换不同线程之间的参数。用ThreadLocal是为了保证线程安全，实际上ThreadLoacal的key就是当前线程的Thread实例。单例模式下，spring把每个线程可能存在线程安全问题的参数值放进了ThreadLocal。这样虽然是一个实例在操作，但是不同线程下的数据互相之间都是隔离的，因为运行时创建和销毁的bean大大减少了，所以大多数场景下这种方式对内存资源的消耗较少，而且并发越高优势越明显

#### 与struts2的区别
很久没用过struts2了...

- `控制器实例` Spring Mvc会比Struts快一些（理论上）。Spring Mvc是基于方法设计，而Sturts是基于对象，每次发一次请求都会实例一个action，每个action都会被注入   属性，而Spring更像Servlet一样，只有一个实例，每次请求执行对应的方法即可(注意：由于是单例实例，所以应当避免全局变量的修改，这样会产生线程安全问题)
