### BootDemo
最初学习使用spring boot的练手项目,后续觉得spring boot挺好用的，然后在这上面完成了许多新功能新特性。
入口：[BootDemo](https://gitee.com/yluoyu/bootdemo)

#### 目录结构

```shell
|--base  //基础常用类
|--cache //注解实现缓存 两种写法 aspect和advisor
|--config //配置类
|--constant //常量
|--controller //RESTFull接口
|--dao     //常见dao
|--database // 主要是写多数据源时候用到
|--exception //封装的业务异常
|--filter // 过滤器 这里写了些处理request和response上的处理器 特别的是content-type处理
|--po // POJO类
|--utils // 常用的工具类
|--BootdemoApplication //启动类
```

#### 主要特性

**MyBatis 使用**
- **BaseDao<T,K>** 使用这个基类，其他各个子类只需要写自己独特的方法
- 同一个表的mapper文件分开写，基类的中的在一个文件，自己独有的方法在另一个，这样改变表结构的时候，可以直接使用generator插件重写那个文件。
- **Example** 发现使用Example还是很方便的

**BaseController**

- 结合BaseDao实现了增删改查四个基本接口

**ContentTypeFilter**

- Request和Response只可读一次需要使用装饰模式
- 对Request中Content-type的理解

**多数据源支持**

- 主要是spring底层本身支持多数据源 继承**AbstractRoutingDataSource**
