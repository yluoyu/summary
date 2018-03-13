
这里先说下`artifactId`的命名问题，
- Spring官方Starter通常命名为`spring-boot-starter-{name}`
- Spring官方建议非官方Starter命名应遵循`{name}-spring-boot-starter`的格式

### mybatis-spring-boot-starter
我们先简单分析一下mybatis starter的编写，然后再编写自定义的starter。
mybatis中的autoconfigure模块中使用了一个叫做MybatisAutoConfiguration的自动化配置类。这个MybatisAutoConfiguration需要在这些Condition条件下才会执行：
- `@ConditionalOnClass({ SqlSessionFactory.class, SqlSessionFactoryBean.class })`。需要SqlSessionFactory和SqlSessionFactoryBean在classpath中都存在
- `@ConditionalOnBean(DataSource.class)`。 spring factory中需要存在一个DataSource的bean。
- `@AutoConfigureAfter(DataSourceAutoConfiguration.class)`。需要在DataSourceAutoConfiguration自动化配置之后进行配置，因为mybatis需要数据源的支持。

同时在META-INF目录下有个`spring.factories`这个properties文件，而且它的key为`org.springframework.boot.autoconfigure.EnableAutoConfiguration`，这样才会被springboot加载
```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration
```
有了这些东西之后，mybatis相关的配置会被自动加入到spring container中，只要在maven中加入starter即可：
```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
```

### 自定义的starter

这里讲一下我们的Starter要实现的功能，很简单，提供一个Service，包含一个能够将字符串加上前后缀的方法String wrap(String word)。
```java
public class ExampleService {

    private String prefix;
    private String suffix;

    public ExampleService(String prefix, String suffix) {
        this.prefix = prefix;
        this.suffix = suffix;
    }
    public String wrap(String word) {
        return prefix + word + suffix;
    }
}
```
前缀、后缀通过读取application.properties(yml)内的参数获得
```java
@ConfigurationProperties("example.service")
public class ExampleServiceProperties {
    private String prefix;
    private String suffix;
    //省略 getter setter
```
**编写AutoConfigure类**

```java
@Configuration
@ConditionalOnClass(ExampleService.class)
@EnableConfigurationProperties(ExampleServiceProperties.class)
public class ExampleAutoConfigure {

    @Autowired
    private ExampleServiceProperties properties;

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnProperty(prefix = "example.service",value = "enabled",havingValue = "true")
    ExampleService exampleService (){
        return  new ExampleService(properties.getPrefix(),properties.getSuffix());
    }

}
```
最后一步，在resources/META-INF/下创建spring.factories文件，内容供参考下面
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.example.autocinfigure.ExampleAutoConfigure
```

测试：引入example-spring-boot-starter依赖
```
<dependency>
    <groupId>com.example</groupId>
    <artifactId>example-spring-boot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>
 </dependency>
```


自定义springboot的starter，注意这两点：
- 如果自动化配置类需要在程序启动的时候就加载，可以在META-INF/spring.factories文件中定义。如果本次加载还需要其他一些lib的话，可以使用ConditionalOnClass注解协助
- 如果自动化配置类要在`使用自定义注解后`才加载，可以使用自定义注解+`@Import`注解或`@ImportSelector`注解完成

延伸：
- `@ConditionalOnBean`:当容器中有指定的Bean的条件下
- `@ConditionalOnClass`：当类路径下有指定的类的条件下
- @ConditionalOnExpression:基于SpEL表达式作为判断条件
- @ConditionalOnJava:基于JVM版本作为判断条件
- @ConditionalOnJndi:在JNDI存在的条件下查找指定的位置
- `@ConditionalOnMissingBean`:当容器中没有指定Bean的情况下
- `@ConditionalOnMissingClass`:当类路径下没有指定的类的条件下
- @ConditionalOnNotWebApplication:当前项目不是Web项目的条件下
- `@ConditionalOnProperty`:指定的属性是否有指定的值
- `@ConditionalOnResource`:类路径下是否有指定的资源
- `@ConditionalOnSingleCandidate`:当指定的Bean在容器中只有一个，或者在有多个Bean的情况下，用来指定首选的Bean
- @ConditionalOnWebApplication:当前项目是Web项目的条件下

@EnableAutoConfiguration也是借助@Import的帮助，将所有符合自动配置条件的bean定义加载到IoC容器

借助于Spring框架原有的一个工具类：`SpringFactoriesLoader`的支持，@EnableAutoConfiguration可以智能的自动配置功效才得以大功告成
