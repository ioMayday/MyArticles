**目录**
[toc]

标题：语音处理：Python实现wav与pcm格式转换

> 作者：来知晓
> 公众号：来知晓
> 简介：哈工大硕士，华为算法工程师，专注分享读书、职场、技术及自我提升等内容

## 知识点
----

 - wave包的读写及参数设置
 - 文件路径操作拼接
 - 音频相关参数了解

wav文件存储的`参数信息`有：通道数、采样率、位深，其他参数如文件大小，数据大小等。详细内容见：[wav文件格式解析](https://blog.csdn.net/gwhcsdn/article/details/70964342)。

##  wav转pcm
-----

思路：
 - 利用wav工具包，跳过包头，直接读取裸流bytes数据
 - 将裸流数据`wb`模式按二进制写入pcm文件中

```python
import os
import wave

def wav2pcm(input_dir, out_dir) :
    with open(input_dir, 'rb') as wavfile:
        ori_data = wavfile.read() ## 读出来是裸流bytes数据
        wavfile.close()
    with open(out_dir, 'wb') as pcmfile:
        pcmfile.write(ori_data)
        pcmfile.close()

if __name__ == '__main__':
    wav2pcm(os.path.join(r"D:\seq\input\6channel_file.wav"), 
            r"D:\seq\out\6channel_file_48k_16bit.pcm")
```

## pcm转wav
-----


```python
## 功能：将pcm文件转成对应wav文件，无压缩

import os
import wave

def pcm2wav(pcm_path, out_path, channel, sample_rate):
    with open(pcm_path, 'rb') as pcm_file:
        pcm_data = pcm_file.read()
        pcm_file.close()
    with wave.open(out_path, 'wb') as wav_file:
        ## 不解之处， 16 // 8， 第4个参数0为何有效
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


## 参考资料
-----

 1. [Python的wave工具包读写WAV操作说明](https://docs.python.org/zh-cn/3/library/wave.html)
 2. [WAV和PCM的转换（Python版本）](https://www.pythonf.cn/read/67832)
 3. [python音频处理常用到的操作](https://www.cnblogs.com/xingshansi/p/6799994.html)
 4. [python标准库wave的使用说明](https://blog.csdn.net/qq_43474959/article/details/108149334) 
 5. [PCM数据包格式说明](https://www.jianshu.com/p/e568f94cdf6a)
 6. [WAV文件格式解析](https://blog.csdn.net/gwhcsdn/article/details/70964342)

