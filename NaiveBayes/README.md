## 朴素贝叶斯算法 (Naive Bayes)

> https://scikit-learn.org/stable/modules/naive_bayes.html

### 数学基础

首先从贝叶斯公式说起，假设 x<sub>1</sub>,x<sub>2</sub>,...,x<sub>n</sub> 为输入数据，可以理解为 feature<sub>1</sub> = x<sub>1</sub> , feature<sub>2</sub> = x<sub>2</sub>, ...,feature<sub>n</sub> = x<sub>n</sub>时，求 label = y 的概率，
$$
P(A \mid B) = \frac{P(A) P(B \mid A)}
                                 {P(B)}
$$
代入式子，得
$$
P(y \mid x_1, \dots, x_n) = \frac{P(y) P(x_1, \dots x_n \mid y)}
                                 {P(x_1, \dots, x_n)}
$$
之所有称之为 *朴素*(Naive)  贝叶斯，是因为接下来的算法是基于一条重要假设而进行的：假设所有事件是相互独立的。在这种假设下可以得到公式：
$$
{P(x_1, \dots, x_n)} = P(x_1)P(x_2)\dots P(X_n)
$$
则
$$
{P(x_1, \dots, x_n\mid y)} = P(x_1\mid y)P(x_2\mid y)\dots P(X_n\mid y)
$$
所以原式可以化为
$$
P(y \mid x_1, \dots, x_n) = \frac{P(y) \prod_{i=1}^{n} P(x_i \mid y)}
                                 {P(x_1, \dots, x_n)}
$$
因为P(x<sub>1</sub>,x<sub>2</sub>,...,x<sub>n</sub>) 是输入的常量，所以
$$
P(y \mid x_1, \dots, x_n) \propto P(y) \prod_{i=1}^{n} P(x_i \mid y)\\\Downarrow\\\hat{y} = \arg\max_y P(y) \prod_{i=1}^{n} P(x_i \mid y)
$$

我们可以用最大后验概率来评估 ***P(y)*** 和 ***P(x<sub>i</sub>|y)***，其中 ***P(y)*** 与训练集中 ***y*** 类型频率有关。

其中的 ***P(y)*** 即先验概率，而 ***P(x<sub>i</sub>|y)***  即条件概率。

多种朴素贝叶斯分类算法的不同之处主要在于它们对 ***P(x<sub>i</sub>|y)*** 分布的假设上。

### 高斯朴素贝叶斯算法(Gaussian Naive Bayes)

该算法假设 ***P(x<sub>i</sub>|y)*** 为高斯分布，根据高斯分布的公式， ***P(x<sub>i</sub>|y)*** 的计算如下：
$$
P(x_i \mid y) = \frac{1}{\sqrt{2\pi\sigma^2_y}} \exp\left(-\frac{(x_i - \mu_y)^2}{2\sigma^2_y}\right) \\
\mu_y 与 \sigma_y 是极大似然估计的参数
$$

#### GaussianNB 参数

* **priors: ** **array-like, shape (n_classes)** default None

  即指定先验概率，指定后模型将不会再根据数据计算先验概率。

* **var_smoothing**： ***float***

  指定平滑处理时所有特征出现次数统计量增加数目。

### 多项式朴素贝叶斯算法(Multinomial Naive Bayes)

该算法假设 ***P(x<sub>i</sub>|y)*** 为多项分布，是文本分类(text classification) 中两个经典的朴素贝叶斯演化算法之一。这个分布是对每一种类型 ***y*** 通过向量
$$
\theta_y = (\theta_{y1},\ldots,\theta_{yn}) \\
其中\theta_y 参数由最大似然的平滑处理(如拉普拉斯平滑)估计，即相对频率计数：\\
\hat{\theta}_{yi} = \frac{ N_{yi} + \alpha}{N_y + \alpha n}\\
其中 N_{yi} = \sum_{x \in T} x_i 是训练集中y类样本中i特征出现的次数，\\
N_{y} = \sum_{i=1}^{n} N_{yi} 是训练集中y类所有特征出现次数总和，\\
\alpha \ge 0，用于解决训练集中不含某些特征的问题和防止在以后计算中出现0概率的问题。\\
当\alpha = 1时称为拉普拉斯(Laplace)平滑;当\alpha < 1时，称为Lidstone平滑。
$$

### 小结

朴素贝叶斯算法是基于贝叶斯公式并假设所有条件是相互独立的而推导出来的，但是条件概率 ***P(x<sub>i</sub>|y)*** 的似然估计的方法是开放的，多种朴素贝叶斯算法的不同也正是似然估计方法不同。根据sklearn官网介绍，朴素贝叶斯还有另外三种算法，这里并未全部列举。



> Smileyan
>
> 2020年3月14日 13:52