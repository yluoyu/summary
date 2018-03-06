- 数据库自增Id
- 使用reids的incr命令
  Redis INCR命令用于将键的整数值递增1。如果键不存在，则在执行操作之前将其设置为0。 如果键包含错误类型的值或包含无法表示为整数的字符串，则会返回错误。此操作限于64位有符号整数。
  `redis 127.0.0.1:6379> INCR KEY_NAME`
- 使用UUID


#### UUID
UUID是Universally Unique Identifier的缩写，它是在一定的范围内（从特定的名字空间到全球）唯一的机器生成的标识符。UUID具有以下涵义：
- 经由一定的算法机器生成
- 非人工指定，非人工识别
- 在特定的范围内重复的可能性极小
GUID（Globally Unique Identifier）是UUID的别名；但在实际应用中，GUID通常是指微软实现的UUID

UUID具有多个版本，每个版本的算法不同，应用范围也不同
**一、UUID Version 1：基于时间的UUID**
以保证在全球范围的唯一性。但与此同时，使用MAC地址会带来安全性问题，这就是这个版本UUID受到批评的地方。如果应用只是在局域网中使用，也可以使用退化的算法，以IP地址来代替MAC地址－－Java的UUID往往是这样实现的（当然也考虑了获取MAC的难度）
二、UUID Version 2：DCE安全的UUID
DCE（Distributed Computing Environment）安全的UUID和基于时间的UUID算法相同，但会把时间戳的前4位置换为POSIX的UID或GID。这个版本的UUID在实际中较少用到
三、UUID Version 3：基于名字的UUID（MD5）
基于名字的UUID通过计算名字和名字空间的MD5散列值得到。这个版本的UUID保证了：相同名字空间中不同名字生成的UUID的唯一性；不同名字空间中的UUID的唯一性；相同名字空间中相同名字的UUID重复生成是相同的
四、UUID Version 4：随机UUID
根据随机数，或者伪随机数生成UUID。这种UUID产生重复的概率是可以计算出来的，但随机的东西就像是买彩票：你指望它发财是不可能的，但狗屎运通常会在不经意中到来
五、UUID Version 5：基于名字的UUID（SHA1）
和版本3的UUID算法类似，只是散列值计算使用SHA1（Secure Hash Algorithm 1）算法。
#### UUID的应用
从UUID的不同版本可以看出，Version 1/2适合应用于分布式计算环境下，具有高度的唯一性；Version 3/5适合于一定范围内名字唯一，且需要或可能会重复生成UUID的环境下；至于Version 4，我个人的建议是最好不用（虽然它是最简单最方便的）。
通常我们建议使用UUID来标识对象或持久化数据，但以下情况最好不使用UUID：
映射类型的对象。比如只有代码及名称的代码表
人工维护的非系统生成对象。比如系统中的部分基础数据

对于具有名称不可重复的自然特性的对象，最好使用Version 3/5的UUID。比如系统中的用户。如果用户的UUID是Version 1的，如果你不小心删除了再重建用户，你会发现人还是那个人，用户已经不是那个用户了。（虽然标记为删除状态也是一种解决方案，但会带来实现上的复杂性。）

下面是一些可用的Java UUID生成器：

- Java UUID Generator (JUG)：开源UUID生成器，LGPL协议，支持MAC地址。
- UUID：特殊的License，有源码。
- Java 5以上版本中自带的UUID生成器：好像只能生成Version 3/4的UUID
