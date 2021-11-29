**目录**
[toc]


标题：C学习：常用字符串操作函数之strtok和strtok_s

## 基本说明
---
**头文件引用**

```c
#include <string.h>
```

**函数签名**

```c
char * strtok(char *s, const char *delim);
```

**函数功能**

 - 按分割符切割目标字符串
 - 返回分割后的左侧部分字符串首指针

**函数说明**

 - 正常返回是切割后的**左侧字符串首地址**，直到分割完毕或无法分割时，返回`NULL`；
 - 第一次调用需要输入第一个入参指针`s`，找到一个分割符分割成左右两块，返回左边字符串首地址；
 - 第二次调用时，第一个入参输入必须为`NULL`；
 - 函数会改变输入的字符串，**分割字符可以有多个**，本质是将`s`中含有`delim`里目标分割字符的字符都替换为`\0`；
 - 分割符最好采用`const`声明成不可改变的字符串，如：`const char *delim = ":";`  或 `const char split[2] = ","`；
 - `strtok()`内部涉及到`malloc()`，每次调用结果不同，为不可重入函数；
 - 中断当前分隔符使用新的分隔符切割时，方法是：重新传入新入参指针和分割字符串，形如`strtok(newStr, newSplit)`，再次调用分割时`strtok(NULL, newSplit)`；


##  使用示例
---
```c
#include <string.h>
void main(void){
    char s[] = "ab-cd : ef;gh :i-jkl;mnop;qrs-tu: vwx-y;z";
    char *delim = "-: ;";
    char *p;
    printf("%s\n", strtok(s, delim));
    while((p = strtok(NULL, delim)))
        printf("%s\n", p);
    return;
}
```
切割后输出效果为：

> ab
cd
ef
gh
i
jkl
mnop
qrs
tu
vwx
y
z



##  安全函数使用strtok_s()
---
由于`strtok()`不适用于多线程操作，因为用静态变量保存分割下个字符串首地址，且内部有动态分配内存，所以延伸出可重入的安全函数`strtok_s()`。

**注意：**`linux`平台下对应的接口名为`strtok_r()`，用法一致，具体使用方法如下：

```c
// strtok_s用法举例
char str[] = "a.b.c/d.e,ee,eee";
const char demlit[] = ",./";

char *context = NULL;  // context指针保存分割后下一个字符串首地址
char *p = str;
p = strtok_s(p, demlit, &context); // &context为二级指针，便于存储一级指针
while (p != NULL) {
    printf("%s\n", p);
    p = strtok_s(NULL, demlit, &context);
}
```
切割后输出效果为：    

> a
> b
> c
> d
> e
> ee
> eee

**对同一个字符串先后使用两种分割符**，改动如下：

```c
// strtok_s用法举例
char str[] = "a.b.c/d.e,ee,eee";
const char demlit1[] = "/";
const char demlit2[] = ",";

char *context = NULL;  // context指针保存分割后下一个字符串首地址
char *p = str;
p = strtok_s(p, demlit1, &context); // &context为二级指针，便于存储一级指针
printf("%s\n", p);

p = context; // 字符串开头改为/后的d.e...，指向d
p = strtok_s(p, demlit2, &context); // 换为新的分割符和字符串新起始地址
while (p != NULL) {
    printf("%s\n", p);
    p = strtok_s(NULL, demlit2, &context);
}
```
切割后输出效果为：

> a.b.c
> d.e
> ee
> eee

## 扩展阅读
-----
[1、C语言strtok()函数：字符串分割](http://c.biancheng.net/cpp/html/175.html)
[2、菜鸟教程：C 库函数 - strtok()](https://www.runoob.com/cprogramming/c-function-strtok.html)
