默认缓存jar文件位置
`~/.gradle/caches/modules-2/files-2.1`

启用init.gradle文件的方法：
1、在命令行指定文件，例如：gradle –init-script yourdir/init.gradle -q taskName.你可以多次输入此命令来指定多个init文件
2、把init.gradle文件放到USER_HOME/.gradle/ 目录下.
3、把以.gradle结尾的文件放到USER_HOME/.gradle/init.d/ 目录下.
4、把以.gradle结尾的文件放到GRADLE_HOME/init.d/ 目录下

配置全局中央仓库镜像
`当前用户目录/.gradle/init.gradle`
```
allprojects {
    repositories {
        mavenLocal()
        maven{
        	name "aliyun"
        	url "http://maven.aliyun.com/nexus/content/groups/public/"
        }
    }
}
```
