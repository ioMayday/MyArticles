**目录**
[toc]

标题：GDB：break断点调试小结
## 常用命令
-----
举个例子：

假设我们的`test.c`文件中有`add()`函数，`break`指令可用`b`缩写替代。

**1）普通断点**，运行到指定位置就停止

```c
b 6   			// 表示在程序开始第6行暂停
b +6			// 表示在当前暂停位置往后偏移6行，打个断点
b -6			// 表示在当前暂停位置往前偏移6行，打个断点
b add			// 表示在程序中函数add开头处暂停
b test.c:6		// 表示在test.c文件的第6行暂停
b test.c:add	// 表示在test.c文件中函数add开头处暂停
```
**2）条件断点**，在1的基础上，增加条件判别式，满足则中断

```c
b test.c:add if num < 1 	// 表示在test.c文件中函数add开头处判断变量num，满足条件时才暂停
```


下面两段具体分析摘自参考资料《GDB break（b）：设置断点》。
![图自：GDB break（b）：设置断点](https://img-blog.csdnimg.cn/20210612181320114.png)
![图自：GDB break（b）：设置断点](https://img-blog.csdnimg.cn/20210612181320141.png)

## 参考资料
1. [【C语言中文网】GDB break（b）：设置断点](http://c.biancheng.net/view/8189.html)
