**目录**
[toc]

标题：机器学习：Kmeans聚类算法总结及GPU配置加速demo

## Kmeans算法介绍

-----

- **算法简介**
	- 该算法是一种贪心策略，初始化时随机选取N个质心，不断迭代，让周围元素到质心的误差累积和最小，从而找到质心或者说对应的簇。

- **核心步骤**
  - 先得到待分类的大量数据（多维向量）
  - 初步尝试得到最佳的分类簇数量
  - 根据最佳簇数量和随机起点作聚类
  - 得到最佳簇划分的码矢，将其编制成固定顺序
  - 计算得到个别数据对应的index，即码矢的索引序号
  - 码矢的集合组成码本
- **评判指标**
  - 簇内相似度高，即被分类到某一簇的样本，离簇的距离足够小
  - 簇间相似度低，即每个簇的差距较大，能表征更多信息

下面Python代码实践总结如下，分别布置在CPU和GPU上。

## 版本1：利用sklearn的kmeans算法，CPU上跑

-------

- 好处
  - 快速调用机器学习库，sklearn
  - 适合进行码本训练和简单分类任务

- 劣势
  - 问题当数据量大时，迭代速度较慢

- 参考链接：[here](https://www.analyticsvidhya.com/blog/2021/01/in-depth-intuition-of-k-means-clustering-algorithm-in-machine-learning/)


```python
import module_kmeans
dir_in = r"/home/work/codebook_train_data/"
# module_kmeans.sf_kmeans(dir_in)
```

对应的调用脚本文件：`sf_kmeans.py`，内部代码如下：

```python
# -*- coding: utf-8 -*-
import sys
import os
import wave
from scipy.io import wavfile
import numpy as np

import pandas as pd
import matplotlib.pyplot as plt

# error
# from sklearn.datasets.samples_generator import make_blobs
from sklearn.datasets import make_blobs
from sklearn.cluster import KMeans


def sf_kmeans(path):
    data = pd.read_csv(path + 'sf_taylor.csv')
    X = data.iloc[:, 0:16]  # get low 16 values

    # X, y = make_blobs(n_samples=300, centers=4, cluster_std=0.60, random_state=0)
    # plt.scatter(X[:, 0], X[:, 1])
    # plt.close()

    # plt.figure()
    max_range = 1025
    wcss = []
    for i in range(1, max_range):
        kmeans = KMeans(n_clusters=i, init='k-means++', max_iter=300, n_init=10, random_state=0)
        kmeans.fit(X)
        wcss.append(kmeans.inertia_)
    plt.figure()
    plt.grid(linestyle='-.')
    plt.title('Elbow Method')
    plt.xlabel('Number of clusters')
    plt.ylabel('WCSS')
    plt.plot(range(1, max_range), wcss)
    plt.show()

    plt.figure()
    kmeans = KMeans(n_clusters=4, init='k-means++', max_iter=300, n_init=10, random_state=0)
    pred_y = kmeans.fit_predict(X)
    plt.grid(linestyle='-.')
    plt.scatter(X[:, 0], X[:, 1])
    plt.scatter(kmeans.cluster_centers_[:, 0], kmeans.cluster_centers_[:, 1], s=300, c='red')
    plt.show()

    print(kmeans.labels_)
    print(kmeans.cluster_centers_)

    print('done!')
```

## 版本2：利用网上的kmeans算法实现，GPU上跑

------

- 好处：
  - 能解决迭代速度问题
- 劣势：
  - 随之而来的问题是，该算法实现精度较差
  - 无法达到与本地CPU跑的sklearn的kmeans算法效果
- 参考链接：[here](https://cloud.tencent.com/developer/article/1729690)

```python
import torch
import time
from tqdm import tqdm
import pandas as pd

# import pdb # for debug
# pdb.set_trace()

class KMEANS:
    def __init__(self, n_clusters=20, max_iter=None, verbose=True,device = torch.device("cuda")):
        self.n_cluster = n_clusters # n_clusters > 0
        self.n_clusters = n_clusters
        self.labels = None
        self.dists = None  # shape: [x.shape[0],n_cluster]
        self.centers = None
        self.variation = torch.Tensor([float("Inf")]).to(device)
        self.verbose = verbose
        self.started = False
        self.representative_samples = None
        self.max_iter = max_iter
        self.count = 0
        self.device = device

    def fit(self, x):
        # 随机选择初始中心点，想更快的收敛速度可以借鉴sklearn中的kmeans++初始化方法
        init_row = torch.randint(0, x.shape[0], (self.n_clusters,)).to(self.device)
        init_points = x[init_row]
        self.centers = init_points
        while True:
            # 聚类标记
            self.nearest_center(x)
            # 更新中心点
            self.update_center(x)
            if self.verbose:
                print(self.variation, torch.argmin(self.dists, (0)))
            if torch.abs(self.variation) < 1e-3 and self.max_iter is None:
                break
            elif self.max_iter is not None and self.count == self.max_iter:
                break

            self.count += 1

        self.representative_sample()

    def nearest_center(self, x):
        labels = torch.empty((x.shape[0],)).long().to(self.device)
        dists = torch.empty((0, self.n_clusters)).to(self.device)
        for i, sample in enumerate(x):
            dist = torch.sum(torch.mul(sample - self.centers, sample - self.centers), (1))
            labels[i] = torch.argmin(dist)
            dists = torch.cat([dists, dist.unsqueeze(0)], (0))
        self.labels = labels
        if self.started:
            self.variation = torch.sum(self.dists - dists)
        self.dists = dists
        self.started = True

    def update_center(self, x):
        centers = torch.empty((0, x.shape[1])).to(self.device)
        for i in range(self.n_clusters):
            mask = self.labels == i
            cluster_samples = x[mask]
            centers = torch.cat([centers, torch.mean(cluster_samples, (0)).unsqueeze(0)], (0))
        self.centers = centers

    def representative_sample(self):
        # 查找距离中心点最近的样本，作为聚类的代表样本，更加直观
        self.representative_samples = torch.argmin(self.dists, (0))

        
def sf_kmeans(matrix,device):
    max_range = 4
    wcss = []
    gpu_speeds = []

    print(matrix.shape)
    print(matrix)
    print('\n')
    
    for i in tqdm(range(1, max_range + 1)):
#         print('%d'%(i), end='\r')
        a = time.time()
        kmeans = KMEANS(n_clusters=i, max_iter=None, verbose=False, device=device)
        kmeans.fit(matrix)
#         wcss.append(kmeans.inertia_)
        wcss.append(torch.sum(kmeans.dists))
#         print(torch.sum(kmeans.dists) / k)
#         print(kmeans.variation)
        b = time.time()
        speed = (b - a) / kmeans.count
        gpu_speeds.append(speed)
        print(kmeans.centers)
        print(kmeans.dists)
        print(torch.sum(kmeans.dists))
        print('\n')
          
#     plt.figure()
    plt.grid(linestyle='-.')
    plt.title('Elbow Method')
    plt.xlabel('Number of clusters')
    plt.ylabel('WCSS')
    plt.plot(range(max_range), wcss)
    plt.show()
    
    plt.figure()
    l2, = plt.plot(range(max_range), gpu_speeds, color='g',label = "GPU")
    plt.xlabel("num_features")
    plt.ylabel("speed(s/iter)")
    plt.title("Speed with cuda")
    plt.legend(handles = [l2], labels = ['GPU'], loc='best')


def choose_device(cuda=False):
    if cuda:
        device = torch.device("cuda:0")
    else:
        device = torch.device("cpu")
    return device


if __name__ == "__main__":
    import matplotlib.pyplot as plt
    
    dir_in = r"/home/work/codebook_train_data/"
#     data = pd.read_csv(dir_in + 'sf_taylor.csv')
#     df = data.iloc[:, 0:16]  # get low 16 values
#     print(df.dtypes)
#     print(type(data))
#     np_data = df.values
    
    data = pd.read_csv(dir_in + 'Mall_Customers.csv')
    np_data = data.iloc[1 : 6, [3, 4]].values
    
    device = choose_device(True)
    matrix = torch.from_numpy(np_data).to(device)
    matrix = matrix.float()
#     matrix = torch.rand((10000, 10)).to(device)
    
    sf_kmeans(matrix, device)
```



## 版本3：利用Pytorch的kmeans包实现，GPU上跑

------

调用Pytorch现成的kmeans包，进行修改。

- 好处：
  - 能解决迭代速度问题
  - 达到与sklearn相同的精度结果

- `package name`：kmeans-pytorch
- 相关资料：[ref1](https://www.kaggle.com/himanigulati/pytorch-kmeans)
- 相关资料：[ref2](https://github.com/subhadarship/kmeans_pytorch)
- 以下代码含画图及对比


```python
!pip install kmeans-pytorch
```


```python
import torch
import numpy as np
import time
# from tqdm import tqdm
import pandas as pd
# from kmeans_pytorch import kmeans 
from module_pytorch_kmeans import kmeans
import matplotlib.pyplot as plt

def choose_device(cuda=False):
    if cuda:
        device = torch.device("cuda:0")
    else:
        device = torch.device("cpu")
    return device


def sf_kmeans(matrix,device, dims):
    max_range = 40
    wcss = []
    gpu_speeds = []

#     print(matrix.shape)
#     print(matrix)
#     print('\n')
    
    # data
#     data_size, dims, num_clusters = 1000, 2, 3
#     x = np.random.randn(data_size, dims) / 6
#     x = torch.from_numpy(x)

    
    for n_clusters in range(2, max_range + 1):
        a = time.time()
        
        # kmeans
        cluster_ids_x, cluster_centers, iters = kmeans(
        X=matrix, num_clusters=n_clusters, distance='euclidean', tqdm_flag=False, device=torch.device('cuda:0')
        )
#         iter_limit=500, 

#         print(cluster_ids_x)
#         print(cluster_centers)
#         print('\n')
        dists = torch.empty((0, dims)).to(device)
        for i, sample in enumerate(matrix):
            # 0按行追加扩展， 1按列追加扩展
            id = cluster_ids_x[i]
            dist = torch.mul(sample.to(device) - cluster_centers[id].to(device), sample - cluster_centers[id].to(device))
            dists = torch.cat([dists, dist.unsqueeze(0)], (0))
        
        print(torch.sum(dists))
#         print('\r{}'.format(torch.sum(dists)), end='')
        wcss.append(torch.sum(dists))
        
        b = time.time()
        speed = (b - a) / iters
        gpu_speeds.append(speed)
#         print('\n')

#     print(wcss)
#     print(gpu_speeds)
        
#     plt.figure()
    plt.grid(linestyle='-.')
    plt.title('Elbow Method')
    plt.xlabel('Number of clusters')
    plt.ylabel('WCSS')
    plt.plot(range(max_range - 1), wcss)
    plt.show()
    
    plt.figure()
    l2, = plt.plot(range(max_range - 1), gpu_speeds, color='g',label = "GPU")
    plt.xlabel("num_features")
    plt.ylabel("speed(s/iter)")
    plt.title("Speed with cuda")
    plt.legend(handles = [l2], labels = ['GPU'], loc='best')


if __name__ == "__main__":
    
    
    dir_in = r"/home/work/codebook_train_data/"
    data = pd.read_csv(dir_in + 'sf_large.csv')
    dims = 8

    df = data.iloc[:, 0:dims]  # get low 16 values
#     print(df.dtypes)
#     print(type(data))
    np_data = df.values
    
#     data = pd.read_csv(dir_in + 'Mall_Customers.csv')
#     np_data = data.iloc[1 : 6, [3, 4]].values
    
    device = choose_device(True)
    matrix = torch.from_numpy(np_data).to(device)
    matrix = matrix.float()
    
#     matrix = torch.rand((10000, 10)).to(device)
    
    sf_kmeans(matrix, device, dims)
```

输出提示如下：
    ....
    tensor(3.3288e+08, device='cuda:0')
    tensor(2.3872e+08, device='cuda:0')
    tensor(1.9115e+08, device='cuda:0')
    tensor(1.6357e+08, device='cuda:0')
    tensor(1.4616e+08, device='cuda:0')
    ....

![在这里插入图片描述](https://img-blog.csdnimg.cn/a1a6fc7da79c45edaf7d912b6fc0000e.PNG)

## 相关资料

----

1. [K-means Clustering Python Example](https://towardsdatascience.com/machine-learning-algorithms-part-9-k-means-example-in-python-f2ad05ed5203)
2. [K-means(K均值）](https://blog.csdn.net/u012328159/article/details/51377896)
3. [scikit-learn之kmeans应用及问题](https://blog.csdn.net/u012328159/article/details/86554833)
4. [用scikit-learn学习K-Means聚类](https://www.cnblogs.com/pinard/p/6169370.html)