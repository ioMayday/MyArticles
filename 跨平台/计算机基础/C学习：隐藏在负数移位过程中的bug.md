
**目录**
[toc]

标题：C学习：隐藏在负数移位过程中的bug

## 问题
----
负数移位时，你是否了解以下两条语句的区别？

 - `a /= (1 << bit)`
 - `a >>= bit` 

## 分析
---

 - 若右移位后结果`>=-1`，第一种方法移位与第二种方法移位相同，本质相当于除法。
 - 若右移位后结果`<-1`，则有所区别：第一种方法会保留符号位，不管怎么右移一直保持符号位，结果为-1；第二种方法会丢弃符号位，结果为0；

验证代码如下，可尝试将`MOVE_BIT`修改为4、3、2查看效果：

```c
#define MOVE_BIT 4

int i = -8;
i >>= MOVE_BIT;
printf("i = %d\n", i);

int j = -8;
j /= (1 << MOVE_BIT);
printf("j = %d\n", j);
```


## 总结
---
**总而言之**，直接移位会保留符号位，负数不断右移变大，最大也只能是-1，不能到0，除法移位则会舍弃符号位，右移放大至0。具体采用哪种方法需要根据使用场合灵活应用。



