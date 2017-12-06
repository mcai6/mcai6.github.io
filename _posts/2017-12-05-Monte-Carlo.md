---
layout:     post   				    # 使用的布局（不需要改）
title:      1.4 Monte Carlo Simulation 				# 标题 
subtitle:    #副标题
date:       2017-12-05				# 时间
author:     Manlin 						# 作者
header-img: img/post-bg-coffee.jpeg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
- statistic methods
---


```python
import numpy as np
import math
import matplotlib.pyplot as plt
```

## Wiener Process

Generalized Wiener Process said, stock price change in a small time frame is like this: 

dx = a * dt + b * dz 

a * dt is drift rate, b * dz is noise


```python
def WienerProcess(a,b,steps):
    deltaT = 1./steps
    x = []
    for step in range(steps):
        deltaX = a*deltaT + b* np.random.normal (0,1)*math.sqrt(deltaT)
        x.append(deltaX)
    X = np.cumsum(x)
    return X
```


```python
A = WienerProcess(1.5,0,250)
B = WienerProcess(0,1,250)
plt.subplot(1,2,1)
plt.plot(A,label = 'a*dt')
plt.plot(B,label = 'b*dz')
plt.ylim((-1.5, 2))
plt.legend()
plt.subplot(1,2,2)
plt.plot(A+B,label = 'a*dt+b*dz')
plt.ylim((-1.5, 2))
plt.legend()
plt.show()
```


![](https://ws4.sinaimg.cn/large/006tNc79gy1fm6xnefm9hj30n20dewff.jpg)


## Ito Process
From Wiener, a and b are just constant.. Ito process is more complicated, in which ***a*** and ***b*** vary with price x and time t.

Now, dx = a(x,t)dt+b(x,t)dz

## Monte Carlo simulation

Let's try a very simple simulation: initial price 100, 250 trading days per annum, 15% expected return per annum, and 30% volatility, 50 iteration simulation


```python
steps = 250  #suppose we have 250 trading day per year
deltaT = 1./250
mu = 0.15 #annual return 15%
sigma = 0.3 #volatility
iteration = 50 
S = 100 * np.ones((1,iteration))

for step in range(steps):
    e = np.random.normal (0,1,(1, iteration))  #mean = 0, std = 1
    deltaS = mu * deltaT * S[-1] + sigma * math.sqrt(deltaT) * e * S[-1]
    St = S[-1] + deltaS
    S = np.vstack((S,St))
print(S)

```


```python
for i in range(iteration):
    plt.plot(S[:,i])
```


![](https://ws3.sinaimg.cn/large/006tNc79gy1fm6xnxagz0j30mz0dedol.jpg)


### WOW....
