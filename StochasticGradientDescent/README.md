## 随机梯度下降算法 Stochastic Gradient Descent

***SGD*** 是一个简单但非常高效的机器学习方法，

```python
import numpy as np
import pandas as pd
import os
import time

def sigmoid(z):
    return 1 / (1 + np.exp(-z))
def model(X, theta):
    return sigmoid(np.dot(X, theta.T))
def cost(X, y, theta):
    left = np.multiply(-y, np.log(model(X, theta)))
    right = np.multiply(1 - y, np.log(1 - model(X, theta)))
    return np.sum(left - right) / (len(X))
def gradient(X, y, theta):
    grad = np.zeros(theta.shape)
    error = (model(X, theta)- y).ravel()
    for j in range(len(theta.ravel())): #for each parmeter
        term = np.multiply(error, X[:,j])
        grad[0, j] = np.sum(term) / len(X)    
    return grad
STOP_ITER = 0
STOP_COST = 1
STOP_GRAD = 2

def stopCriterion(type, value, threshold):
    #设定三种不同的停止策略
    if type == STOP_ITER:        return value > threshold
    elif type == STOP_COST:      return abs(value[-1]-value[-2]) < threshold
    elif type == STOP_GRAD:      return np.linalg.norm(value) < threshold
#洗牌
def shuffleData(data):
    np.random.shuffle(data)
    cols = data.shape[1]
    X = data[:, 0:cols-1]
    y = data[:, cols-1:]
    return X, y


def descent(data, theta, batchSize, stopType, thresh, alpha):
    #梯度下降求解
    
    init_time = time.time()
    i = 0 # 迭代次数
    k = 0 # batch
    X, y = shuffleData(data)
    grad = np.zeros(theta.shape) # 计算的梯度
    costs = [cost(X, y, theta)] # 损失值

    
    while True:
        grad = gradient(X[k:k+batchSize], y[k:k+batchSize], theta)
        k += batchSize #取batch数量个数据
        if k >= n: 
            k = 0 
            X, y = shuffleData(data) #重新洗牌
        theta = theta - alpha*grad # 参数更新
        costs.append(cost(X, y, theta)) # 计算新的损失
        i += 1 

        if stopType == STOP_ITER:       value = i
        elif stopType == STOP_COST:     value = costs
        elif stopType == STOP_GRAD:     value = grad
        if stopCriterion(stopType, value, thresh): break
    return theta, i-1, costs, grad, time.time() - init_time

def runExpe(data, theta, batchSize, stopType, thresh, alpha):
    #import pdb; pdb.set_trace();
    theta, iter, costs, grad, dur = descent(data, theta, batchSize, stopType, thresh, alpha)
    print(theta, iter, costs, grad, dur)
#     name = "Original" if (data[:,1]>2).sum() > 1 else "Scaled"
#     name += " data - learning rate: {} - ".format(alpha)
    
#     if batchSize==n: strDescType = "Gradient"
#     elif batchSize==1:  strDescType = "Stochastic"
#     else: strDescType = "Mini-batch ({})".format(batchSize)
#     name += strDescType + " descent - Stop: "
    
#     if stopType == STOP_ITER: strStop = "{} iterations".format(thresh)
#     elif stopType == STOP_COST: strStop = "costs change < {}".format(thresh)
#     else: strStop = "gradient norm < {}".format(thresh)
        
#     name += strStop

#     print ("***{}\nTheta: {} - Iter: {} - Last cost: {:03.2f} - Duration: {:03.2f}s".format(
#         name, theta, iter, costs[-1], dur))

#     fig, ax = plt.subplots(figsize=(12,4))
#     ax.plot(np.arange(len(costs)), costs, 'r')
#     ax.set_xlabel('Iterations')
#     ax.set_ylabel('Cost')
#     ax.set_title(name.upper() + ' - Error vs. Iteration')
    return theta


# 预测
def predict(X, theta):
    return [1 if x >= 0.5 else 0 for x in model(X, theta)]


path = 'data' + os.sep + 'LogiReg_data.txt'
pdData = pd.read_csv(path, header=None, names=['Exam 1', 'Exam 2', 'Admitted'])

pdData.insert(0, 'Ones', 1) 

# set X (training data) and y (target variable)
# orig_data = pdData.values # convert the Pandas representation of the data to an array useful for further computations
# cols = orig_data.shape[1]
# X = orig_data[:,0:cols-1]
# y = orig_data[:,cols-1:cols]

theta = np.zeros([1, 3])


#选择的梯度下降方法是基于所有样本的
n=100
theta = runExpe(orig_data, theta, n, STOP_COST, thresh=0.0004, alpha=0.000001)
theta
```

