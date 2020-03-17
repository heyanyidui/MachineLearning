## criterion

这里主要介绍 **信息熵** 、**条件熵**、**信息熵增益**、**信息增益率** 的计算方法。

|  NO  | Outlook  | Temperature | Humidity |  Wind  | Play |
| :--: | :------: | :---------: | :------: | :----: | :--: |
|  1   |  Sunny   |     Hot     |   High   |  Weak  |  No  |
|  2   |  Sunny   |     Hot     |   High   | Strong |  No  |
|  3   | Overcast |     Hot     |   High   |  Weak  | Yes  |
|  4   |   Rain   |    Mild     |   High   |  Weak  | Yes  |
|  5   |   Rain   |    Cool     |  Normal  |  Weak  | Yes  |
|  6   |   Rain   |    Cool     |  Normal  | Strong |  No  |
|  7   | Overcast |    Cool     |  Normal  | Strong | Yes  |
|  8   |  Sunny   |    Mild     |   High   |  Weak  |  No  |
|  9   |  Sunny   |    Cool     |  Normal  |  Weak  | Yes  |
|  10  |   Rain   |    Mild     |  Normal  |  Weak  | Yes  |
|  11  |  Sunny   |    Mild     |  Normal  | Strong | Yes  |
|  12  | Overcast |    Mild     |   High   | Strong | Yes  |
|  13  | Overcast |     Hot     |  Normal  |  Weak  | Yes  |
|  14  |   Rain   |    Mild     |   High   | Strong |  No  |

### 信息熵

现在计算 **Play** 标注的信息熵S，根据熵的计算公式：
$$
H(S) = -\sum p_ilog_2(p_i)  \\ 共两种情况,其中 p_0 = 9/14; p_1 = 5/14
$$
**Play** 14次数据中，**Yes: 9；No: 5** 得
$$
H(Play) = -(9/14)*log_2(9/14)-(5/14)*log_2(5/14)
$$
约等于 **0.94**

### 条件熵

> [百度百科]([https://baike.baidu.com/item/%E6%9D%A1%E4%BB%B6%E7%86%B5/24479163?fr=aladdin](https://baike.baidu.com/item/条件熵/24479163?fr=aladdin))

𝐻(X|Y)定义为在给定条件 𝑌 下，X 的条件概率分布的熵对 Y 的数学期望。

条件熵的计算公式如下：
$$
\begin{aligned}
H(X\mid Y)=\sum_{y}P(y)H(X|Y=y)

\end{aligned}
$$
比如说在接下来的例子中计算 ***Wind*** 的熵增益，代入公式中的 X = Play，Y = Wind，由于Play有两种情况，所以
$$
H(Play\mid Wind)=P(Wind=Strong)H(Play|Wind=Strong)\\+P(Wind=Weak)H(Play|Wind=Weak)
$$
 其中，

* Wind=Strong共6次；Wind=Weak共8次；
* Wind=Strong而且
  * Play = Yes 共3次
  * Play = No 共3次
* Wind=Weak而且
  * Play = Yes 共6次
  * Play = No 共2次

所以，
$$
H(Play\mid Wind)
= \frac{6}{14}*(-\frac{1}{2}log_2\frac{1}{2}-\frac{1}{2}log_2\frac{1}{2})+\frac{8}{14}*(-\frac{3}{4}log_2\frac{3}{4}-\frac{1}{4}log_2\frac{1}{4})\\
$$
可以计算得 **0.892**

### 信息增益-ID3

> [百度百科]([https://baike.baidu.com/item/%E4%BF%A1%E6%81%AF%E5%A2%9E%E7%9B%8A/8864911?fr=aladdin](https://baike.baidu.com/item/信息增益/8864911?fr=aladdin))

信息增益 $I(X, Y) $ 反映了Y条件（特征）对X的影响。信息增益越大，说明这个特征对结果的影响越大，在决策树中应该被优先考虑。公式如下：
$$
I(X,Y) = H(X) - H(X\mid Y) = H(Y) - H(Y\mid X)
$$
现在以切分 ***Wind*** 为例，其信息熵增益如下:
$$
Gain(Wind) = H(Play) - H(Play\mid Wind)
$$
计算得 **0.048**

**类似地**，可以计算得 $Gain(Temperature)=0.151$ $Gain(Outlook)=0.247$ $Gain(Temperature)=0.029$

**结果分析**

根据以上的计算结果，可以发现Outlook的熵增益最大，所以在决策树种应该第一个切分。

### 信息增益率-C4.5

即a的信息增益除以a的熵。
$$
GainRatio(D,a) = \frac{I(D,a)}{H(a)}
$$

### 基尼系数(不纯度)

$$
G = 1 - \sum_{i=1}^k p_i^2
$$

同样以上面数据为例，计算 Humidity的不纯度，首先分别计算G(High)与G(Normal)，然后再求整体均值：

High 总共7次，其中有3次Play=Yes，4次Play=No，所以
$$
G(High)=1-((\frac{3}{7})^2+(\frac{4}{7})^2)=0.490
$$
类似地，
$$
G(Normal)=1-((\frac{1}{7})^2+(\frac{6}{7})^2) = 0.245
$$
从而计算G(Humidity),
$$
G(Humidity)=\frac{7}{14}*G(High)+\frac{7}{14}*G(Normal)=0.368
$$
同样地计算其他特征的不纯度，然后找到不纯度最低的特征，作为当前切分的特征。



> Smileyan
>
> 2020.3.16 15:58

