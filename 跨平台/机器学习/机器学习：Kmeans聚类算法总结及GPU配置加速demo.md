**Ŀ¼**
[toc]

���⣺����ѧϰ��Kmeans�����㷨�ܽἰGPU���ü���demo

## Kmeans�㷨����

-----

- **�㷨���**
	- ���㷨��һ��̰�Ĳ��ԣ���ʼ��ʱ���ѡȡN�����ģ����ϵ���������ΧԪ�ص����ĵ�����ۻ�����С���Ӷ��ҵ����Ļ���˵��Ӧ�Ĵء�

- **���Ĳ���**
  - �ȵõ�������Ĵ������ݣ���ά������
  - �������Եõ���ѵķ��������
  - ������Ѵ�������������������
  - �õ���Ѵػ��ֵ���ʸ��������Ƴɹ̶�˳��
  - ����õ��������ݶ�Ӧ��index������ʸ���������
  - ��ʸ�ļ�������뱾
- **����ָ��**
  - �������ƶȸߣ��������ൽĳһ�ص���������صľ����㹻С
  - �ؼ����ƶȵͣ���ÿ���صĲ��ϴ��ܱ���������Ϣ

����Python����ʵ���ܽ����£��ֱ�����CPU��GPU�ϡ�

## �汾1������sklearn��kmeans�㷨��CPU����

-------

- �ô�
  - ���ٵ��û���ѧϰ�⣬sklearn
  - �ʺϽ����뱾ѵ���ͼ򵥷�������

- ����
  - ���⵱��������ʱ�������ٶȽ���

- �ο����ӣ�[here](https://www.analyticsvidhya.com/blog/2021/01/in-depth-intuition-of-k-means-clustering-algorithm-in-machine-learning/)


```python
import module_kmeans
dir_in = r"/home/work/codebook_train_data/"
# module_kmeans.sf_kmeans(dir_in)
```

��Ӧ�ĵ��ýű��ļ���`sf_kmeans.py`���ڲ��������£�

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

## �汾2���������ϵ�kmeans�㷨ʵ�֣�GPU����

------

- �ô���
  - �ܽ�������ٶ�����
- ���ƣ�
  - ��֮�����������ǣ����㷨ʵ�־��Ƚϲ�
  - �޷��ﵽ�뱾��CPU�ܵ�sklearn��kmeans�㷨Ч��
- �ο����ӣ�[here](https://cloud.tencent.com/developer/article/1729690)

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
        # ���ѡ���ʼ���ĵ㣬�����������ٶȿ��Խ��sklearn�е�kmeans++��ʼ������
        init_row = torch.randint(0, x.shape[0], (self.n_clusters,)).to(self.device)
        init_points = x[init_row]
        self.centers = init_points
        while True:
            # ������
            self.nearest_center(x)
            # �������ĵ�
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
        # ���Ҿ������ĵ��������������Ϊ����Ĵ�������������ֱ��
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



## �汾3������Pytorch��kmeans��ʵ�֣�GPU����

------

����Pytorch�ֳɵ�kmeans���������޸ġ�

- �ô���
  - �ܽ�������ٶ�����
  - �ﵽ��sklearn��ͬ�ľ��Ƚ��

- `package name`��kmeans-pytorch
- ������ϣ�[ref1](https://www.kaggle.com/himanigulati/pytorch-kmeans)
- ������ϣ�[ref2](https://github.com/subhadarship/kmeans_pytorch)
- ���´��뺬��ͼ���Ա�


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
            # 0����׷����չ�� 1����׷����չ
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

�����ʾ���£�
    ....
    tensor(3.3288e+08, device='cuda:0')
    tensor(2.3872e+08, device='cuda:0')
    tensor(1.9115e+08, device='cuda:0')
    tensor(1.6357e+08, device='cuda:0')
    tensor(1.4616e+08, device='cuda:0')
    ....

![���������ͼƬ����](https://img-blog.csdnimg.cn/a1a6fc7da79c45edaf7d912b6fc0000e.PNG)

## �������

----

1. [K-means Clustering Python Example](https://towardsdatascience.com/machine-learning-algorithms-part-9-k-means-example-in-python-f2ad05ed5203)
2. [K-means(K��ֵ��](https://blog.csdn.net/u012328159/article/details/51377896)
3. [scikit-learn֮kmeansӦ�ü�����](https://blog.csdn.net/u012328159/article/details/86554833)
4. [��scikit-learnѧϰK-Means����](https://www.cnblogs.com/pinard/p/6169370.html)