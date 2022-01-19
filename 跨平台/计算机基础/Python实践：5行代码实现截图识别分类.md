[toc]

标题：Python实践：5行代码实现截图识别分类



![在这里插入图片描述](https://img-blog.csdnimg.cn/38a42ba4488a409989dc4f548bc03063.png)

## 问题背景

----

在进行相片整理时，一般生活照片存档供回忆，截图保存在工作目录供引用。可面对几千张图片，为了筛出生活照片，需要剔除大量截图。`手动去分类出截图文件，太繁琐`。遂尝试代码实现，批量分类并移动至指定目录。



## 实现思路

---

分析知，截图文件最大的特征就是exif中无相机制造商，故可以此为突破口。

**代码思路：**

- 利用os模块获取文件夹下所有文件名
- 循环遍历所有文件
    - 利用exifread模块，读取exif信息
    - 判断若为heic格式，则为手机拍摄图片，直接跳过
    - 判断tag中是否存在相机制造商信息
        - 若存在，则不是截图
        - 若存在，则是截图
            - 利用shutil模块，移动文件至指定目录，指令：`shutil.move(“oldpos”,”newpos”)`
- 遍历完毕，提示done



## Python源码

----

```python
# -*- coding: utf-8 -*-

import os
import exifread
import sys
import shutil

def move_file(src_path, dst_path, file):
    # print('from : {}'.format(src_path))
    # print('to   : {}'.format(dst_path))

    # cmd = 'chmod -R +x ' + src_path
    # os.popen(cmd)
    f_src = os.path.join(src_path, file)
    if not os.path.exists(dst_path):
        os.mkdir(dst_path)
    f_dst = os.path.join(dst_path, file)
    shutil.move(f_src, f_dst)
    # 正式退出main函数进程，以免main函数空跑
#    sys.exit()  # spyder不需要每次都关闭ipython，pycharm则需要，因为每次都是重新生成并调用的python


def is_screenshot(path, filename):
    old_full_file_name = os.path.join(imgpath, filename)
    # FIELD = 'EXIF DateTimeOriginal'
    FIELD = 'Image Make'
    fd = open(old_full_file_name, 'rb')
    tags = exifread.process_file(fd)
    fd.close()
    #显示图片所有的exif信息
    # print("showing res of getExif: \n")
    # print(tags)
    # print("\n\n\n\n");
    if FIELD in tags:
        equip_name = str(tags[FIELD])
        print("\n name:%s str(tags[FIELD]): %s" %(filename, equip_name))
        return False
    else:
        return True
        

# python 主程序，当本文件被执行的时候，运行下列语句：
if __name__ == "__main__":
    src_path = r"D:\BaiduNetdiskDownload\xx"
    dst_path = r"D:\BaiduNetdiskDownload\yy"
    # file = r"xx.jpg"
    
    print('start processing!')
    imgpath = src_path
    cnt = 0
    for filename in os.listdir(imgpath):
         # os.path.join用于路径拼接，将imgpath和filename连在一起得到完整的路径，后面的参数可有多个，从第一个以”/”开头的参数开始拼接
        full_file_name = os.path.join(imgpath, filename)
        
        if '.heic' in filename:
            continue

        # os.path.isfile用于判断路径指向的是否为文件，相类似的os.path.isdir用于判断是否为文件夹
        if os.path.isfile(full_file_name) and is_screenshot(imgpath, filename):
           move_file(src_path, dst_path, filename)
           cnt += 1
           print('\r cnt={}'.format(cnt), end='')
           
    print(' ')
    print('done!')
```





## 参考资料

---

1. python移动文件，[https://blog.csdn.net/xuezhangjun0121/article/details/80859679](https://blog.csdn.net/xuezhangjun0121/article/details/80859679)
2. python解析照片拍摄时间和地点，[https://blog.csdn.net/yph001/article/details/84288620](https://blog.csdn.net/yph001/article/details/84288620)