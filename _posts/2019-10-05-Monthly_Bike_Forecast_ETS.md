---
title: Exponential Smoothing Forecasts of Capital Bikeshare
categories: [R, forecasting]
date: 2019-10-05
excerpt: "Predicting monthly Bikeshare usage with R, forecast"

---

One of the most basic methods for forecasting univariate series is
exponential smoothing. The basic idea behind exponential smoothing is
that forecasts are produced by using weighted averages of past
observations. The weights applied to the past observations decay
exponentially as the observations get older with more recent
observations receiving more weight. This post will examine applying this
method to predict monthly ridership on the Capital BikeShare program.

![](/rblogging/2019/10/05/viz-1.png)

**Partition**

To test the accuracy of the predictions, we will split the ts() object
using the `TSstudio` package. The test period is set to be the final 12
observations, or one year.

``` r
ts_month
```

    ##         Jan    Feb    Mar    Apr    May    Jun    Jul    Aug    Sep    Oct
    ## 2010                                                                 36613
    ## 2011  38116  48089  63855  94632 135537 143266 141105 136447 127158 123292
    ## 2012  96590 102979 164654 173954 195637 202600 203607 214503 218572 198840
    ## 2013 126142 111190 158838 238604 252922 257118 270919 292083 285187 257652
    ## 2014 114006 124031 167568 272490 310151 320523 331157 324218 327597 301244
    ## 2015 128592 109766 193107 318127 366930 314016 364015 364313 328038 290351
    ## 2016 123252 145654 283493 285516 288720 368096 366435 357306 344246 343243
    ## 2017 174804 226303 245403 365990 339677 398751 397680 402534 391371 384833
    ## 2018 168590 182378 238998 328907 374115 392338 404761 403866              
    ##         Nov    Dec
    ## 2010  48114  28765
    ## 2011 101949  87087
    ## 2012 152664 123713
    ## 2013 194621 138090
    ## 2014 199298 153221
    ## 2015 228296 187357
    ## 2016 259106 168719
    ## 2017 252534 177897
    ## 2018

``` r
ts_month_partition <- TSstudio::ts_split(ts_month,sample.out = 12)
train <- ts_month_partition$train
test <- ts_month_partition$test
```

**Holt-Winters Modeling**

Given both the clear seasonality and growing trend in the series, we’ll
have to use the Holt-Winters seasonal method to capture both factors.
Furthermore, given that the size of the seasonal swings in ridership
have seen to grown from \~100k in 2012 to over +200k in 2017 and 2018,
it makes sense to use a multiplicative method. Read more about the
methodology [here](https://otexts.com/fpp2/holt-winters.html).

![](/rblogging/2019/10/05/holt-winters-1.png)

It certainly appears that the multiplicative model does a better job
than the additive one in estimating the ridership, at least until the
final few months of the predictive window.

We can confirm this by looking at the errors on a monthly basis. Over
the 12 months, the mean absolute percent error for the additive model is
23.9% while the multiplicative method is closer at 10.6%.

|  Actual|  Add.Forecast|  Add.Error.Abs|  Add.Error.Perc|  Mult.Forecast|  Mult.Error.Abs|  Mult.Error.Perc|
|-------:|-------------:|--------------:|---------------:|--------------:|---------------:|----------------:|
|  391371|      394964.9|       3593.933|            0.92|       383812.5|       -7558.548|            -1.93|
|  384833|      376862.2|      -7970.817|           -2.07|       355866.2|      -28966.766|            -7.53|
|  252534|      314872.2|      62338.235|           24.69|       274616.9|       22082.928|             8.74|
|  177897|      269802.0|      91905.036|           51.66|       206849.5|       28952.470|            16.27|
|  168590|      254859.0|      86268.970|           51.17|       185347.2|       16757.204|             9.94|
|  182378|      265063.3|      82685.256|           45.34|       205223.4|       22845.435|            12.53|
|  238998|      336311.3|      97313.296|           40.72|       286876.9|       47878.856|            20.03|
|  328907|      397253.7|      68346.681|           20.78|       383590.8|       54683.760|            16.63|
|  374115|      425827.6|      51712.589|           13.82|       412234.7|       38119.712|            10.19|
|  392338|      437097.9|      44759.941|           11.41|       422610.3|       30272.296|             7.72|
|  404761|      451522.4|      46761.354|           11.55|       435415.4|       30654.421|             7.57|
|  403866|      455687.8|      51821.826|           12.83|       439027.5|       35161.529|             8.71|

**Improving with ets()**

Within the `forecast` package in R there exists the `ets()` function
which actually estimates the best model

![](/rblogging/2019/10/05/ets-1.png)

Note that the ets() forecast performs much better than the Holt-Winters
approach, with an average absolute erro of just 4.6%

![](/rblogging/2019/10/05/ets%20evaluation-1.png)
