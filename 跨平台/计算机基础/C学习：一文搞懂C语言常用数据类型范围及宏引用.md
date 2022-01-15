**目录**
[toc]

标题：C学习：一文搞懂C语言常用数据类型范围及宏引用

## 常见类型数值范围
-----
**`char/signed char/int/long long` 的整数范围整理如下表：**
 |类型 |所占字节| 最小值|最大值 
----|----|---|-----
char  						|1			|0 											|127		
unsigned char      	|1        	|0												|255
signed char        	 	|1        	|-128										|127
unsigned short      	|2        	|0												|65535（常用）
short int           		|2        	|-32768									|32767（常用）
unsigned int				|4        	|0												|4294967295  (`2^32 - 1`)
int / signed int      	|4        	|-2147483648 （`-2^31`）		|2147483647 (`2^31 - 1`)
long int / long     		|4        	|-2147483648							|2141483647
unsigned long long 	|8			|0												|`2^64 - 1`
long long int       		|8        	|-9223372036854775808		|9223372036854775807 （`2^63 - 1`）

**说明**：一个Byte占8bit，`unsigned int`类型，则占32bit，可表示的最大正整数是`2^32 - 1`，因为含0，所以要减一。而`signed int`类型，由于最高位为符号位，故只有31位有效，可表示最大正整数为`2^31 - 1`，最小负数为`-2^31`。
## 数据类型范围宏定义
----

所需头文件limits.h：
 - C： `#include <limits.h>`
 - C++： `#include <climits>`

C语言各个类型的最大值和最小值的宏定义如下，有了这个就再也不用记那么多范围啦，可直接引用最值范围对应的宏即可:
宏定义 | 类型 | 最值 
----|----|---
|CHAR_MAX 						|char					|127
|CHAR_MIN 　　　　　		|char					|-128
|SCHAR_MAX 　　　　　 	|signed char 		|127
|SCHAR_MIN 　　　　　 	|signed char 		|-128
|UCHAR_MAX 　　　　　	|unsigned char 	|255
|SHRT_MAX 　　　　　　	|short 					|32767
|SHRT_MIN　　　　　　 	|short 					|-32768
|USHRT_MAX　　　　　 	|unsigned short 	|65535
|INT_MAX　　　　　　　	|int 						|2147483647
|INT_MIN 　　　　　　　	|int 						|-2147483648
|UINT_MAX 　　　　　　	|unsigned int 		|4294967295U
|UINT_MIN  　　　　　　	|unsigned int 		|0
|LONG_MAX　　　　　　	|long					|9223372036854775807
|LONG_MIN 　　　　　　	|long					|-9223372036854775808
|ULONG_MAX 　　　　　	|unsigned long 	|18446744073709551615
|FLT_MIN 　　　　　　　	|float					|保留全部精度的正数最小值
|FLT_MAX　　　　　　　 	|float					|正数最大值
|FLT_MANT_DIG　　　　	|float 					|尾数
|FLT_DIG　　　　　　　  	|float 					|最少有效数字位数
|FLT_MIN_10_EXP　　　	|float					|带有全部有效数的的负指数的最小值（以10为底）
|FLT_MAX_10_EXP 　　 	|float					|正指数的最大值（以10为底）

## 参考资料
----

 1. [C语言的整型溢出问题 int、long、long long取值范围 最大最小值](https://blog.csdn.net/qq_16234613/article/details/77541722)