```python
import numpy as np
import pandas as pd
import os
import time

def sigmoid(z):
    return 1 / (1 + np.exp(-z))

def model(X, theta):
    return sigmoid(np.dot(X, theta.T))
def cost(X, y, theta):
    left = np.multiply(-y, np.log(model(X, theta)))
    right = np.multiply(1 - y, np.log(1 - model(X, theta)))
    return np.sum(left - right) / (len(X))
def gradient(X, y, theta):
    grad = np.zeros(theta.shape)
    error = (model(X, theta)- y).ravel()
    for j in range(len(theta.ravel())): #for each parmeter
        term = np.multiply(error, X[:,j])
        grad[0, j] = np.sum(term) / len(X)    
    return grad
STOP_ITER = 0
STOP_COST = 1
STOP_GRAD = 2

def stopCriterion(type, value, threshold):
    #设定三种不同的停止策略
    if type == STOP_ITER:        return value > threshold
    elif type == STOP_COST:      return abs(value[-1]-value[-2]) < threshold
    elif type == STOP_GRAD:      return np.linalg.norm(value) < threshold
#洗牌
def shuffleData(data):
    np.random.shuffle(data)
    cols = data.shape[1]
    X = data[:, 0:cols-1]
    y = data[:, cols-1:]
    return X, y


def descent(data, theta, batchSize, stopType, thresh, alpha):
    #梯度下降求解
    
    init_time = time.time()
    i = 0 # 迭代次数
    k = 0 # batch
    X, y = shuffleData(data)
    grad = np.zeros(theta.shape) # 计算的梯度
    costs = [cost(X, y, theta)] # 损失值

    
    while True:
        grad = gradient(X[k:k+batchSize], y[k:k+batchSize], theta)
        k += batchSize #取batch数量个数据
        if k >= n: 
            k = 0 
            X, y = shuffleData(data) #重新洗牌
        theta = theta - alpha*grad # 参数更新
        costs.append(cost(X, y, theta)) # 计算新的损失
        i += 1 

        if stopType == STOP_ITER:       value = i
        elif stopType == STOP_COST:     value = costs
        elif stopType == STOP_GRAD:     value = grad
        if stopCriterion(stopType, value, thresh): break
    return theta, i-1, costs, grad, time.time() - init_time

def runExpe(data, theta, batchSize, stopType, thresh, alpha):
    #import pdb; pdb.set_trace();
    theta, iter, costs, grad, dur = descent(data, theta, batchSize, stopType, thresh, alpha)
#     print(theta, iter, costs, grad, dur)
    return theta

# 预测
def predict(X, theta):
    return [1 if x >= 0.5 else 0 for x in model(X, theta)]

path = 'data' + os.sep + 'LogiReg_data.txt'
pdData = pd.read_csv(path, header=None, names=['Exam 1', 'Exam 2', 'Admitted'])

pdData.insert(0, 'Ones', 1) 

orig_data = pdData.values # convert the Pandas representation of the data to an array useful for further computations

theta = np.zeros([1, 3])

#选择的梯度下降方法是基于所有样本的
n=100
theta = runExpe(orig_data, theta, n, STOP_COST, thresh=0.0000001, alpha=0.0000001)
theta
```

