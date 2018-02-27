Linux 使用退格键时出现^H解决方法
当我们再和脚本交互的时候，在终端上输错了内容，使用退格键，屏幕上会出现乱码，比如 H。H不是H键的意思，是backspace 解决方法：
- 要使用回删键（backspace）时，同时按住ctrl键
- 设定环境变量 在脚本的开头可结尾 参数 stty erase ^H stty erase ^?
