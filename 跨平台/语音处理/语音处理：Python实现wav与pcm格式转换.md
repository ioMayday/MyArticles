**Ŀ¼**
[toc]

���⣺��������Pythonʵ��wav��pcm��ʽת��

> ���ߣ���֪��
> ���ںţ���֪��
> ��飺������˶ʿ����Ϊ�㷨����ʦ��רע������顢ְ������������������������

## ֪ʶ��
----

 - wave���Ķ�д����������
 - �ļ�·������ƴ��
 - ��Ƶ��ز����˽�

wav�ļ��洢��`������Ϣ`�У�ͨ�����������ʡ�λ������������ļ���С�����ݴ�С�ȡ���ϸ���ݼ���[wav�ļ���ʽ����](https://blog.csdn.net/gwhcsdn/article/details/70964342)��

##  wavתpcm
-----

˼·��
 - ����wav���߰���������ͷ��ֱ�Ӷ�ȡ����bytes����
 - ����������`wb`ģʽ��������д��pcm�ļ���

```python
import os
import wave

def wav2pcm(input_dir, out_dir) :
    with open(input_dir, 'rb') as wavfile:
        ori_data = wavfile.read() ## ������������bytes����
        wavfile.close()
    with open(out_dir, 'wb') as pcmfile:
        pcmfile.write(ori_data)
        pcmfile.close()

if __name__ == '__main__':
    wav2pcm(os.path.join(r"D:\seq\input\6channel_file.wav"), 
            r"D:\seq\out\6channel_file_48k_16bit.pcm")
```

## pcmתwav
-----


```python
## ���ܣ���pcm�ļ�ת�ɶ�Ӧwav�ļ�����ѹ��

import os
import wave

def pcm2wav(pcm_path, out_path, channel, sample_rate):
    with open(pcm_path, 'rb') as pcm_file:
        pcm_data = pcm_file.read()
        pcm_file.close()
    with wave.open(out_path, 'wb') as wav_file:
        ## ����֮���� 16 // 8�� ��4������0Ϊ����Ч
        wav_file.setparams((channel, 16 // 8, sample_rate, 0, 'NONE', 'NONE'))
        wav_file.writeframes(pcm_data)
        wav_file.close()

if __name__ == '__main__':
    dir = r"C:\Users\Administrator\Desktop\test"
    out_dir = dir + r"\outwav"
    sample_rate = 48000
    channel = 1
    out_path = os.path.join(out_dir, "16k.wav")
    pcm2wav(os.path.join(dir, "16k.pcm"), out_path, channel, sample_rate)
```


## �ο�����
-----

 1. [Python��wave���߰���дWAV����˵��](https://docs.python.org/zh-cn/3/library/wave.html)
 2. [WAV��PCM��ת����Python�汾��](https://www.pythonf.cn/read/67832)
 3. [python��Ƶ�����õ��Ĳ���](https://www.cnblogs.com/xingshansi/p/6799994.html)
 4. [python��׼��wave��ʹ��˵��](https://blog.csdn.net/qq_43474959/article/details/108149334) 
 5. [PCM���ݰ���ʽ˵��](https://www.jianshu.com/p/e568f94cdf6a)
 6. [WAV�ļ���ʽ����](https://blog.csdn.net/gwhcsdn/article/details/70964342)

