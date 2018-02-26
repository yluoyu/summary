
#### maven 配置全局中央仓库镜像
在Maven的settings.xml配置, 只需在mirrors节点里面加上一个mirror子节点

```xml
<mirror>
    <!--This sends everything else to /public -->
    <id>nexus-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```
