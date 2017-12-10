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
import pandas as pd
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

## Simulation method -- Monte Carlo

Based on theories of Ito process, Wiener process, Markov process... A popular way of giving an intuitive understanding of stochastic process is Monte Carlo Simulation. It's also important in derivative pricing. When we want pricing a swaption, we can simulate 10000 times or more, then calculate a mean. 

Let's try a very simple simulation: initial price 100 dollars, 250 trading days per annum, 15% expected return per annum, and 30% volatility, 50 iteration simulation


```python
steps = 250  #suppose we have 250 trading day per year
deltaT = 1./250
mu = 0.15 #annual return 15%
sigma = 0.3 #volatility
iteration = 50 
S = pd.DataFrame(np.ones((1,iteration))*100)


for step in range(steps):
    e = pd.DataFrame(np.random.normal (0,1,(1, iteration)))  #mean = 0, std = 1
    deltaS = mu * deltaT * S.loc[step] + sigma * math.sqrt(deltaT) * e * S.loc[step]
    St = S.loc[step] + deltaS
    S = pd.concat([S,St],axis=0,ignore_index=True)
```
```python
S.head()
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>40</th>
      <th>41</th>
      <th>42</th>
      <th>43</th>
      <th>44</th>
      <th>45</th>
      <th>46</th>
      <th>47</th>
      <th>48</th>
      <th>49</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>...</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>98.799372</td>
      <td>98.613010</td>
      <td>99.054043</td>
      <td>98.951496</td>
      <td>99.675369</td>
      <td>96.039261</td>
      <td>100.033568</td>
      <td>99.957196</td>
      <td>95.369025</td>
      <td>103.946669</td>
      <td>...</td>
      <td>98.430461</td>
      <td>100.825713</td>
      <td>100.933380</td>
      <td>102.446119</td>
      <td>103.565280</td>
      <td>100.865304</td>
      <td>94.855860</td>
      <td>103.897204</td>
      <td>99.564886</td>
      <td>101.008968</td>
    </tr>
    <tr>
      <th>2</th>
      <td>97.216696</td>
      <td>96.964628</td>
      <td>97.463697</td>
      <td>101.583594</td>
      <td>102.187120</td>
      <td>95.860745</td>
      <td>100.346235</td>
      <td>97.733937</td>
      <td>98.482128</td>
      <td>103.844044</td>
      <td>...</td>
      <td>97.533574</td>
      <td>101.639430</td>
      <td>101.079234</td>
      <td>105.913118</td>
      <td>106.120520</td>
      <td>95.122657</td>
      <td>93.723337</td>
      <td>105.305003</td>
      <td>99.160192</td>
      <td>98.144769</td>
    </tr>
    <tr>
      <th>3</th>
      <td>101.677488</td>
      <td>96.072972</td>
      <td>97.815723</td>
      <td>99.112850</td>
      <td>100.320990</td>
      <td>98.223254</td>
      <td>105.867891</td>
      <td>99.971380</td>
      <td>100.286124</td>
      <td>104.485413</td>
      <td>...</td>
      <td>97.379666</td>
      <td>101.984687</td>
      <td>105.191351</td>
      <td>107.497072</td>
      <td>104.432157</td>
      <td>94.754123</td>
      <td>94.926242</td>
      <td>105.141475</td>
      <td>99.621328</td>
      <td>98.829195</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100.711137</td>
      <td>93.997839</td>
      <td>97.798960</td>
      <td>102.687318</td>
      <td>102.010434</td>
      <td>97.899202</td>
      <td>107.314447</td>
      <td>96.359926</td>
      <td>99.335166</td>
      <td>106.539097</td>
      <td>...</td>
      <td>95.383104</td>
      <td>102.611748</td>
      <td>104.199084</td>
      <td>105.577873</td>
      <td>103.529675</td>
      <td>97.955022</td>
      <td>92.651058</td>
      <td>109.322455</td>
      <td>98.313385</td>
      <td>95.648553</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 50 columns</p>
</div>


```python
S.plot(legend=None)
plt.show()
```


![](https://ws2.sinaimg.cn/large/006tNc79gy1fmc9m37g81j30mz0detgi.jpg)


### wow....

## Links
1. [Option, Futures and other derivatives](https://www.amazon.com/Options-Futures-Derivatives-DerivaGem-Package/dp/0132777428/ref=sr_1_2?ie=UTF8&qid=1512540363&sr=8-2&keywords=options+futures+and+other+derivatives+8)  Chaper 13


