
**目录**
[toc]

标题：C学习：C语言中floor函数的NEON优化

## 举例说明
----
假设已有float类型的大数组`x[10000]`和float类型的`y[10000]`，将`x[i]`以向下取整的方式转为都是整数的float类型数组，结果放在`y[i]`数组里。

`ceil/floor/round/int` **傻傻分不清楚？** 先把浮点取整函数梳理如下表：
| 函数名 | 符号 |      功能      |                       举例                       |
| :----: | ---- | :------------: | :----------------------------------------------: |
|  ceil  | ⌈𝑦⌉  |    向上取整    |     正数：(4, 5] -> 5；负数：(-6, -5] -> -5      |
| floor  | ⌊𝑦⌋  |    向下取整    |     正数：[5, 6) -> 5；负数：[-5, -4) -> -5      |
| round  | ⌊𝑦⌉  | 圆整，四舍五入 | 正数：[4.5, 5.5) -> 5；负数： (-5.5, -4.5] -> -5 |
|  int   | NA   |      截断      |     正数：[5, 6) -> 5；负数：(-6, -5] -> -5      |

## 思路分析
---
从上表中可以看出，floor函数的特性就是，非负数时等价于截断功能，负数时若不是整数则减一再截断，若**是负整数则等于自身**。我们容易发现，floor函数处理前后两数间距不可能`>=1`，作为处理特殊情况负整数的依据。

于是有以下思路：
 - 先判断是否为负数
 - 若为非负数，则直接截断
 - 若为负数，则统一都减一后再截断
 - 处理特殊情况：负整数为其本身
 	- 当前结果统一加1，与初始float数据比较
 	- 若初始数据大于等于当前结果，则说明当前间距`>=1`，该值为负整数，需换为初始数据

> 注意，该思路同样也可以推广到ceil和round函数的优化。ceil处理前后数据间距小于1，round函数处理后数据间距不超过0.5。

## 代码实现
---
**原函数：**

```c
#include <math.h>

int i;
for (i = 0; i < 10000; i++)
{
	y[i] = (int)floor(x[i]);
}
```

**优化后：**

```c
#include <math.h>
#include <arm_neon.h>

float32x4_t vf32x4Temp, vf32x4TempV1;
float32x4_t vf32x4Zero, vf32x4One;
int32x4_t vs32x4Temp;
uint32x4_t vu32x4TempV1;

vf32x4Zero = vdupq_n_f32(0.0f);
vf32x4One = vdupq_n_f32(1.0f);

#define STEP_NUM 4
for (i = 0; i < 10000 - STEP_NUM + 1; i += STEP_NUM) {	
    vf32x4Temp = vld1q_f32(&x[i]);
    vf32x4TempV1 = vf32x4Temp;
    vu32x4TempV1 = vcltq_f32(vf32x4Temp, vf32x4Zero); // 判断是否小于0
    vf32x4Temp = vbslq_f32(vu32x4TempV1, vsubq_f32(vf32x4Temp, vf32x4One), vf32x4Temp); // 大于0，则截断；否则，就减1
    vs32x4Temp = vcvtq_s32_f32(vf32x4Temp); // 截断取整，但存在如果是负数且为整数时，多减了1
    vf32x4Temp = vcvtq_f32_s32(vs32x4Temp);
    vu32x4TempV1 = vcgeq_f32(vf32x4TempV1, vaddq_f32(vf32x4Temp, vf32x4One)); // a>=b+1 时，多减1的负数整数本身
    vf32x4Temp = vbslq_f32(vu32x4TempV1, vf32x4TempV1, vf32x4Temp); // 出现a>=b+1情况，必然是负数整数本身，此时赋值为原初始值
    vst1q_f32(&y[i], vf32x4Temp);
}
for (; i < 10000; i++) // 处理数组尾部不足STEP_NUM的数据
{
    y[i] = (float)floor(x[i]);
}
```

扩展阅读：[C语言（C++）中：详解floor函数、ceil函数和round函数](https://blog.csdn.net/dangzhangjing97/article/details/81279862)