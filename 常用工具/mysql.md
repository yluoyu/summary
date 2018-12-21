默认地址
```
/var/log/
```


export GRADLE_HOME=/data/gradle-4.10.3
export PATH=${GRADLE_HOME}/bin:${PATH}
export GRADLE_USER_HOME=/data/gradle-4.10.3/.gradle


#!/bin/bash

cd /data/bootdemo3
#0、删除原有的日志文件

rm -f nohup.out
#1、获取正在运行的 Spring Boot 应用的 pid
appPid=`netstat -ntlp | grep java | awk '{print $7}' | head -1 | grep '[0-9]\+' -o`

#2、关闭正在运行的 Spring Boot 应用
kill -9 ${appPid}

#3、从 git 上拉最新的代码

git pull

#4、使用 Maven 打包最新的代码
gradle clean build -x test

#5、后台运行新的 jar 文件
nohup java -jar build/libs/*.jar &

#6、休息 3 秒
sleep 3

#7、打印最新的日志
tail -f nohup.out
