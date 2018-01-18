### NPM
NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题

```
  npm -v //版本信息
  npm install <Module Name>  //安装模块 本地安装
  npm install -g <Module Name>  //安装模块 全局安装
  npm list -g //查看所有全局安装的块
  npm list grunt // 查看指定模块的版本号
  npm uninstall express // 卸载指定模块
  npm update express // 更新指定模块
  npm search express // 搜索模块

  // 安装淘宝镜像cnpm 之后可以像使用npm一样使用cnpm
  npm install -g cnpm --registry=https://registry.npm.taobao.org
```

#### 本地安装与全局安装

**本地安装**

- 将安装包放在 ./node_modules 下（运行 npm 命令时所在的目录），如果没有 node_modules 目录，会在当前执行 npm 命令的目录下生成 node_modules 目录
- 可以通过 require() 来引入本地安装的包

**全局安装**

- 通常将安装包放在 /usr/local 下或者你 node 的安装目录。通过 npm config set prefix "目录路径" 来设置
- 可以直接在命令行里使用

默认下node.js会在NODE_PATH和目前js所在项目下的node_modules文件夹下去寻找模块，因此，如果只是全局安装，不能直接通过require()的方式去引用模块，需要手动解决包路径的配置问题.
本地安装可以让每个项目拥有独立的包，不受全局包的影响，方便项目的移动、复制、打包等，保证不同版本包之间的相互依赖，这些优点是全局安装难以做到的
