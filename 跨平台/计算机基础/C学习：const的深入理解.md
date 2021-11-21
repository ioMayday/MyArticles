**目录**
[toc]

标题：C学习：const的深入理解

## 一句话总结
----
const放在变量前表示为常量，值不可改变，这种情况很容易区分。

但是，当const与结构体或者其指针结合时，该如何区分呢，是指针不变还是指向的内容不变？


**原则**：
 - const放在`*`前，表示内容不变
 - 放在`*`和变量中间，表示指针不变


## 实例分析
-----
```c
PstHandle * const st; // 指针地址不变
st->on = 1;           // 可以
st = ptrSt1;          // 不可以

const PstHandle *st;  // 指向内容不变
PstHandle const *st;  // 与上句等价
st->on= 1;            // 不可以
st = ptrSt1;          // 可以
```

## 扩展资料
----

1. [const的理解、const指针、指向const的指针](https://www.cnblogs.com/qxj511/p/4965793.html)


