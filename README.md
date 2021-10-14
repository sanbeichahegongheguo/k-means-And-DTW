# k-meansAndDTW.
Clustering and fitting of time series based on DTW and k-means

一、问题分析
1、首先尝试了使用：提取时间序列的统计学特征值，例如最大值，最小值等。然后利目前常用的算法根据提取的特征进行分类，例如Naive Bayes, SVMs，KNN 等。发现效果并不是很好。
2、尝试基于K-means的无监督形式分类，这种分类方式基于两个数据的距离进行分类，这样要定义号距离的概念，后来查阅资料，考虑使用动态时间规整（Dynamic Time Warping, DTW）。

二、数据处理
给出的数据较为完整，就一个excel表格，做了以下简单的排序，原始数据可见文末github地址。

三、代码实现
3.1 动态时间规整（Dynamic Time Warping, DTW）

如果是欧拉距离：则ts3比ts2更接近ts1，但是肉眼看并非如此。故引出DTW距离。
        动态时间规整算法，故名思议，就是把两个代表同一个类型的事物的不同长度序列进行时间上的“对齐”。比如DTW最常用的地方，语音识别中，同一个字母，由不同人发音，长短肯定不一样，把声音记录下来以后，它的信号肯定是很相似的，只是在时间上不太对整齐而已。所以我们需要用一个函数拉长或者缩短其中一个信号，使得它们之间的误差达到最小。下面这篇博文给了比较好的解释：https://blog.csdn.net/lin_limin/article/details/81241058。 简单英文解释如下（简而言之：就是允许错开求差值，并且取最小的那个作为距离。）

3.2  LB_Keogh距离
主要思想是在搜索数据很大的时候， 逐个用DTW算法比较每一条是否匹配非常耗时。那我们能不能使用一种计算较快的近似方法计算LB， 通过LB处理掉大部分不可能是最优匹配序列的序列，对于剩下的序列在使用DTW逐个比较呢？英文解释如下：
  3.3  使用k-means算法实现聚类

四、结果
定义了分成两类的情形，可以根据num_clust 的值进行灵活的调整，等于2是的分类和图示情况如下：

WBC01：[6774, 7193, 8070, 8108, 8195, 2020006799, 2020007003, 2020007251, 2020007420, 2020007636, 2020007718, 2020007928, 2020007934, 2020008022, 2020008196, 2020008239, 2020008302, 2020008354, 2020008418, 2020008513, 2020008535, 2020008737, 2020008890, 2020008909, 2020009042, 2020009043, 2020009050, 2020009201, 2020009213, 2020009289, 2020009420, 2020009557]

WBC02：[2020007250, 2020007388, 2020007389, 2020007422, 2020007625, 2020007703, 2020007927, 2020009049, 2020009158, 2020009284, 2020009580]

说明：
代码训练过程中，一定要注意数据类型，比如matrix和ndarray,虽然打印的时候都是（45，30），但是再训练的时候，稍加不注意，就会导致乱七八糟的问题，需要打印排查好久。
本文的数据和代码，请登录：my github ,进行下载。若是对您有用，请不吝给颗星。
具体请看博文：https://www.cnblogs.com/yifanrensheng/p/12501238.html