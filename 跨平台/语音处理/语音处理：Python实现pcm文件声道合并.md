


**Ŀ¼**
[toc]

���⣺��������Pythonʵ��pcm�ļ������ϲ�

> ���ߣ���֪��
> ���ںţ���֪��
> ��飺������˶ʿ����Ϊ�㷨����ʦ��רע������顢ְ������������������������

## ����˼·
-----

���ܣ�

 - �ϲ������ͨ����pcm��һ����ͨ��pcm�ļ�
 - ���ȶ̵�������ĩβ�Զ����㣬����������ȶ���

˼·��

 - ���˽�pcm���ݱ����ʽ������ĩ�ο�����1
 - ��������Ҫ�ϲ��ĵ������ļ�����ÿ����������׷�ӵ��б���
 - ͬʱ������ÿ�����������ݳ��Ƚ����Ӧ�б���λ��`Byte`
 - ����λ�ȷ����������ÿ�����ݵĴ�С����bitΪ16λ����һ������ռ2��`Byte`����Ϊ����
 - �ڳ����б����ҵ���󳤶ȵ����ݣ��Դ���Ϊѭ����ֹ����
 - ��ĳ���ļ��ѵ�ĩβ������

## ʵ�ִ���
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

#    output_pcm =  # ��ʼ��Ϊ�ַ���
    output_pcm = [] # ��ʼ��Ϊ�б�
    frame_len = bit  8;
    
    start = 0
    step = int(frame_len)
    fill_zero = b # ����һ��bytes���͵��ַ���
    for i in range(step) 
        fill_zero += bx00
        
    for i in range(start, max_len, step) 
#        output_pcm += pcm_data1[ii+2] + pcm_data2[ii+2] #�ַ���׷�ӵķ���
        for file in range(file_num) 
            if i + step  pcm_len[file]
                output_pcm.append(pcm_data[file][ii+step])
            else  # ������Ӧ�����ĳ���
                output_pcm.append(fill_zero)
    
    with open(os.path.join(output_dir, 'combine_out_ch{0}_bit{1}_sr{2}.pcm'.format(file_num, bit, sr)), 'wb') as outpcm_file
        outpcm_file.write(b''.join(output_pcm)) # b����ʾ��byte��ʽд�룬����str

```
## �ο�����
-----


 1. [PCM��Ƶ���ݸ�ʽ����](httpsblog.csdn.netljxt523articledetails52068241)
