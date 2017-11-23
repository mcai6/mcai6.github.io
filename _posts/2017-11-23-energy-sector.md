
## Find the best pair in Energy Sector


```python
from quantopian.pipeline import Pipeline
from quantopian.research import run_pipeline
from quantopian.pipeline.filters import Q500US
import quantopian.pipeline.classifiers.fundamentals as fd
import numpy as np
import pandas as pd
import statsmodels.api as sm
import matplotlib.pyplot as plt
import statsmodels.tsa.stattools as ts 
import heapq
```

### Get the list of stocks in energy sector from Q500US

(Q500US:
"A default universe containing approximately 500 US equities each day.
Constituents are chosen at the start of each calendar month by selecting the top 500 "tradeable" stocks by 200-day average dollar volume, capped at 30% of equities allocated to any single sector"
--From Quantopian API reference)


```python
pipe = Pipeline()
sector_filter = fd.Sector().eq(309)
pipe.set_screen(Q500US() & sector_filter)
stocks = run_pipeline(pipe, start_date='2017-10-31', end_date='2017-10-31')
print len(stocks)
stocks.head()
```

    47





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">2017-10-31 00:00:00+00:00</th>
      <th>Equity(216 [HES])</th>
    </tr>
    <tr>
      <th>Equity(448 [APA])</th>
    </tr>
    <tr>
      <th>Equity(455 [APC])</th>
    </tr>
    <tr>
      <th>Equity(1746 [COG])</th>
    </tr>
    <tr>
      <th>Equity(2368 [DVN])</th>
    </tr>
  </tbody>
</table>
</div>



### Get price in 2017:


```python
stocks_symbol = stocks.index.get_level_values(1)
energy_df = get_pricing(stocks_symbol, fields = 'price',start_date = '2017-01-01',end_date = '2017-10-31')
energy_df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Equity(216 [HES])</th>
      <th>Equity(448 [APA])</th>
      <th>Equity(455 [APC])</th>
      <th>Equity(1746 [COG])</th>
      <th>Equity(2368 [DVN])</th>
      <th>Equity(2564 [EOG])</th>
      <th>Equity(2587 [EQT])</th>
      <th>Equity(2621 [ESV])</th>
      <th>Equity(3443 [HAL])</th>
      <th>Equity(3620 [HFC])</th>
      <th>...</th>
      <th>Equity(34440 [CXO])</th>
      <th>Equity(39797 [OAS])</th>
      <th>Equity(40547 [TRGP])</th>
      <th>Equity(40852 [KMI])</th>
      <th>Equity(41636 [MPC])</th>
      <th>Equity(42251 [WPX])</th>
      <th>Equity(42788 [PSX])</th>
      <th>Equity(43512 [FANG])</th>
      <th>Equity(46240 [RICE])</th>
      <th>Equity(46989 [PE])</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-01-03 00:00:00+00:00</th>
      <td>61.786</td>
      <td>62.489</td>
      <td>70.036</td>
      <td>22.248</td>
      <td>46.830</td>
      <td>102.790</td>
      <td>63.974</td>
      <td>10.161</td>
      <td>55.005</td>
      <td>32.396</td>
      <td>...</td>
      <td>136.64</td>
      <td>15.735</td>
      <td>53.841</td>
      <td>21.068</td>
      <td>51.792</td>
      <td>14.930</td>
      <td>84.650</td>
      <td>103.24</td>
      <td>20.40</td>
      <td>35.730</td>
    </tr>
    <tr>
      <th>2017-01-04 00:00:00+00:00</th>
      <td>60.891</td>
      <td>62.332</td>
      <td>70.584</td>
      <td>22.258</td>
      <td>47.267</td>
      <td>102.889</td>
      <td>65.073</td>
      <td>10.410</td>
      <td>55.212</td>
      <td>33.081</td>
      <td>...</td>
      <td>135.67</td>
      <td>16.235</td>
      <td>54.101</td>
      <td>21.190</td>
      <td>50.148</td>
      <td>15.260</td>
      <td>85.089</td>
      <td>103.50</td>
      <td>20.84</td>
      <td>35.880</td>
    </tr>
    <tr>
      <th>2017-01-05 00:00:00+00:00</th>
      <td>61.077</td>
      <td>61.999</td>
      <td>71.392</td>
      <td>22.527</td>
      <td>48.760</td>
      <td>103.465</td>
      <td>65.762</td>
      <td>10.927</td>
      <td>55.548</td>
      <td>32.560</td>
      <td>...</td>
      <td>135.13</td>
      <td>15.900</td>
      <td>54.826</td>
      <td>21.152</td>
      <td>49.952</td>
      <td>14.945</td>
      <td>84.592</td>
      <td>103.67</td>
      <td>20.99</td>
      <td>36.230</td>
    </tr>
    <tr>
      <th>2017-01-06 00:00:00+00:00</th>
      <td>60.881</td>
      <td>61.872</td>
      <td>71.531</td>
      <td>22.776</td>
      <td>48.421</td>
      <td>104.498</td>
      <td>65.233</td>
      <td>11.634</td>
      <td>56.003</td>
      <td>30.880</td>
      <td>...</td>
      <td>135.62</td>
      <td>15.590</td>
      <td>55.216</td>
      <td>21.278</td>
      <td>49.297</td>
      <td>14.560</td>
      <td>83.266</td>
      <td>103.64</td>
      <td>21.00</td>
      <td>36.605</td>
    </tr>
    <tr>
      <th>2017-01-09 00:00:00+00:00</th>
      <td>59.612</td>
      <td>61.127</td>
      <td>69.747</td>
      <td>21.985</td>
      <td>46.342</td>
      <td>102.313</td>
      <td>63.056</td>
      <td>11.644</td>
      <td>55.430</td>
      <td>29.858</td>
      <td>...</td>
      <td>134.65</td>
      <td>15.130</td>
      <td>54.454</td>
      <td>21.112</td>
      <td>48.406</td>
      <td>13.840</td>
      <td>81.930</td>
      <td>101.43</td>
      <td>20.04</td>
      <td>35.750</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 47 columns</p>
</div>



### Find 5 largest correlation

I saw some people using log return to calculate correlation. The idea behind is right, but if you calculate the cointigration using return, you'll find all of them are cointegrated! So I'll still just use stock price to roughly found out some pairs with high correlation, then use coint() and adfuller() after that to pick the best one.


```python
CORR = energy_df.corr()
CORR = CORR.replace(1.0,0)
max_corr =heapq.nlargest(10, CORR.max())
a = set(max_corr)
max_corr = sorted(list(a),reverse=True)[0:5]
print '5 largest corrlation:\n',max_corr
```

    5 largest corrlation:
    [0.98607086616740613, 0.98544544942329704, 0.98422933159021964, 0.98299566414913098, 0.98107708803375981]


### 5 Pairs:


```python
pair_stocks=[]
for i in range(5):
    pair_stock=[]
    for j in range(len(CORR)):
        if max_corr[i] in CORR.values[j]:
            pair_stock.append(CORR.index[j])
    pair_stocks.append(pair_stock)
