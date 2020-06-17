---
title: Daily Forecasting of Capital Bikeshare usage with Facebook's prophet
categories: [R, forecasting]
excerpt: "Forecasting daily Bikeshare usage with Facebook's prophet"
toc: true
toc_label: "Content"
toc_sticky: true
toc_icon: 'calendar-day'
usemathjax: true
---
### Predicting Daily DC Bike Share Ridership with **prophet**

The
[prophet](https://facebook.github.io/prophet/docs/quick_start.html#r-api)
package was created by Facebook in 2017 and provides a procedure for
forecasting time series data. It’s a great package that’s meant to help
produce reasonable forecasts with messy data without much manual effort.
Prophet can handle, yearly, weekly, and daily seasonality, miultiple
regressors, missing data, and can account for holidays or other dates
that have a profound effect on the time series. It’s also very quick,
allowing the user to iterate between many different approaches. The full
white paper is available [here](https://peerj.com/preprints/3190/)

I’ve used prophet for goal-setting in a previous role, but in this post
will apply it to the DC Capital BikeShare daily ridership. This post is
an extension of the [Forecasting Monthly Usage of Capital
Bikeshare](https://rsolter.github.io/r/forecasting/Monthly_Bike_Forecast/);
its dataset is from the same source, just not aggregated to the monthly
level.

To try and improve the accuracy of the ridership forecasts, daily
precipitation data from NOAA will be included. The precipitation data
was collected from the Reagan National Airport weather station and
measures precipitation in tenths of a millimeter.

------------------------------------------------------------------------

### Visual Exploration of Daily Ridership Data

A few takeaways:

-   The average number of trips taken in a given day is just under
    7,500.

-   Unsurprisingly, the series exhibits clear seasonality, with more
    trips taking place during the summer months and fewer during the
    winter. From this plot, it’s unclear if a weekly seasonality exists.

-   There are also a number of outliers apparent in the data. Most of
    these outliers are registering below the curve, and could possibly
    be due to bad weather on those particular days. There also appear to
    be a few positive outliers in the first quarter of the year and
    could relate to a seasonal event such as the arrival of the cherry
    blossoms.

-   Given that the size of the seasonal fluctuations grow over time, a
    multiplicative model may be best for modeling this series.

-   Similarly, over time the trend appears to grow less and less,
    indicating a model incorporating a logistic trend may be most
    appropriate. Ostensibly, this growth is down to the
    [expansion](https://en.wikipedia.org/wiki/Capital_Bikeshare#Expansion)
    in the number of bike stations and bikes over the last \~10 years.

<!-- -->

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
    ##       1    4378    7296    7499   10804   19113

![](/rblogging/2019/12/20/exploratory%20viz-1.png)![](/rblogging/2019/12/20/exploratory%20viz-2.png)

------------------------------------------------------------------------

#### Removing Outliers

Since there appear to be multiple outliers in the dataset and the
prophet package can handle missing data points, we’ll remove some
outliers before proceeding to modeling the data.

*Identifying and processing outliers*

One way to identify outliers is to use [moving-median
decomposition](https://anomaly.io/anomaly-detection-moving-median-decomposition/).
In the case below, a two-week-long running median trend is subtracted
from the original series. Next, observations which fall outside of 4
standard deviations of that running median trend are isolated and
removed from the series for future modeling.

![](/rblogging/2019/12/20/Handing%20Outliers%20in%20overall%20dataset-1.png)![](/rblogging/2019/12/20/Handing%20Outliers%20in%20overall%20dataset-2.png)

------------------------------------------------------------------------

### Prophet Models

To test the model, the daily observations of bike rides and daily
precipitation are split into training sets (2010-05-15 to 2016-08-31)
and test splits (2016-09-01 to 2018-09-17). Effectively, 25% of the data
is being set aside for testing.

``` r
# Change column names for propet package
prophet_btrips <- bike_trips_clean
colnames(prophet_btrips) <- c("ds","y")

# Paritioning
train <- prophet_btrips %>% filter(ds<'2016-09-01') # 2173 records
test <- prophet_btrips %>% filter(ds>='2016-09-01') # 747 records

reg_train <- percip %>% filter(ds<'2016-09-01') # 2178 records
reg_test <- percip %>% filter(ds>='2016-09-01') # 747 records
```

#### Base Model

The first model is set to account for daily, weekly, and yearly
seasonality.

Ultimately, the MAPE is close to 31%. From both the plot of the forecast
as well as the residual distribution, we can see that the current model
generally underestimates daily ridership.

``` r
# https://facebook.github.io/prophet/docs/quick_start.html#r-api

## Prophet Forecast 1

  # Training
  prophetFit1 <- prophet(train,
                         yearly.seasonality = T,
                         weekly.seasonality = T,
                         daily.seasonality = F)

  # Creating dataframe for future forecast, will only have date values
  future <- make_future_dataframe(prophetFit1, periods = nrow(test),freq = 'day')

  # Predicting - resulting 'prophetForecast1' dataframe contains columns for predictions, trend data, and uncertainty intervals
  prophetForecast1 <- predict(prophetFit1, future)

  # Visualizing ts components - trend, weekly and yearly seasonalities
  prophet_plot_components(prophetFit1, prophetForecast1)
```

![](/rblogging/2019/12/20/Prophet%20Forecast%201-1.png)

``` r
  # Visualize forecast
  #plot(prophetFit1, prophetForecast1,ylabel = "Daily Rides",xlabel = "Date")

  # Plot
  p1 <- ggplot() + theme_minimal()
  p1 <- p1 + geom_point(data = prophetFit1$history, aes(x = as.Date(ds), y = y), size = 0.5)
  p1 <- p1 + geom_line(data = prophetForecast1, aes(x = as.Date(ds), y = yhat), color = "#0072B2")
  p1 <- p1 + geom_ribbon(data = prophetForecast1, aes(x = as.Date(ds), ymin = yhat_lower, ymax = yhat_upper), fill = "#0072B2", alpha = 0.15)
  p1 <- p1 + geom_point(data = test, aes(x = ds, y = y), size = 0.5, color = '#4daf4a') + scale_x_date(date_labels = "%Y")
  p1 <- p1 + labs(title = "Propet Base Model",caption = "Test data in green") + xlab("Date") + ylab("Daily Rides")

  p1
```

![](/rblogging/2019/12/20/Prophet%20Forecast%201-2.png)

``` r
  # Dynamic Plot of forecast
  # dyplot.prophet(prophetFit1, prophetForecast1)  



## Prophet Foreceast 1 - Evaluation


  # Calculating Fit
  Fit <- test
  Fit$y_hat <- tail(prophetForecast1$yhat,nrow(test))
  Fit$resid <- Fit$y-Fit$y_hat
  Fit$resid_perc <- (Fit$resid/Fit$y)*100
  Fit$abs_resid_perc <- abs(Fit$resid_perc)

  error_summary <-summary(Fit$abs_resid_perc)


  # Results
  error_summary # A summary of the errors as percentages
```

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max.
    ##   0.0069   8.0666  15.7430  31.2611  25.1704 621.1372

``` r
  ggplot(Fit,aes(resid_perc)) + geom_histogram(binwidth = 10) + ggtitle("Prophet Model 1 : Distribution of Residuals (%)") +
    xlab("Residual Percentage") + ylab("") + theme_minimal()
```

![](/rblogging/2019/12/20/Prophet%20Forecast%201-3.png)

------------------------------------------------------------------------

#### Logistic Growth Model + Carrying Capacity

Given that the overall trend appears to be leveling off, we’ll try a
second modeling approach with a logistic growth specified and a carrying
capacity. The *carry capacity* refers to the maxium possible value for
the series. In this case, we’ll set it at 19,113 which was the maximum
value of rides in the original dataset.

Unforuntately, taking both of these steps doesn’t seem to have a
material difference on the MAPE (32%) or the overall

``` r
  # Training

  train$cap <- 19113  

  prophetFit2 <- prophet(train,
                         yearly.seasonality = T,
                         weekly.seasonality = T,
                         daily.seasonality = F,
                         growth = "logistic")

  # Creating dataframe for future forecast
  future <- make_future_dataframe(prophetFit2, periods = nrow(test),freq = 'day')
  future$cap <- 19113


  # Predicting
  prophetForecast2 <- predict(prophetFit2, future)

  # The resulting prophetForecast1 dataframe contains columns for predictions, trend data, and uncertainty intervals

  # Visualizing ts components - trend, weekly and yearly seasonalities
  prophet_plot_components(prophetFit2, prophetForecast2)
```

![](/rblogging/2019/12/20/Prophet%20Forecast%202%20Log%20Growth%20+%20CC-1.png)

``` r
  # Visualize forecast
  # Plot
  p2 <- ggplot() + theme_minimal()
  p2 <- p2 + geom_point(data = prophetFit2$history, aes(x = as.Date(ds), y = y), size = 0.5)
  p2 <- p2 + geom_line(data = prophetForecast2, aes(x = as.Date(ds), y = yhat), color = "#0072B2")
  p2 <- p2 + geom_ribbon(data = prophetForecast2, aes(x = as.Date(ds), ymin = yhat_lower, ymax = yhat_upper), fill = "#0072B2", alpha = 0.15)
  p2 <- p2 + geom_point(data = test, aes(x = ds, y = y), size = 0.5, color = '#4daf4a') + scale_x_date(date_labels = "%Y")
  p2 <- p2 + labs(title = "Propet Model: Logistic Growth + Carrying Capacity",caption = "Test data in green") + xlab("Date") + ylab("Daily Rides")

  p2
```

![](/rblogging/2019/12/20/Prophet%20Forecast%202%20Log%20Growth%20+%20CC-2.png)

``` r
  # Dynamic Plot of forecast
  #dyplot.prophet(prophetFit1, prophetForecast1)  


# Evaluation of the second prediction:

  # Calculating Fit
  Fit <- test
  Fit$y_hat <- tail(prophetForecast2$yhat,nrow(test))
  Fit$resid <- Fit$y-Fit$y_hat
  Fit$resid_perc <- (Fit$resid/Fit$y)*100
  Fit$abs_resid_perc <- abs(Fit$resid_perc)

  error_summary <-summary(Fit$abs_resid_perc)


  # Results
  error_summary # A summary of the errors as percentages
```

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max.
    ##   0.0174   7.5454  14.9604  32.1028  26.0863 648.6879

``` r
  ggplot(Fit,aes(resid_perc)) + geom_histogram(binwidth = 10) + ggtitle("Prophet Model 2 : Distribution of Residuals (%)") +
    xlab("Residual Percentage") + ylab("") + theme_minimal()
```

![](/rblogging/2019/12/20/Prophet%20Forecast%202%20Log%20Growth%20+%20CC-3.png)

#### Model with Logistic Transformation

Another step we can take is transforming the data with logarithmic
transformation. This is done when the time series shows
heteroskedasticity, or a non-constant variance. This is evident in the
way the size of the seasonal swings in ridership grow over time.

Transforming the data back to compare our predictions to the test set
reports a MAPE of 26%, a clear improvement!

``` r
# 3rd Prophet prediction with Log transformation



# Paritioning
train <- prophet_btrips %>% filter(ds<'2016-09-01') # 2173 records
test <- prophet_btrips %>% filter(ds>='2016-09-01') # 747 records

reg_train <- percip %>% filter(ds<'2016-09-01') # 2178 records
reg_test <- percip %>% filter(ds>='2016-09-01') # 747 records


  ## The log transform results in slightly lower MAPE rate

  # Transform
  log_train <- train
  log_train$y <- log(log_train$y)


  # Trainging
  m_log <- prophet(log_train,
                   yearly.seasonality = T,
                         weekly.seasonality = T,
                         daily.seasonality = F)

  # Creating dataframe for log forecast
  future_log <- make_future_dataframe(m_log, periods = nrow(test),freq = 'day')

  # Predicting
  forecast_log <- predict(m_log, future_log)

  # Visualize forecast
  #plot(m_log, forecast_log)

  # Plot
  p3 <- ggplot() + theme_minimal()
  p3 <- p3 + geom_point(data = m_log$history, aes(x = as.Date(ds), y = y), size = 0.5)
  p3 <- p3 + geom_line(data = forecast_log, aes(x = as.Date(ds), y = yhat), color = "#0072B2")
  p3 <- p3 + geom_ribbon(data = forecast_log, aes(x = as.Date(ds), ymin = yhat_lower, ymax = yhat_upper), fill = "#0072B2", alpha = 0.15)
  #p3 <- p3 + geom_point(data = test, aes(x = ds, y = y), size = 0.5, color = '#4daf4a') + scale_x_date(date_labels = "%Y")
  p3 <- p3 + labs(title = "Propet Model: Log Transform") + xlab("Date") + ylab("Log Transform of Daily Rides")

  p3
```

![](/rblogging/2019/12/20/Prophet%20Forecast%203%20-%20Log-1.png)

``` r
  # Visualizing ts components - trend, weekly and yearly seasonalities
  prophet_plot_components(m_log, forecast_log)
```

![](/rblogging/2019/12/20/Prophet%20Forecast%203%20-%20Log-2.png)

``` r
  # Function for back transform of log
  log_back <- function(y){
    e <- exp(1)
    return(e^y)
  }


  # Calculating Fit
  Log_Fit <- test
  Log_Fit$y_hat_log <- tail(forecast_log$yhat,nrow(Log_Fit))
  Log_Fit$y_hat <- log_back(Log_Fit$y_hat_log)

  Log_Fit$resid <- Log_Fit$y-Log_Fit$y_hat
  Log_Fit$resid_perc <- (Log_Fit$resid/Log_Fit$y)*100

  Log_Fit$abs_resid_perc <- abs(Log_Fit$resid_perc)


  log_error_summary <-summary(Log_Fit$abs_resid_perc)



    # Results
  log_error_summary # A summary of the errors as percentages
```

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max.
    ##   0.0484   8.9831  16.4676  26.3528  27.5142 381.9659

``` r
  ggplot(Log_Fit,aes(resid_perc)) + geom_histogram(binwidth = 10) + ggtitle("Prophet Model 3 : Distribution of Residuals (%)") +
    xlab("Residual Percentage") + ylab("") + theme_minimal()
```

![](/rblogging/2019/12/20/Prophet%20Forecast%203%20-%20Log-3.png)

#### Model with precipitation Regressor

In this attempt, we will add the daily precipitation data in as a
regressor. Technically, we would want to actually use predictions of
daily precipitation if we wanted to do an actual forecast, but this is
just an illustrative example.

Unfortunately, the addition of this data has not materially improved the
accuracy of our predictions in comparison to the base model.

``` r
# Paritioning
train <- prophet_btrips %>% filter(ds<'2016-09-01') # 2173 records
test <- prophet_btrips %>% filter(ds>='2016-09-01') # 747 records

reg_train <- percip %>% filter(ds<'2016-09-01') # 2178 records
reg_test <- percip %>% filter(ds>='2016-09-01') # 747 records


reg_train <- left_join(train,reg_train)

## Prophet Forecast 4

  # Training
  m <- prophet()
  add_regressor(m=m,name="percip")

  m <- fit.prophet(m,reg_train)

  future_m <- make_future_dataframe(m,periods=nrow(test),freq="day")

  future_m$ds <- as.Date(future_m$ds)
  percip$ds <- as.Date(percip$ds)

  future_ridership <- left_join(future_m,percip)

  reg_forecast <- predict(m, future_ridership)

  #plot(m,reg_forecast)

  p4 <- ggplot() + theme_minimal()
  p4 <- p4 + geom_point(data = m$history, aes(x = as.Date(ds), y = y), size = 0.5)
  p4 <- p4 + geom_line(data = reg_forecast, aes(x = as.Date(ds), y = yhat), color = "#0072B2")
  p4 <- p4 + geom_ribbon(data = reg_forecast, aes(x = as.Date(ds), ymin = yhat_lower, ymax = yhat_upper), fill = "#0072B2", alpha = 0.15)
  p4 <- p4 + geom_point(data = test, aes(x = ds, y = y), size = 0.5, color = '#4daf4a') + scale_x_date(date_labels = "%Y")
  p4 <- p4 + labs(title = "Propet Model: Using precipitation as a Regressor",caption = "Test data in green") + xlab("Date") + ylab("Daily Rides")

  p4
```

![](/rblogging/2019/12/20/Prophet%20Forecast%204%20-%20Regression-1.png)

``` r
## Prophet Foreceast 4 - Evaluation


  # Calculating Fit
  Fit <- test
  Fit$y_hat <- tail(reg_forecast$yhat,nrow(test))
  Fit$resid <- Fit$y-Fit$y_hat
  Fit$resid_perc <- (Fit$resid/Fit$y)*100
  Fit$abs_resid_perc <- abs(Fit$resid_perc)

  error_summary <-summary(Fit$abs_resid_perc)


  # Results
  error_summary # A summary of the errors as percentages
```

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max.
    ##   0.0069   8.0666  15.7430  31.2611  25.1704 621.1372

``` r
  ggplot(Fit,aes(resid_perc)) + geom_histogram(binwidth = 10) + ggtitle("Prophet Model 4 : Distribution of Residuals (%)") +
    xlab("Residual Percentage") + ylab("") + theme_minimal()
```

![](/rblogging/2019/12/20/Prophet%20Forecast%204%20-%20Regression-2.png)



### Conclusion and Next Steps

Predicting daily ridership is inherently difficult. swings in the weather, holidays, Nationals games, protests, and events like the blooming of the cherry blossoms can all affect ridership on a day to day basis. It shouldn't come as much of a surprise that the best model put forth was the one that transformed the data specifically to handle non-constant variability in the time series.

That said, there are still a number of steps that could be taken to potentially
improve the prediction:

-   Iterate further, trying different combinations of the above approaches.

-   Make use of prophet’s holiday feature which allows for certain dates
    to be marked as influential in establishing change points in the
    trend

-   Engineer the precipitation data to a dummy variable based upon a
    certain amount of precipitation being measured

-   Add more regressors such as number of bike stations in use or daily
    temperature
