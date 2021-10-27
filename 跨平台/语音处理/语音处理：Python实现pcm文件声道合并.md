


**目录**
[toc]

标题：语音处理：Python实现pcm文件声道合并

> 作者：来知晓
> 公众号：来知晓
> 简介：哈工大硕士，华为算法工程师，专注分享读书、职场、技术及自我提升等内容

## 功能思路
-----

功能：

 - 合并多个单通道的pcm成一个多通道pcm文件
 - 长度短的声道，末尾自动补零，与最长声道长度对齐

思路：

 - 先了解pcm数据保存格式，见文末参考资料1
 - 遍历所有要合并的单声道文件，将每个声道数据追加到列表中
 - 同时，保存每个声道的数据长度进入对应列表，单位是`Byte`
 - 根据位深，确定单个声道每块数据的大小，如bit为16位，则一个数据占2个`Byte`，作为步长
 - 在长度列表中找到最大长度的数据，以此作为循环终止条件
 - 若某个文件已到末尾，则补零

## 实现代码
-----
```python
# -- coding utf-8 --

import os

if __name__ == '__main__'
    input_dir = rDseqpcm
    output_dir = input_dir + rcombin

    pcm_data = []
    pcm_len = []
    file_num = 3
    
    for i in range(file_num) 
        with open(os.path.join(input_dir, file%d.pcm %(i + 1)), 'rb') as pcmfile
#            pcm_data[i] = pcmfile.read()
#            pcm_len[i] = len(pcm_data[i])
            pcm_data.append(pcmfile.read())
            pcm_len.append(len(pcm_data[i]))
            print(i={0}, len={1}.format(i, len(pcm_data[i])))
            
    max_len = max(pcm_len)
    
    ch = file_num
    sr = 48000
    bit = 16

#    output_pcm =  # 初始化为字符串
    output_pcm = [] # 初始化为列表
    frame_len = bit  8;
    
    start = 0
    step = int(frame_len)
    fill_zero = b # 声明一个bytes类型的字符串
    for i in range(step) 
        fill_zero += bx00
        
    for i in range(start, max_len, step) 
#        output_pcm += pcm_data1[ii+2] + pcm_data2[ii+2] #字符串追加的方法
        for file in range(file_num) 
            if i + step  pcm_len[file]
                output_pcm.append(pcm_data[file][ii+step])
            else  # 超过对应声道的长度
                output_pcm.append(fill_zero)
    
    with open(os.path.join(output_dir, 'combine_out_ch{0}_bit{1}_sr{2}.pcm'.format(file_num, bit, sr)), 'wb') as outpcm_file
        outpcm_file.write(b''.join(output_pcm)) # b，表示用byte方式写入，而非str

```
## 参考资料
-----


 1. [PCM音频数据格式介绍](httpsblog.csdn.netljxt523articledetails52068241)
