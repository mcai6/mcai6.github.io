---
layout:     post   				    # 使用的布局（不需要改）
title:      1.3 Dynamic Hedge Ratio by Kalman Filter 				# 标题 
subtitle:    #副标题
date:       2017-11-26				# 时间
author:     Manlin 						# 作者
header-img: img/post-bg-coffee.jpeg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
- statistic methods
---
From the last post we know how to use cointegration and ADF to find a pair of assets, and from there we should be able to build a spread which is stationary. But actually it's kind of impossible that the spread is alwasy stable when we use a same hedge ratio. It should be better if this hedge ratio can vary with time.

1) **Moving Average:** The most common way people are using is linear regression with a proper rolling look-back window size. i.e. using 100 history data and OLS to find the rolling average.

2) **EMA:** It's better cause it weighted more on the most recent data. But not clear why exponential is optimal

3) **Kalman Filter!!!**  I'm not gonna to tell you the math behind. For more detail, I'd really recommend [**Algothmic Trading**](https://www.amazon.com/Algorithmic-Trading-Winning-Strategies-Rationale/dp/1118460146/ref=sr_1_1?ie=UTF8&qid=1511660151&sr=8-1&keywords=algorithmic+trading) page 77-84 

Now we can simply follow these 8 steps

* R is the covariance matrix of 𝛽(t) and 𝛽(t-1).

* Q is the variance of y and expected y^

* V_w and V-e are error

Given the  𝛽^(1)=0 and R(0)=1
![](https://ws4.sinaimg.cn/large/006tNc79gy1flvjheme23j30f707tt9v.jpg)

Then for every day or minute or what ever time frequency, just do the loop and keep updating the hedge ratio  𝛽(t)

But I like the auther of [**Algothmic Trading**](https://www.amazon.com/Algorithmic-Trading-Winning-Strategies-Rationale/dp/1118460146/ref=sr_1_1?ie=UTF8&qid=1511660151&sr=8-1&keywords=algorithmic+trading) said about quantitative trader: *As traders, we don't need to know how to derive the relationships between these quantities--we only need to know where to find a good software package that gives us the right answer.'*  We now have Kalman filter from pykalman library. See examples: [*Quantopian Lecture*](https://www.quantopian.com/lectures/kalman-filters) 



```python
from quantopian.research.experimental import continuous_future, history
import matplotlib.pyplot as plt
import numpy as np
import statsmodels.tsa.stattools as ts 
from pykalman import KalmanFilter
import statsmodels.api as sm
```

## AAPL & MSFT


```python
y = get_pricing('AAPL',fields = 'price',start_date = '2016-01-01',end_date = '2017-01-01')
x = get_pricing('MSFT',fields = 'price',start_date = '2016-01-01',end_date = '2017-01-01')
y.plot()
x.plot()
plt.ylabel('Price')
plt.legend(['AAPL', 'MSFT'])
```







![](https://ws2.sinaimg.cn/large/006tNc79gy1flvjm72lk3j30n60csjsl.jpg)


## Calculate Beta by OLS and plot spread


```python
X = sm.add_constant(x)
Y = y
model= sm.OLS(Y,X).fit()
k = model.params[1]
plt.plot(y-k*x)
print 'ADF test:', ts.adfuller(y-k*x)[0]
```

    ADF test: -2.56698238336



![](https://ws2.sinaimg.cn/large/006tNc79gy1flvjmhky7xj30mm0deab2.jpg)


ADF test result just -2.567, means that's not that stable. spread also kind of wide, between 0 to 20

## Calculate by Kalman filter and plot dynamic beta


```python
delta = 1e-3   ### from Ernest P. Chan's book, choose dalta 0.0001
trans_cov = delta / (1 - delta) * np.eye(2) #Vw
obs_mat = np.expand_dims(np.vstack([[x], [np.ones(len(x))]]).T, axis=1)
kf = KalmanFilter(n_dim_obs=1, n_dim_state=2, # y is 1-dimensional, (alpha, beta) is 2-dimensional
                  initial_state_mean=[0,0],     #initial beta
                  initial_state_covariance=np.ones((2, 2)),   #initial R
                  transition_matrices=np.eye(2),   #生成指定长度的单位矩阵 R         
                  observation_matrices=obs_mat,   #x 两列                         
                  observation_covariance=100,       #Ve---Q                  
                  transition_covariance=trans_cov) #Vw---R    
state_means = kf.filter(y.values)[0]
plt.plot(x.index, state_means[:,0])
```






![](https://ws3.sinaimg.cn/large/006tNc79gy1flvjnjblqbj30mu0dejs7.jpg)


## plot new spread using dynamic hedge ratio


```python
plt.plot(y-state_means[:,0]*x+5)
ts.adfuller(y-state_means[:,0]*x)[0]
```




    -6.4526601673175712




![](https://ws2.sinaimg.cn/large/006tNc79gy1flvjnsagzgj30ml0de75l.jpg)


Looks much more stable with tighter range between 2 to 14. ADF test also looks great -6.453

## Now compare 2 spread using OLS beta and Kalman filter Beta


```python
plt.plot(y-state_means[:,0]*x+5,'r',label='kalman filter')
plt.plot(y-k*x,'g',label = 'OLS')
plt.legend()
plt.show()
```

![png](https://ws1.sinaimg.cn/large/006tNc79gy1flvjo6lgt7j30mm0de0ub.jpg)

## Links
1. [Algothmic Trading](https://www.amazon.com/Algorithmic-Trading-Winning-Strategies-Rationale/dp/1118460146/ref=sr_1_1?ie=UTF8&qid=1511660151&sr=8-1&keywords=algorithmic+trading) page 77-84
2. [Quantopian Lecture](https://www.quantopian.com/lectures/kalman-filters) 
