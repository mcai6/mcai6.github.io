---
layout:     post   				    # 使用的布局（不需要改）
title:      2.1 Pairs -- Bitcoin and Litecoin 				# 标题 
subtitle:    #副标题
date:       2017-12-12				# 时间
author:     Manlin 						# 作者
header-img: img/post-bg-coffee.jpeg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
- trading
- backtesting
---

## Roughly analyzing about pairs trading between ltc & btc
Will take a look at how to apply **FFT** or **Endpoint FFT** to do filter noise.... 


```python
import quandl
import matplotlib.pyplot as plt
import statsmodels.api as sm
import numpy as np
import statsmodels.tsa.stattools as ts 
from pykalman import KalmanFilter
```


```python
btc2016 = quandl.get("BITFINEX/BTCUSD", authtoken="7j6iBm2vwLRzFD4gLJxQ",start_date="2016-01-01", end_date="2017-12-12")
ltc2016 = quandl.get("BITFINEX/LTCUSD", authtoken="7j6iBm2vwLRzFD4gLJxQ",start_date="2016-01-01", end_date="2017-12-22")
```


```python
## Bicoin
btc2016.tail()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>High</th>
      <th>Low</th>
      <th>Mid</th>
      <th>Last</th>
      <th>Bid</th>
      <th>Ask</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-08</th>
      <td>16649.0</td>
      <td>13139.000000</td>
      <td>16610.0</td>
      <td>16607.000000</td>
      <td>16608.0</td>
      <td>16612.0</td>
      <td>141225.692924</td>
    </tr>
    <tr>
      <th>2017-12-09</th>
      <td>17171.0</td>
      <td>13722.000000</td>
      <td>15833.0</td>
      <td>15816.780524</td>
      <td>15822.0</td>
      <td>15844.0</td>
      <td>124036.209586</td>
    </tr>
    <tr>
      <th>2017-12-10</th>
      <td>16300.0</td>
      <td>13010.000000</td>
      <td>14660.5</td>
      <td>14660.000000</td>
      <td>14660.0</td>
      <td>14661.0</td>
      <td>77819.746914</td>
    </tr>
    <tr>
      <th>2017-12-11</th>
      <td>15741.0</td>
      <td>12730.636861</td>
      <td>14932.0</td>
      <td>14937.000000</td>
      <td>14926.0</td>
      <td>14938.0</td>
      <td>107145.646045</td>
    </tr>
    <tr>
      <th>2017-12-12</th>
      <td>17450.0</td>
      <td>14900.000000</td>
      <td>16753.5</td>
      <td>16754.000000</td>
      <td>16753.0</td>
      <td>16754.0</td>
      <td>78088.984663</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Litecoin
ltc2016.tail()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>High</th>
      <th>Low</th>
      <th>Mid</th>
      <th>Last</th>
      <th>Bid</th>
      <th>Ask</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-08</th>
      <td>97.92</td>
      <td>84.40</td>
      <td>93.6405</td>
      <td>93.776</td>
      <td>93.516</td>
      <td>93.765</td>
      <td>4.115328e+05</td>
    </tr>
    <tr>
      <th>2017-12-09</th>
      <td>129.00</td>
      <td>88.93</td>
      <td>120.8950</td>
      <td>120.900</td>
      <td>120.890</td>
      <td>120.900</td>
      <td>1.194806e+06</td>
    </tr>
    <tr>
      <th>2017-12-10</th>
      <td>156.00</td>
      <td>120.55</td>
      <td>148.9700</td>
      <td>149.000</td>
      <td>148.950</td>
      <td>148.990</td>
      <td>1.588527e+06</td>
    </tr>
    <tr>
      <th>2017-12-11</th>
      <td>154.00</td>
      <td>122.45</td>
      <td>144.4400</td>
      <td>144.620</td>
      <td>144.300</td>
      <td>144.580</td>
      <td>8.341240e+05</td>
    </tr>
    <tr>
      <th>2017-12-12</th>
      <td>226.80</td>
      <td>143.20</td>
      <td>209.9000</td>
      <td>210.000</td>
      <td>209.670</td>
      <td>210.130</td>
      <td>1.682685e+06</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.subplot(1,2,1)
btc2016['Last'].plot(label = 'btc',figsize=(20,6))
plt.legend()
plt.subplot(1,2,2)
ltc2016['Last'].plot(label = 'ltc',figsize=(20,6))
plt.legend()
plt.show()
```


