### Homebrew

Mac OS X是基于Unix的，它可以使用非常多Linux平台上开源的优秀工具，比如wget，比如dos2unix脚本工具等

但是OS X系统本身却缺少Linux下得包管理器。比如Fedora的yum与dnf，比如Ubuntu的apt-get，比如ArchLinux的Pacman等

于是这些优秀的开源软件在Mac上的安装只能通过下载源码，编译，安装，配置环境变量的步骤来完成安装。对于大部分的软件，在安装过程中是需要很多的依赖库的，手动去解决这些依赖库是十分痛苦的事情。包管理器干的就是这样的事情：解决软件安装过程中的依赖关系

有一个开源的项目叫Homebrew，完美解决了Mac OS X上没有包管理器的尴尬

#### 安装

- **安装** XCode或者Command Line Tools for Xcode。如果你使用XCode来进行软件的开发，那么只需要在App Store中安装Xcode即可。如果你像我一样，并不使用Xcode这个庞然大物来编码，那么可以安装Command Line Tools for Xcode：打开终端，键入以下代码完成安装
    `xcode-select --install`
- 安装完上面的编译依赖之后，通过下面的代码完成homebrew的安装

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```




#### Homebrew 命令

- brew **info** mysql 主要看具体的信息，比如目前的版本，依赖，安装后注意事项等
- brew **search** mysql #搜索
- brew **update**  更新 Homebrew 的信息
- brew **outdated** 看一下哪些软件可以升级
- brew **upgrade** <xxx>  如果不是所有的都要升级，那就这样升级指定的
- brew **upgrade**;
- brew **cleanup** 清理不需要的版本极其安装包缓存
- brew **cleanup** git #清理单个已安装软件包的历史版本
- brew **home** git 访问软件包官方站
- brew **list** 显示已经安装的所有软件包
- brew **uninstall** git #卸载软件包
- brew **install** git #安装软件包(这里是示例安装的Git版本控制)

#### Homebrew的扩充

Homebrew 本身不能安装软件但是homebrew-cask这个工具扩充了homebrew

**安装**

    brew install caskroom/cask/brew-cask

**使用**

    brew cask install google-chrome
    brew cask uninstall google-chrome
