---
title: Forecasting Capital Bikeshare usage with ARIMA
categories: [R, forecasting]
date: 2019-11-01
excerpt: "ARIMA modeling of monthly Bikeshare usage"

---

![DC BikeShare](/assets/images/obi-onyeador-a3PAvq9FpIY-unsplash.jpg)
_Photo by Obi Onyeador on Unsplash_


### Monthly DC BikeShare Ridership

------------------------------------------------------------------------

In this post, I'll be modeling monthly ridership in the DC BikeShare program using ARIMA modeling.
The original data was gathered from the official DC Bike Share
[site](https://s3.amazonaws.com/capitalbikeshare-data/index.html), spans
bike trips logged from October, 2010 to August, 2018, and has been aggregated up from a daily level.


Having used this data [previously](https://rsolter.github.io/r/forecasting/Monthly_Bike_Forecast_ETS/) to forecast with exponential smoothing models, I wanted to try ARIMA out in part because it allows for explanatory variables in the regression. ARIMA is an acronym for Auto Regressive (AR) Integrated (I) Moving Average (MA) and can be implemented in R using the automated function `auto.arima` from the forecast package. For illustration, I've added monthly average temperatures [downloaded](https://www.ncei.noaa.gov/access/search/data-search/global-summary-of-the-month) from NOAA and collected from the Regan National Airport weather station.



From the initial plot, we can tell the data has clear seasonality with many fewer riders in the winter months. At the same time, we see an overall growing trend that appears logarithmic.

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Plots-1.png)

#### Decomposition

The first step in building an ARIMA model is decomposing the series into its three components: seasonal, trend, and remainder. It appears that the magnitude of the seasonal fluctuations are not stable and appear to grow over time:

``` r
decompose(ts_month, type="multiplicative") %>% autoplot() +
  xlab("Year") +
  ggtitle("Classical multiplicative decomposition
    of monthly bike rentals")
```

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/unnamed-chunk-1-1.png)

#### Stationarity

A requirement of ARIMA modeling is **stationarity** of the series which
is achieved by having *time invariant* mean, variance, and co-variance
of the series. Stationarity is tested formally using the augmented
Dickey-Fuller test.

To accomplish this, the first step is to remove the seasonal trend from
the original series.

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Removing%20Seasonality-1.png)

However, the “de-seasoned” data fails the Adjusted Dickey-Fuller test,
indicating the series is not yet stationary:

``` r
adf.test(deseasonal_cnt, alternative = "stationary")
```

    ##
    ##  Augmented Dickey-Fuller Test
    ##
    ## data:  deseasonal_cnt
    ## Dickey-Fuller = -3.9739, Lag order = 4, p-value = 0.01378
    ## alternative hypothesis: stationary

------------------------------------------------------------------------

Examining the ACF and PACF plots indicate that differencing the series
by 1 (_d_=1), could help:

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/ACF%20and%20PACF%20plots-1.png)![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/ACF%20and%20PACF%20plots-2.png)

Differencing the data by one period appears to bring stationarity to the
series as tested by the ADF test.

``` r
deseasoned_count_d1 = diff(deseasonal_cnt, differences = 1)

adf.test(deseasoned_count_d1, alternative = "stationary")
```

    ##
    ##  Augmented Dickey-Fuller Test
    ##
    ## data:  deseasoned_count_d1
    ## Dickey-Fuller = -5.7992, Lag order = 4, p-value = 0.01
    ## alternative hypothesis: stationary

From the plot, it appears that the differenced, deseasoned data has a
stationary mean, though the variance does not appear constant:

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Searching%20for%20Stationarity:%20Differencing%20Plot-1.png)

------------------------------------------------------------------------

Running ACF, PACF plots of the differenced data to see what values for
*q*, *p* would be for an ARIMA model:

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Differenced%20ACF-1.png)

The ACF plot shows significant auto-correlations at lags 1,2,8,9,11
(*q*)

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Differenced%20PACF-1.png)

The PACF plot shows significant partial-correlations at 1,2, and beyond
(*p*)

#### ARIMA

Using the findings from the ACF, PACF plots above, an ARIMA(2,1,9) model
is chosen.

    ##
    ## Call:
    ## arima(x = deseasoned_count_d1, order = c(2, 1, 9))
    ##
    ## Coefficients:
    ##           ar1      ar2      ma1      ma2      ma3      ma4      ma5
    ##       -0.6738  -0.6415  -0.5796  -0.1744  -0.8391  -0.0723  -0.0498
    ## s.e.   0.1069   0.1014   0.1965   0.1638   0.1301   0.1766   0.1902
    ##          ma6     ma7     ma8      ma9
    ##       0.4855  0.5922  0.3399  -0.6488
    ## s.e.  0.1856  0.1361  0.1390   0.1653
    ##
    ## sigma^2 estimated as 434834666:  log likelihood = -1067.37,  aic = 2158.74

Evaluating the diagnostic plots for the (2,1,9) residuals return a
seemingly random residual plot and no significant autocorrelations.

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Fit%20Evaluation-1.png)


#### Comparing ARIMA to auto.arima

Comparing the ARIMA(2,1,9) to the results from auto.arima we can see the
automated function does not account for the autocorrelation at q(9).
Furthermore, the AIC is slightly smaller in the ARIMA(2,1,9) model.

``` r
auto.arima(deseasoned_count_d1)
```

    ## Series: deseasoned_count_d1
    ## ARIMA(2,0,2) with non-zero mean
    ##
    ## Coefficients:
    ##          ar1      ar2      ma1     ma2      mean
    ##       1.5664  -0.7621  -1.8584  0.9260  2642.453
    ## s.e.  0.0843   0.0814   0.0881  0.0825   905.082
    ##
    ## sigma^2 estimated as 669557486:  log likelihood=-1087.21
    ## AIC=2186.42   AICc=2187.39   BIC=2201.68

``` r
fit <- auto.arima(deseasoned_count_d1,seasonal=FALSE)

tsdisplay(residuals(fit), lag.max=45, main='(2,0,2) Model Residuals')
```

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Auto-Arima-1.png)

#### Forecasting and Back-Testing

To fully evaluate the model’s predictive power, we set aside the last 12
months of the *deseasonal\_cnt* object and compare our predictions to
what was actually observed.

![](/rblogging/2019/11/01/Monthly_Bike_Forecast_files/Partition%20and%20Forecast-1.png)


#### References

Hyndman, R.J., & Athanasopoulos, G. (2018) Forecasting: principles and practice, 2nd edition, OTexts: Melbourne, Australia. [https://otexts.com/fpp2/](https://otexts.com/fpp2/). Accessed on October, 2019
