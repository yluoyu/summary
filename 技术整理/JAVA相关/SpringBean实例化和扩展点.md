### 整体大致流程图

![流程图](https://i.loli.net/2018/02/10/5a7ef944c42dc.png)


### 扩展点
一般而言，开发者不需要继承ApplicationContext实现类，可以通过实现特定的集成接口来扩展Spring IoC容器的功能。

**一、InitialingBean和DisposableBean**
- InitialingBean是一个接口，提供了一个唯一的方法afterPropertiesSet()
- DisposableBean也是一个接口，提供了一个唯一的方法destory()

这两个接口是一组的，功能类似，因此放在一起：前者顾名思义在Bean属性都设置完毕后调用afterPropertiesSet()方法做一些初始化的工作，后者在Bean生命周期结束前调用destory()方法做一些收尾工作

关于这两个接口，我总结几点：

1、InitializingBean接口、Disposable接口可以和init-method、destory-method配合使用，接口执行顺序优先于配置

2、InitializingBean接口、Disposable接口底层使用类型强转.方法名()进行直接方法调用，init-method、destory-method底层使用反射，前者和Spring耦合程度更高但效率高，后者解除了和Spring之间的耦合但是效率低，使用哪个看个人喜好

3、afterPropertiesSet()方法是在Bean的属性设置之后才会进行调用，某个Bean的afterPropertiesSet()方法执行完毕才会执行下一个Bean的afterPropertiesSet()方法，因此不建议在afterPropertiesSet()方法中写处理时间太长的方法

**二、BeanNameAware、ApplicationContextAware和BeanFactoryAware**

- 实现BeanNameAware接口的Bean，在Bean加载的过程中可以获取到该Bean的id
- 实现ApplicationContextAware接口的Bean，在Bean加载的过程中可以获取到Spring的ApplicationContext，这个尤其重要，ApplicationContext是Spring应用上下文，从ApplicationContext中可以获取包括任意的Bean在内的大量Spring容器内容和信息
- 实现BeanFactoryAware接口的Bean，在Bean加载的过程中可以获取到加载该Bean的BeanFactory

关于这三个接口以及上面的打印信息，总结几点：
- 如果你的BeanName、ApplicationContext、BeanFactory有用，那么就自己定义一个变量将它们保存下来，如果没用，那么只需要实现setXXX()方法，用一下Spring注入进来的参数即可
- 如果Bean同时还实现了InitializingBean，容器会保证BeanName、ApplicationContext和BeanFactory在调用afterPropertiesSet()方法被注入

**三、FactoryBean**
传统的Spring容器加载一个Bean的整个过程，都是由Spring控制的，换句话说，开发者除了设置Bean相关属性之外，是没有太多的自主权的。FactoryBean改变了这一点，开发者可以个性化地定制自己想要实例化出来的Bean，方法就是实现FactoryBean接口

FactoryBean的几个方法：
- getObject()方法是最重要的，控制Bean的实例化过程
- getObjectType()方法获取接口返回的实例的class
- isSingleton()方法获取该Bean是否为一个单例的Bean

**BeanFactory与FactoryBean的区别**
- BeanFactory，以Factory结尾，表示它是一个工厂类(接口)，用于管理Bean的一个工厂。在Spring中，BeanFactory是IOC容器的核心接口，它的职责包括：实例化、定位、配置应用程序中的对象及建立这些对象间的依赖
- FactoryBean 以Bean结尾，表示它是一个Bean，不同于普通Bean的是：它是实现了FactoryBean<T>接口的Bean，根据该Bean的ID从BeanFactory中获取的实际上是FactoryBean的getObject()返回的对象，而不是FactoryBean本身，如果要获取FactoryBean对象，请在id前面加一个&符号来获取

FactoryBean 通常是用来创建比较复杂的bean，一般的bean 直接用xml配置即可，但如果一个bean的创建过程中涉及到很多其他的bean 和复杂的逻辑，用xml配置比较困难，这时可以考虑用FactoryBean

通过使用FactoryBean，我们可以得到不同类型的对象实例。这也就是我们在AOP中通过设置calss为ProxyFactoryBean可以返回不同类型的业务对象的原理

**四、BeanPostProcessor**
之前的InitializingBean、DisposableBean、FactoryBean包括init-method和destory-method，针对的都是某个Bean控制其初始化的操作，而似乎没有一种办法可以针对每个Bean的生成前后做一些逻辑操作，PostProcessor则帮助我们做到了这一点
网上有一张图画了Bean生命周期的过程：
![bean](https://i.loli.net/2018/02/10/5a7f0531af244.png)

BeanPostProcess接口有两个方法:
- postProcessBeforeInitialization：在初始化Bean之前
- postProcessAfterInitialization：在初始化Bean之后

值得注意的是，这两个方法是有返回值的，不要返回null，否则getBean的时候拿不到对象

**五、BeanFactoryPostProcessor**
Spring IoC容器允许BeanFactoryPostProcessor在容器实例化任何bean之前读取bean的定义(配置元数据)，并可以修改它。同时可以定义多个BeanFactoryPostProcessor，通过设置'order'属性来确定各个BeanFactoryPostProcessor执行顺序

Spring允许在Bean创建之前，读取Bean的元属性，并根据自己的需求对元属性进行改变，比如将Bean的scope从singleton改变为prototype，最典型的应用应当是PropertyPlaceholderConfigurer，替换xml文件中的占位符，替换为properties文件中相应的key对应的value，这将会在下篇文章中专门讲解PropertyPlaceholderConfigurer的作用及其原理。
BeanFactoryPostProcessor就可以帮助我们实现上述的功能

- BeanFactoryPostProcessor的执行优先级高于BeanPostProcessor
- BeanFactoryPostProcessor的postProcessBeanFactory()方法只会执行一次

注意到postProcessBeanFactory方法是带了参数ConfigurableListableBeanFactory的，这就和我之前说的可以使用BeanFactoryPostProcessor来改变Bean的属性相对应起来了。ConfigurableListableBeanFactory功能非常丰富，最基本的，它携带了每个Bean的基本信息

**六、InstantiationAwareBeanPostProcessor**

InstantiationAwareBeanPostProcessor又代表了Spring的另外一段生命周期：实例化。先区别一下Spring Bean的实例化和初始化两个阶段的主要作用

- 实例化----实例化的过程是一个创建Bean的过程，即调用Bean的构造函数，单例的Bean放入单例池中
- 初始化----初始化的过程是一个赋值的过程，即调用Bean的setter，设置Bean的属性

**之前的BeanPostProcessor作用于过程（2）前后，现在的InstantiationAwareBeanPostProcessor则作用于过程（1）前后**

InstantiationAwareBeanPostProcessor作用的是Bean实例化前后，即：
- 1、Bean构造出来之前调用postProcessBeforeInstantiation()方法
- 2、Bean构造出来之后调用postProcessAfterInstantiation()方法

不过通常来讲，我们不会直接实现InstantiationAwareBeanPostProcessor接口，而是会采用继承InstantiationAwareBeanPostProcessorAdapter这个抽象类的方式来使用。

### 应用实例

**FactoryBean**
- 很多开源项目在集成Spring 时都使用到FactoryBean，比如 MyBatis3 提供 mybatis-spring项目中的 org.mybatis.spring.SqlSessionFactoryBean
- 阿里开源的分布式服务框架 Dubbo 中的Consumer 也使用到了FactoryBean



#### 配置中心
