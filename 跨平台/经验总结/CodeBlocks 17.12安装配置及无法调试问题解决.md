**目录**
[toc]

标题：CodeBlocks 17.12安装配置及无法调试问题解决

> 系统环境：Win10 + CodeBlocks 17.12

## 1 安装配置
在官方网站**下载含MinGW的 CodeBlocks 17.02**，里面自带编译器，否则安装 CodeBlocks后无法编译，需自己再次安装。
接下来，按照图片箭头指示**配置下C++11的标准库**即可进行正常调用。
![下载带MinGW版](https://img-blog.csdnimg.cn/2019072413121063.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)

## 2 无法调试Debugger
调试时，若提示 You need to specify a debbuger program，则你需要配置下调试的二进制文件 **gdb32.exe**，然后即可正常调试。接
下来，按照图片箭头指示**进入Debbuger Setting进行设置**即可进行正常调试。
![CodeBlocks无法调试](https://img-blog.csdnimg.cn/20190724131446365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE3MjU2Njg5,size_16,color_FFFFFF,t_70)