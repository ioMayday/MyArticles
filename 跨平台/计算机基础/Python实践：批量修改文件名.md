# Python实践：批量修改文件名


[toc]




![在这里插入图片描述](https://img-blog.csdnimg.cn/38a42ba4488a409989dc4f548bc03063.png)
## 问题背景

----

因项目需要，有个需求是**将某目录下各文件添加特定格式的后缀标记**，为提高效率，遂编写此自动化脚本。读者可根据自身需要进行修改。



## 实现思路

-----

前提：码流名字中含有声道数、01、码率信息，且命名格式固定
功能：利用已经生成好的码流及其名字，生成对应的解码指令和md5sum

**编码思路：**

- 获取文件名字
- 统一文件命名格式，`ch_xx_bitrate_0/1.bit`
- 从文件名中获取`ch/bitrate/01`信息
- 根据输入要求，连接新的文件名字符串
- 利用rename函数进行重命名



## Python代码

-----

前置知识：

- `import os`, 导入系统文件操作包
- `os.listdir(文件夹路径)`，获取指定目录下的所有子目录和文件名 
- `os.chdir(文件夹路径）`，切换到当前目录下
- `os.rename(原文件名，新文件名）`，对文件或目录改名

具体代码：

```python
# -*- coding: utf-8 -*-
'''
@Time    : 2021年12月31日
'''

import os

# 批量改名函数
def rename_file(path):
    file_list = os.listdir(path)
    # 必须先切换到当前目录下，os.rename才能找到对应文件去改名
    os.chdir(path)
    for old_name in file_list:
#        # 改2声道
#        new_name = "2_" + old_name
        
        # 改1-channels
        tmp_name = old_name.split('-')[1]
        print(tmp_name)
        ch = old_name.split('_')[1]
        print(ch)
        new_name = ch + '_' + tmp_name

        # 改名
        os.rename(old_name, new_name)
        
    return


if __name__ == '__main__':
    path = r"D:\sequences\input"
    outPath = r"D:\sequences\output"
    inputBatPath = r"D:\sequences\bat"
    pathRename = r"D:\sequences\files"
    namelist = os.listdir(path)
    
    
    # 批量重命名文件
#    rename_file(pathRename)
     
    with open(os.path.join(outPath, "file_list_test.txt"), "w", encoding="UTF-8") as f:
        f.write('\n'.join(namelist[:-1]))

    all_test_list = []
    all_md5sum_list = []
     
    for name in namelist:
        # 获取信息
        ch = name.split('_')[0]
        isSetereo = name.split('_')[-1]
        isSetereo = isSetereo[:-4] #左闭右开，去掉.bit
        bitrate = name.split('_')[-2]
        
        # 连接指令
        dec_cmd = '{0} {1} {2} /mnt/data/{3}  '.format(ch, isSetereo, bitrate, name)
        md5_cmd = 'md5sum /mnt/data//{0}_out.wav  '.format(name[:-4])
        
        # 追加到输出数组
        all_test_list.append(dec_cmd) 
        all_md5sum_list.append(md5_cmd)    

    with open(os.path.join(outPath, "test_list.txt"), "w", encoding="UTF-8") as f:
        f.write('\n'.join(all_test_list))

    with open(os.path.join(outPath, "md5sum_out.sh"), "w", encoding="UTF-8") as f:
        f.write('\n'.join(all_md5sum_list))
```



## 参考资料

----

1. 简书：Python修改文件名，[link](https://www.jianshu.com/p/a66dcb250d7a)