![png](https://ws3.sinaimg.cn/large/006tNc79gy1fmdzmu1r55j30wn0a2q3g.jpg)


## Too calm before, let's start from April 2017


```python
btc = quandl.get("BITFINEX/BTCUSD", authtoken="7j6iBm2vwLRzFD4gLJxQ",start_date="2017-04-01", end_date="2017-12-12")
ltc = quandl.get("BITFINEX/LTCUSD", authtoken="7j6iBm2vwLRzFD4gLJxQ",start_date="2017-04-01", end_date="2017-12-22")
```


```python
e_rate = quandl.get("BITFINEX/LTCBTC", authtoken="7j6iBm2vwLRzFD4gLJxQ", start_date="2017-04-01", end_date="2017-12-12") # btc&ltc exchange rate
e_rate
e_rate['Last'].plot()
plt.show()
## exchage rate between btc and ltc
```


![png](https://ws2.sinaimg.cn/large/006tNc79gy1fmdzn8xzsrj30ax07cwes.jpg)


plot abave is the exchange rate between btc and ltc, from 0.008-0.02, or lets say 50-125 times.
Let's blindly choose a hedge ratio 100, to make them into a same scale, later we will find out what real-time ratio should be.


```python
btc_last = btc['Last']
ltc_last = ltc['Last']*100
spread = btc_last - ltc_last

btc_last.plot(label = 'btc',figsize=(20,6))
ltc_last.plot(label = 'ltc')
plt.legend()
plt.show()

spread.plot(label = 'spread', figsize=(20,6))
plt.legend()
plt.show()
## plot both price and spread
```


![png](https://ws4.sinaimg.cn/large/006tNc79gy1fmdznni4w4j30wn0a2q3p.jpg)



![png](https://ws2.sinaimg.cn/large/006tNc79gy1fmdzo27znpj30wn0a2gm9.jpg)


Well...Spread on Dec is still on mean reserting, but this swing is so risky... I need to do some research later about these days around the Futures launching.... If get rid off these days, since btc was involved in Future launched, looks pretty good to do pairs.. But if we use dynamic hedge ratio later, we can see if we can solve this super volatile problem. 


```python
x = ltc_last/100
y = btc_last
X = sm.add_constant(x)
Y = y
model= sm.OLS(Y,X).fit()
k = model.params[1]
plt.plot(y-k*x)
plt.show()
print('ADF test:', ts.adfuller(y-k*x)[0])
```


![png](https://ws1.sinaimg.cn/large/006tNc79gy1fmdzod3988j30ax070mxa.jpg)


    ADF test: -2.28912152118


ADF reselt is not that stable. Let's try another way to get hedge ratio


```python
delta = 1e-3   ### from Ernest P. Chan's book, choose dalta 0.0001
trans_cov = delta / (1 - delta) * np.eye(2) #Vw
obs_mat = np.expand_dims(np.vstack([[x], [np.ones(len(x))]]).T, axis=1)
kf = KalmanFilter(n_dim_obs=1, n_dim_state=2, # y is 1-dimensional, (alpha, beta) is 2-dimensional
                  initial_state_mean=[0,0],     #initial beta
                  initial_state_covariance=np.ones((2, 2)),   #initial R
                  transition_matrices=np.eye(2),   #生成指定长度的单位矩阵 R
                  observation_matrices=obs_mat,   #x 两列
                  observation_covariance=50,       #Ve---Q
                  transition_covariance=trans_cov) #Vw---R
beta = kf.filter(y.values)[0]
plt.plot(x.index, beta[:,0])
plt.show()
```


![png](https://ws4.sinaimg.cn/large/006tNc79gy1fmdzoly3ylj30ai070t8t.jpg)



```python
plt.plot(y-beta[:,0]*x)
plt.show()
ts.adfuller(y-beta[:,0]*x)[0]
```


![png](https://ws1.sinaimg.cn/large/006tNc79gy1fmdzoyh5n7j30ax0700sv.jpg)





    -3.1725709923182284




```python
plt.plot(y-beta[:,0]*x,'r',label='kalman filter')
plt.plot(y-k*x,'g',label = 'OLS')
plt.legend()
plt.show()
```


![png](https://ws1.sinaimg.cn/large/006tNc79gy1fmdzp9xsf4j30ax0700t0.jpg)


So..No ideal, but much more hedged and mean reverting....So should be less risky.. On Doc. it tighten the swing **-5k~7k** to **-2.5k~3.5k**

Every day we can update a perfectly hedge ratio, from that can do intraday arbitrage

I'll update the **signal part** after I can get intraday data....


```python

```
