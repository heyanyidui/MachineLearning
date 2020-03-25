## Decision Trees

> https://scikit-learn.org/stable/modules/tree.html

决策树(Decision Trees, DTs) 是一种无参数的监督学习算法，可以用来分类和回归。基本方法是从样本数据特征中学习简单的决策规则，然后根据这些规则创建模型来预测目标。

### 分类模型

决策树即模仿做决策的过程，一步步根据特征进行判段，从而达到分类的目的。

决策树算法能够解决二分类问题和多分类问题。为了确定建立决策树时所依据的特征的顺序，引入了 ***信息熵entropy*** 和 ***基尼系数gini*** 的概念。

#### 信息熵与基尼系数(不纯度)

关于 **信息熵** 、**条件熵**、**信息增益**、**信息增益率** 的概念与例子，[参考***entropy***](/DecisionTrees/criterion.md.md)

#### 常见问题

* 连续值切分：将连续值进行排序，排序后按照一定间隔进行切分。
* 规则用尽：
  * 规则用尽时也未能完成所有数据的切分，可以考虑进行投票，选择样本多的类型；
  * 也可以多次使用特征，多次切分。
* 过拟合：
  * 前剪枝，在构造决策树之前就设定好每个叶子节点最大样本数或最大深度；
  * 后剪枝，先构建好决策树，然后再根据决策树对一些样本值比较悬殊的枝叶进行修剪。

#### 参数说明

> [sklearn 官网文档](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html#sklearn.tree.DecisionTreeClassifier)

* **criterion**： ***{“gini”, “entropy”}, default=”gini”*** 即评测标准
* **splitter** : ***{“best”, “random”}, default=”best”***  即切分方法
* **max_depth**:  ***int, default=None*** 即决策树最大深度
* **random_state**: ***int*** 即随机种子
* **max_leaf_nodes** ***int, default=None*** 即最大叶子节点数目
* **class_weight** ***dict, list of dict or “balanced”, default=None*** 即各个分类的权重

### 优缺点

* 优点

  * 简单易懂，树的模型支持可视化。
  * 只需要少量的训练数据。但是不支持含有缺省值的数据。

  * 预测速度快。类似二叉树的查找算法时间复杂度，***log<sub>2</sub> N***
  * 能处理数值数据和分类数据。
  * 能处理多输出问题。
  * 使用白盒模型。
  * 可以使用统计测试来验证模型。
  * 即使它的假设与生成数据的真实模型有点冲突，也能很好地执行。

* 缺点

  * 容易过拟合。可能创建一个非常复杂但是并不能很好的概括数据特征的树。
  * 不稳定。可能因为数据极小的改变而导致生成模型差异很大。
  * 实际的决策树算法都是基于启发式算法，每个节点上进行局部最优决策，不能保证返回全局最优决策树。
  * 有些概念对决策树来说很难学习，因为决策树很难表达这些概念。
  * 当训练数据中某些类占主导地位时，该算法创建的决策树是有偏向的。因此，建议在拟合之前平衡一下数据集。

  