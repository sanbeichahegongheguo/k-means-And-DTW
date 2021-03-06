# 项目介绍

![Alt](https://repobeats.axiom.co/api/embed/1b81d2577f36a09f53a1f9216390ff64eeed8116.svg "Repobeats analytics image")



## 涉及的问题

1、当面对带有时间序列的多组数据进行聚类问题时。

2、首先尝试：提取时间序列的统计学特征值，例如最大值，最小值等。

然后利目前常用的算法根据提取的特征进行分类，例如`Naive Bayes`, `SVMs`，`KNN` 等。

但是效果并不好。

3、然后可以尝试基于`K-means`的无监督形式分类。

这种分类方式基于两个数据的距离进行分类。

需要先定义好`距离`的概念，因为是时间序列数据，考虑使用动态时间规整（Dynamic Time Warping，`DTW`）。

这部分介绍见文件夹`doc`里的原理说明文档。

本代码库，主要讲解聚类算法，包括`kmean`、 `DTW`、 `NMF`、 `infomap`等算法。

## 无监督学习算法总结

本文仅对常见的无监督学习算法进行了简单讲述。

其他的如自动编码器、受限玻尔兹曼机、神经网络用于无监督学习等暂未包括。

虽然整体上分为了聚类和降维两大类，但实际上这两类并非完全正交，很多地方可以相互转化，还有一些变种的算法既有聚类功能又有降维功能，一些新出现的和尚在开发创造中的无监督学习算法正在打破聚类和降维的类别划分。

### 一、聚类(clustering)

#### 1、k-均值聚类(k-means)

这是机器学习领域除了线性回归最简单的算法了。

该算法用来对n维空间内的点根据欧式距离远近程度进行分类。

算法工作原理摘要：

```python
# 簇数为k
# 数据空间维度为n
# 训练集元素数为m

def K_means_demo(k,n,m):
    clusters = np.random.randint(0, 40, size=[k, n]) # 随机生成聚类中心
    tr_set = np.random.randint(0, 40, size=[m, n]) # 因为是模拟，所以自己随机生成的数据集for iter in range(0,5):
        clu_asist = np.zeros(shape=[k,n],dtype=int)
        for i in range(0,m):  # 遍历训练集内每个样本
            min = 9999999
            owner = 0
            for j in range(0,k): # 遍历所有聚心找到最近的聚心owner
                dis = 0
                for p in range(0,n):
                    abso = tr_set[i][p] - clusters[j][p]
                    dis += abso * abso  # dis为第i个元素和第j个聚心的欧式距离的平方
                if dis - min < 0:
                    min = dis
                    owner = j
            for p in range(0, n):    # 渐进更新均值
                clu_asist[owner][p] += (tr_set[i][p] - clu_asist[owner][p]) // (p+1)
        clusters=clu_asist　　
    return clusters
```

手动设定了迭代更新次数为5，因为我做的demo规模比较小，迭代几次便收敛了，而在实际使用中一般用( 迭代次数 || EarlyStop )作为迭代终止条件。

动画演示：

![](https://img2018.cnblogs.com/blog/1705789/201910/1705789-20191004105937461-476057557.gif)

通读本算法，可以发现`k-means`对聚心初始值非常敏感，如果初始情况不好会震荡的。这里可以采取一些措施预判聚心大致要在哪个位置，然后直接将其初始化。

另外，关于收敛的判断，可以采取多种方法。比如使用代价函数，或者`F-Measure`和信息熵方法。

K-means优缺点分析：

- 优点： 算法简单易实现；

- 缺点： 需要用户事先指定类簇个数； 聚类结果对初始类簇中心的选取较为敏感； 容易陷入局部最优； 只能发现球形类簇。

#### 2、层次聚类(Hierarchical Clustering)

层次聚类就是一层一层地进行聚类。既可以由下向上对小的类别进行聚合（凝聚法），也可以由上向下对大的类别进行分割（分裂法）。在应用中，使用较多的是凝聚法。

凝聚法：

先将每个样本当做一个类簇，然后依据某种规则合并这些初始的类簇，直到达到某种条件或者减少到设定的簇数。

在算法迭代中每次均选取类簇距离最小的两个类簇进行合并。关于类簇距离的计算表示方法主要有以下几种：

（1）取两个类中距离最小的两个样本的距离作为两个集合的距离

（2）取两个类中距离最大的两个样本的距离作为两个集合的距离

（3）计算两个集合中每两两点的距离并取平均值，这种方法要略费时

（4）比（3）轻松一些，取这些两两点距的中位数

（5）求每个集合中心点，然后以中心点代表集合来计算集合距离

（6）......

迭代会在簇数减少到设定数量时结束，当然，如果设定了阈值f，那么当存在两个距离小于f的集合时则会继续迭代直到不存在这样的两个集合。

分裂法：

首先将所有样本归类到一个簇，然后依据某种规则逐渐分裂，直到达到某种条件或者增加到设定的簇数。

层次聚类和K-means作比较：

（1）K-means时间复杂度为O(N)，而层次聚类时间复杂度为O(N^2)，所以分层聚类不能很好地处理大批量数据，而k-means可以。

（2）K-means不允许嘈杂数据，而层次聚类可以直接使用嘈杂数据集进行聚类

（3）当聚类形状为超球形（如2D圆形，3D球形）时，k-means聚类效果更好。

#### 3、基于密度聚类Mean Shift

mean shift这种基于核函数估计的爬山算法不仅可以用于聚类，也可用于图像分割与目标跟踪等。这个概念早在1975年就被Fukunaga等人提出，而后1998年Bradski将其用于人脸跟踪则使得其优势大大体现出来。我们这里只谈论作为聚类算法的mean shift。

##### 什么是漂移向量？

给定n维空间内数据点集X与中心点x，并以D表示数据集中与中心点x距离小于半径h的点的集合，则漂移向量Mh表示为： Mh =Exi∈D[xi-x]  。

##### 什么是漂移操作？

计算得到漂移向量后将中心位置更新一下，使得中心位置始终处于力的平衡位置。更新公式为： x ← x + Mh   。

另外，mean shift用于聚类时一般不使用核函数，如果用了核函数，权重改变，就不是“均值”漂移了。

均值飘移算法实现过程：

1.在未被标记的点中随机选取一个点作为起始中心点center；

2.找出以center为中心半径为h的空间内所有的点，记作集合D，认为这些点归属于类簇c。同时将这些点属于这个类的概率加1，这个参数将用于最后步骤的分类；

3.计算D内数据点与中心点center的漂移向量Mh ；

4.进行漂移操作x ← x + Mh  ；

5.重复步骤2.3.4直到迭代收敛，记下此时的center位置。在这一过程中遇到的点都归类到簇c；

6.如果收敛时当前簇c的center与其它已存在的簇c‘中心的距离小于阈值，则合并c和c'。否则，把c作为新的聚类，增加1类；

7.重复步骤1-6直到所有的数据点都被标记访问；

8.分类：根据每个类对每个点的访问频率，取频率最大的类作为当前点集的所属类。

shift mean跟k-means作比较，两者都用集合内点的均值进行中心点移动，不同的是shift mean可以自行决定类簇数。

#### 4、基于密度聚类DBSCAN

DBSCAN：“深度学习的神经网络，比你们用了几十年的k-means不知道高到哪里去了，我跟他谈笑风生。”  （手动滑稽）DBSCAN可能是聚类领域最迷的算法了，它可以发现任何形状的簇，而且实现简单易懂。至于是谁首先提出的我也不晓得了，就不给各位普及历史了emmm，以下是一段抄来的介绍：DBSCAN（Density-Based Spatial Clustering of Applications with Noise，具有噪声的基于密度的聚类方法）是一种基于密度的空间聚类算法。 该算法将具有足够密度的区域划分为簇，并在具有噪声的空间数据库中发现任意形状的簇，它将簇定义为密度相连的点的最大集合。

\<END>
