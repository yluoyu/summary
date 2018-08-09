删除自带输入法
- 操作`~/Library/Preferences/com.apple.HIToolbox.plist`文件,删除包含ABC选项的数字节点
- [完美删除自带中文输入法](https://bbs.feng.com/read-htm-tid-10947138.html)

`已损坏，打不开。你应该将它移到废纸篓`
- 并非你安装的软件已损坏，而是Mac系统的安全设置问题，因为这些应用都是破解或者汉化的,那么解决方法就是临时改变Mac系统安全设置。
- 修改系统配置：系统偏好设置... -> 安全性与隐私。修改为`任何来源`
- 如果没有这个选项的话,需要执行下面命令`sudo spctl --master-disable`
