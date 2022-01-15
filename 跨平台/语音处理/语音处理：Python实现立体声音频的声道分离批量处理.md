**目录**
[toc]

标题：语音处理：Python实现立体声音频的声道分离批量处理

## 背景

---

在语音处理任务中，有时需要对立体声或多声道音频文件，批量处理成单声道文件，然后送入算法模型进行处理。

这里，提供一个Python脚本实现批量分离，提高处理效率。



## 实现思路

----

**编码思路**

- 采用Python的`wavfile`包
- 先读取多声道音频到data
- 将data中的左右声道分别提取到list中
- 将list数据写入新的单声道音频文件

## Python代码

----

用前需先导入以下包：

```python
import scipy.io.wavfile
```

封装代码如下：

```python
def split_stereo(input_path, output_path):
    # default stereo
    samplerate, data = wavfile.read(input_path)
    left = []
    right = []
    for item in data:
        left.append(item[0])
        right.append(item[1])
    file_name = input_path.split('\\')[-1]
    file_name = file_name.split('.')[0]
    outfile_name = file_name + '_1ch_left.wav'
    out_path_file = os.path.join(output_path, outfile_name)
    wavfile.write(out_path_file, samplerate, np.array(left))
    # wavfile.write('right.wav', samplerate, np.array(right))
```

当然，有兴趣的也可以用专业音频处理软件`Audition`实现批量处理。

## 参考资料

---

1. python 双声道语音分离，[link](https://blog.csdn.net/qq_24822271/article/details/98032946)
2. 立体声音乐分离左右声道，[link](https://blog.csdn.net/dongfuguo/article/details/118705402)
3. 采用soundfile库实现分离的方法，[link](https://blog.csdn.net/weixin_39930671/article/details/111023881)