print 'Pairs:'
for i in pair_stocks:
    print i
```

    Pairs:
    [Equity(2621 [ESV]), Equity(25707 [WLL])]
    [Equity(5214 [NBR]), Equity(6928 [SLB])]
    [Equity(3443 [HAL]), Equity(10254 [PTEN])]
    [Equity(2368 [DVN]), Equity(9038 [RIG])]
    [Equity(455 [APC]), Equity(2621 [ESV])]



```python
for i in range(5):
    pair_price = energy_df[pair_stocks[i]]
    plt.plot(energy_df.index,pair_price.ix[:,0],label = pair_price.columns.values[0])
    plt.plot(energy_df.index,pair_price.ix[:,1],label = pair_price.columns.values[1])
    plt.legend(loc='best')
    plt.show()
```


![](https://ws3.sinaimg.cn/large/006tKfTcgy1flsptz3bj1j30ml0dejsm.jpg)



![](https://ws1.sinaimg.cn/large/006tKfTcgy1flspun9c94j30ml0det9h.jpg)



![](https://ws3.sinaimg.cn/large/006tKfTcgy1flspv1o3euj30ml0det9s.jpg)



![](https://ws1.sinaimg.cn/large/006tKfTcgy1flspvhhxskj30ml0dewfg.jpg)



![](https://ws2.sinaimg.cn/large/006tKfTcgy1flspvs4bkhj30ml0dewf9.jpg)


### Hedge ratio:


```python
K=[]
for i in range(5):
    X= energy_df[pair_stocks[i]].ix[:,0]
    Y= energy_df[pair_stocks[i]].ix[:,1]
    X = sm.add_constant(X)
    model= sm.OLS(Y,X).fit()
    k = model.params[1]
    K.append(k)
    
print 'Hedge ratios:\n',K 
```

    Hedge ratios:
    [1.1323232836864439, 1.8627042886004741, 0.7205511539835382, 0.42935698828016422, 0.23106617835715304]


### Pairs' cointegration & Spread's stationary


```python
print 'Cointegtration P-Value,','ADF t-statistic, ', '99% Critical value' 
for i in range(5):
    X= energy_df[pair_stocks[i]].ix[:,0]
    Y= energy_df[pair_stocks[i]].ix[:,1]
    
    print ts.coint(Y,X)[1],',    ',ts.adfuller(Y-K[i]*X)[0],',  ',ts.adfuller(Y-K[i]*X)[4]['1%']
  
```

    Cointegtration P-Value, ADF t-statistic,  99% Critical value
    2.81859493618e-05 ,     -5.81851116853 ,   -3.46218575928
    0.0312562146658 ,     -3.51304677228 ,   -3.46203150368
    0.00791300948484 ,     -3.97030378526 ,   -3.46203150368
    0.000398613367855 ,     -4.78651033563 ,   -3.46203150368
    0.00790375884328 ,     -3.97066349634 ,   -3.46203150368


From the result, I'd say the first and the 4th pairs are the best, because the cointegration P-Value is low enough to reject the hypothesis that the two legs are not cointegrated, and their ADF t-statistic are lower than 99% critical value which means the spread are stationary.
