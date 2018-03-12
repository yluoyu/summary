
SA 即ServiceAbility

#### 概述

SA包含在$JAVA_HOME/lib/sa-jdi.jar中，包括三个工具
**命令行版本：CLHSDB**
```
java -cp .:$JAVA_HOME/lib/sa-jdi.jar sun.jvm.hotspot.CLHSDB
```
**图形界面版本:HSDB**

```
java -cp .:$JAVA_HOME/lib/sa-jdi.jar sun.jvm.hotspot.HSDB
```
**Javascript引擎版本:JSDB**
```
java -cp .:$JAVA_HOME/lib/sa-jdi.jar sun.jvm.hotspot.tools.soql.JSDB
```
#### 用途
>SA的一个限制是它只实现了调试snapshot的功能,因此要么要让被调试的目标进程完全暂停，要么就调试core dump;另外注意，SA调试通常要求拥有root权限，否则会报错

**调试本地进程**
1. 获取要调试的进程ID
```
ps -ef|grep java
jps -mvl
```
2. attch进程
```
java -cp .:$JAVA_HOME/lib/sa-jdi.jar sun.jvm.hotspot.CLHSDB
attach PID
```
或者采用图形界面版本
```
sudo java -cp .:$JAVA_HOME/lib/sa-jdi.jar sun.jvm.hotspot.HSDB
通过菜单"File"->"Attach to HotSpot process"，录入PID
```

#### 借助HSDB查看JVM数据
[借HSDB来探索HotSpot VM的运行时数据](http://rednaxelafx.iteye.com/blog/1847971?page=2)
[借助HotSpot SA来一窥PermGen上的对象](http://rednaxelafx.iteye.com/blog/730461)
