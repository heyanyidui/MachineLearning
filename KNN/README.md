## KNN (K Nearest Neighbors)

> https://scikit-learn.org/stable/modules/neighbors.html

### 算法概述

首先确定参数K，找到距离待测点最近的K个训练样本数，并从中预测标签。

### 最近邻分类 (Nearest Neighbors Classification)

> https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html#sklearn.neighbors.KNeighborsClassifier

基于最近邻算法的分类是一种基于距离的机器学习算法，这个算法并没有生成真正的模型，而是简单地存储训练集的距离，然后通过统计待测点最近邻的每个点的类的个数，来决定待测点的分类。

### algorithm 参数

使用 KNeighborsClassifier时可以填写多个参数，比如n_neighbors即KNN算法中的K，具体参考官网教程，这里提到其中一个algorithm。这个参数是用来指定用来计算最近邻点使用的算法，包括以下几种：

* ball_tree：用于N点问题中的快速归纳。
* kd_tree：通过树形结构来快速找到该点附近的K个点。

* brute：通过穷举搜索找到邻近点。
* auto：将会通过fit方法中使用的数据找到最合适的算法。

### 优缺点

> [https://baike.baidu.com/item/%E9%82%BB%E8%BF%91%E7%AE%97%E6%B3%95/1151153?fr=aladdin](https://baike.baidu.com/item/邻近算法/1151153?fr=aladdin)

* 优点
  * 简单，易于理解，易于实现，无需估计参数，无需训练；
  * 适合对稀有事件进行分类；
  * 特别适合于多分类问题(multi-modal,对象具有多个类别标签)， kNN比SVM的表现要好。
* 缺点
  * 当样本不平衡时，有可能导致分类出错。
  * 计算量大。

### 小结

由于kNN方法主要靠周围有限的邻近的样本，而不是靠判别类域的方法来确定所属类别的，因此对于类域的交叉或重叠较多的待分样本集来说，kNN方法较其他方法更为适合。

> Smileyan
>
> 2020.3.12 22:47

