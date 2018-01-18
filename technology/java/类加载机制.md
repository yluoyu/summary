## 1.class装载验证流程

### 1.1 加载
- 装载类的第一个阶段
- 通过类的全限定名取得类的二进制流
- 转为方法区数据结构
- 在Java堆中生成对应的java.lang.Class对象

### 1.2 链接 -> 验证
目的：保证Class流的格式是正确的
文件格式的验证
- 是否以0xCAFEBABE开头
- 版本号是否合理

元数据验证

- 是否有父类
- 继承了final类
- 非抽象类实现了所有的抽象方法

字节码验证

- 运行检查
- 栈数据类型和操作码数据参数吻合
- 跳转指令指定到合理的位置

符号引用验证

- 常量池中描述类是否存在
- 访问的方法或字段是否存在且有足够的权限

### 1.3 链接 -> 准备

分配内存，并为类设置初始值

- public static int v=1;
- **在准备阶段中，v会被设置为0**
- 在初始化的<clinit>中才会被设置为1
- 对于static final类型（常量），在准备阶段就会被赋上正确的值
- public static final  int v=1;

### 1.4 链接 -> 解析

>解析是唯一一个不确定顺序的过程，它在某些情况下可以在初始化阶段之后开始，这是为了支持 Java 语言的运行时绑定（也成为动态绑定或晚期绑定）。另外注意这里的几个阶段是按顺序开始，而不是按顺序进行或完成，因为这些阶段通常都是互相交叉地混合进行的，通常在一个阶段执行的过程中调用或激活另一个阶段

这里简要说明下 Java 中的绑定：绑定指的是把一个方法的调用与方法所在的类(方法主体)关联起来，对 Java 来说，绑定分为静态绑定和动态绑定

- 静态绑定:即前期绑定。在程序执行前方法已经被绑定，此时由编译器或其它连接程序实现。针对 Java，简单的可以理解为程序编译期的绑定。Java 当中的方法只有 final，static，private 和构造方法是前期绑定的
- 动态绑定:即晚期绑定，也叫运行时绑定。在运行时根据具体对象的类型进行绑定。在 Java 中，几乎所有的方法都是后期绑定的

符号引用替换为直接引用
符号引用只是一种表示的方式，比如某个类继承java.lang.object，在符号引用阶段，只会记录该类是继承"java.lang.object"，以这种字符串的形式保存，但是不能保证该对象被记载
直接引用就是真正能使用的引用，它是指针或者地址偏移量，引用对象一定在内存。最终知道在内存中到底放在哪里

1. 类或接口的解析：判断所要转化成的直接引用是对数组类型，还是普通的对象类型的引用，从而进行不同的解析。
2. 字段解析：对字段进行解析时，会先在本类中查找是否包含有简单名称和字段描述符都与目标相匹配的字段，如果有，则查找结束；如果没有，则会按照继承关系从上往下递归搜索该类所实现的各个接口和它们的父接口，还没有，则按照继承关系从上往下递归搜索其父类，直至查找结束，查找流程如下图所

从下面一段代码的执行结果中很容易看出来字段解析的搜索顺序:

```

class Super{
    public static int m = 11;
    static{
        System.out.println("执行了super类静态语句块");
    }
}

class Father extends Super{
    public static int m = 33;
    static{
        System.out.println("执行了父类静态语句块");
    }
}

class Child extends Father{
    static{
        System.out.println("执行了子类静态语句块");
    }
}

public class StaticTest{
    public static void main(String[] args){
        System.out.println(Child.m);
    }
}
```

执行结果：
```
执行了super类静态语句块
执行了父类静态语句块
33
```

static变量发生在静态解析阶段，也即是初始化之前，此时已经将字段的符号引用转化为了内存引用，也便将它与对应的类关联在了一起，由于在子类中没有查找到与 m 相匹配的字段，那么 m 便不会与子类关联在一起，因此并不会触发子类的初始化。

至于为什么先执行super的类静态语句块，后执行父类静态语句块是因为在初始化阶段子类的<clinit>调用前保证父类的<clinit>被调用

最后需要注意：理论上是按照上述顺序进行搜索解析，但在实际应用中，虚拟机的编译器实现可能要比上述规范要求的更严格一些。如果有一个同名字段同时出现在该类的接口和父类中，或同时在自己或父类的接口中出现，编译器可能会拒绝编译

3.类方法解析：对类方法的解析与对字段解析的搜索步骤差不多，只是多了判断该方法所处的是类还是接口的步骤，而且对类方法的匹配搜索，是先搜索父类，再搜索接口

4.接口方法解析：与类方法解析步骤类似，只是接口不会有父类，因此，只递归向上搜索父接口就行了


###1.5 初始化

执行类构造器<clinit\>

- static变量 赋值语句
- static{}语句

子类的<clinit\>调用前保证父类的<clinit\>被调用

因此，在虚拟机中第一个被执行的()方法的类肯定是java.lang.Object的


##2.类装载器ClassLoader

- ClassLoader是一个抽象类
- ClassLoader的实例将读入Java字节码将类装载到JVM中
- ClassLoader可以定制，满足不同的字节码流获取方式（譬如从网络中加载，从文件中加载）
- ClassLoader负责类装载过程中的加载阶段

### 系统中的ClassLoader

- BootStrap ClassLoader （启动ClassLoader）启动类加载器
  <Java_Runtime_Home>/lib下面的类库加载到内存中（比如rt.jar）
- Extension ClassLoader （扩展ClassLoader）标准扩展类加载器
  它负责将< Java_Runtime_Home >/lib/ext或者由系统变量 java.ext.dir指定位置中的类库加载到内存中。开发者可以直接使用标准扩展类加载器。
- App ClassLoader （应用ClassLoader/系统ClassLoader）
  是由 Sun 的 AppClassLoader（sun.misc.Launcher$AppClassLoader）实现的。它负责将系统类路径（CLASSPATH）中指定的类库加载到内存中
- Custom ClassLoader(自定义ClassLoader)

每个ClassLoader都有一个Parent作为父亲( BootStrap除外)

##3. JDK中ClassLoader默认设计模式

![类借助机制](http://static.oschina.net/uploads/space/2016/0316/212258_QZKD_2243330.gif)

>自底向上检查类是否被加载，一般情况下，首先先从AppClassLoader中调用findLoadedClass查看是否已经加载，如果没有加载，则会交给父类，ExtensionClassLoader去查看是否加载，还没加载，则再调用其父类，BootstrapClassLoader查看是否已经加载，如果仍然没有，自顶向下尝试加载类，那么从 BootstrapClassLoader到 AppClassLoader依次尝试加载。

即使两个类来源于同一个 Class 文件，只要加载它们的类加载器不同，那这两个类就必定不相等。这里的“相等”包括了代表类的 Class 对象的 equals（）、isAssignableFrom（）、isInstance（）等方法的返回结果，也包括了使用 instanceof 关键字对对象所属关系的判定结果

![loadClass逻辑](http://static.oschina.net/uploads/space/2016/0316/213417_1fRE_2243330.gif)

从代码上可以很容易看出来，首先先自己查看这个类是否被调用，如果没有找到，则调用父亲的loadClass，直到BootstrapClassLoader（没有父亲）。

我们把这个加载的过程叫做双亲模式。

双亲委托机制的作用是防止系统jar包被本地替换

### 3.1 双亲模式的问题

顶层ClassLoader，无法加载底层ClassLoader的类
