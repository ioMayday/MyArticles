[toc]

标题：Python实现CSDN图片水印批量去除

## 背景

---

在使用CSDN做图床时，经常遇到上传的大量图片被自动加上水印，十分影响阅读体验。网上大部分博客，都只是讲CSDN用`markdown`加水印的原理——将图片链接中`.jpg`后面的字符串删去即可去水印，如下图。

![在这里插入图片描述](https://img-blog.csdnimg.cn/ec83170dbf8d44deab1b15cd8855c938.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p2l55-l5pmT,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
>水印去除对比图

如果有大量的图片放到CSDN都手动去除水印，较为繁琐低效。在网上也没搜索到批量去水印的脚本，故用Python实现了下，放在这里，供交流学习。

## 思路理清

-----

**编码思路**

- 获取原始txt，读到每行内容
- 对每行内容分割，分隔符：`?x-oss-process`
- 若有水印，则对左侧字符串追加字符`)`
- 否无水印，则不追加操作
- 重新输出到新txt中
- 直到原始txt内容读取完毕

**使用方法**

- 新建一个UTF-8的文本，复制待去水印的批量markdown图片链接到里面
- Python代码修改对应路径及文件名
- 运行代码，查看去水印后的文本输出

## Python代码

----


```python
# -*- coding: utf-8 -*-
'''
@Time    : 2021年12月30日
function : demo_rm_pic_watermark()
'''

import os
import sys

def rm_watermk(in_path, out_path):
    # f_in = open(in_path)
    f_in = open(in_path, "r", encoding='utf-8')
    line = f_in.readline()
    split_str = r'?x-oss-process'
    out_data = []
    while line:
        if split_str in line:
            str1 = line.split(split_str)[0]
            str2 = str1 + ')'
        else:
            str2 = line
        out_data.append(str2)
        line = f_in.readline()

    with open(out_path, "w", encoding="utf-8") as f_out:
        f_out.write('\n'.join(out_data))


if __name__ == '__main__':
    in_dir = r"D:\tmpFiles"
    out_dir = r"D:\tmpFiles"
    in_name = r'input.txt'
    out_name = r'output.txt'
    in_path = os.path.join(in_dir, in_name)
    out_path = os.path.join(out_dir, out_name)

    print('start rm_watermk funciton!')
    print('processing.....')
    rm_watermk(in_path, out_path)
    print('done!')

    sys.exit()
```