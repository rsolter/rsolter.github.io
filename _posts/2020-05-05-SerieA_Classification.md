---
title: "Predicting Soccer Match Outcomes using caret"
categories: [R, caret]
date: 2020-05-05
excerpt: "Building models to predict the outcome of Serie A matches"
toc: true
toc_label: "Content"
toc_sticky: true
toc_icon: 'futbol'
---

### Predicting Soccer Match Outcomes using caret
![Stadio Olimpico](/assets/images/remi-jacquaint.jpg)

_Code for this project can be found on my [GitHub](https://github.com/rsolter/Serie-A-Predictions)_


### 1. Gathering Data

The data for this project was gathered from the official [Serie A
website](http://www.legaseriea.it/en) and its match reports from the
2015-16 season through the current 2019-20 season. Note that due to the
effects of the Covid-19, all matches were postponed in Italy after the
first week of March, 2020. The scrapers used the `rvest` package and can
be found
[here](https://github.com/rsolter/Serie-A-Predictions/tree/master/01%20Scrapers).
Weekly [Elo](https://en.wikipedia.org/wiki/Elo_rating_system) scores for
each team were also downloaded from the website Clubelo.com using their
[API](http://clubelo.com/API).

**Initial feature list:**

|                  |                                  |                  |
|------------------|----------------------------------|------------------|
| Goals            | Total Shots                      | Attacks (Middle) |
| Saves            | Shots on Target                  | Attacks (Left)   |
| Penalties        | Shots on Target from Free Kicks  | Attacks (Right)  |
| Fouls            | Shots off Target                 | Fast Breaks      |
| Red Cards        | Shots off Target from Free Kicks | Crosses          |
| Yellow Cards     | Shots from within the Box        | Long Balls       |
| Key Passes       | Shots on Target from Set Pieces  | Possession       |
| Completed Passes | Shots off Target from Set Pieces | Corners          |
| Passing Accuracy | Scoring Chances                  | Offsides         |

#### A Note on Expected goals

[Expected
Goals](https://wikieducator.org/Sport_Informatics_and_Analytics/Performance_Monitoring/Expected_Goals)
(*xG*) are not included in this analysis. xG measures the quality of
goalscoring chances and the likelihood of them being scored. Factors
influencing the probability of a goal being scored from a shot include
distance from the goal; angle from the goal; and whether or not the
player taking the shot was at least 1 m away from the nearest defender.
Although very popular, I have not included these stats in the analysis
but may do so later from a site like [understat](https://understat.com/)

------------------------------------------------------------------------

### 2. Processing the Data

In its raw form the observations gathered are grouped by match, with
stats for both the home and away teams. Below is an example of the top
five records.

<table>
<thead>
<tr>
<th style="text-align:left;">
Team\_h
</th>
<th style="text-align:left;">
Team\_a
</th>
<th style="text-align:right;">
goals\_h
</th>
<th style="text-align:right;">
goals\_a
</th>
<th style="text-align:right;">
saves\_h
</th>
<th style="text-align:right;">
saves\_a
</th>
<th style="text-align:right;">
pen\_h
</th>
<th style="text-align:right;">
pen\_a
</th>
<th style="text-align:right;">
shots\_h
</th>
<th style="text-align:right;">
shots\_a
</th>
<th style="text-align:right;">
shots\_on\_h
</th>
<th style="text-align:right;">
shots\_on\_a
</th>
<th style="text-align:right;">
shot\_on\_fk\_h
</th>
<th style="text-align:right;">
shot\_on\_fk\_a
</th>
<th style="text-align:right;">
shots\_off\_h
</th>
<th style="text-align:right;">
shots\_off\_a
</th>
<th style="text-align:right;">
shot\_off\_fk\_h
</th>
<th style="text-align:right;">
shot\_off\_fk\_a
</th>
<th style="text-align:right;">
shots\_box\_h
</th>
<th style="text-align:right;">
shots\_box\_a
</th>
<th style="text-align:right;">
shots\_sp\_on\_h
</th>
<th style="text-align:right;">
shots\_sp\_on\_a
</th>
<th style="text-align:right;">
fouls\_h
</th>
<th style="text-align:right;">
fouls\_a
</th>
<th style="text-align:right;">
scoring\_chances\_h
</th>
<th style="text-align:right;">
scoring\_chances\_a
</th>
<th style="text-align:right;">
offsides\_h
</th>
<th style="text-align:right;">
offsides\_a
</th>
<th style="text-align:right;">
corners\_h
</th>
<th style="text-align:right;">
corners\_a
</th>
<th style="text-align:right;">
yellow\_h
</th>
<th style="text-align:right;">
yellow\_a
</th>
<th style="text-align:right;">
shots\_sp\_off\_h
</th>
<th style="text-align:right;">
shots\_sp\_off\_a
</th>
<th style="text-align:right;">
fast\_breaks\_h
</th>
<th style="text-align:right;">
fast\_breaks\_a
</th>
<th style="text-align:left;">
season
</th>
<th style="text-align:left;">
round
</th>
<th style="text-align:right;">
poss\_h
</th>
<th style="text-align:right;">
poss\_a
</th>
<th style="text-align:left;">
match\_date
</th>
<th style="text-align:right;">
attacks\_h
</th>
<th style="text-align:right;">
attacks\_a
</th>
<th style="text-align:right;">
match\_id
</th>
<th style="text-align:left;">
outcome
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
HellasVerona
</td>
<td style="text-align:left;">
Roma
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
2015-16
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:right;">
0.37
</td>
<td style="text-align:right;">
0.63
</td>
<td style="text-align:left;">
2015-08-22
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
D
</td>
</tr>
<tr>
<td style="text-align:left;">
Lazio
</td>
<td style="text-align:left;">
Bologna
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
2015-16
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:right;">
0.58
</td>
<td style="text-align:right;">
0.42
</td>
<td style="text-align:left;">
2015-08-22
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
H
</td>
</tr>
<tr>
<td style="text-align:left;">
Juventus
</td>
<td style="text-align:left;">
Udinese
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
2015-16
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:right;">
0.64
</td>
<td style="text-align:right;">
0.36
</td>
<td style="text-align:left;">
2015-08-23
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
A
</td>
</tr>
<tr>
<td style="text-align:left;">
Empoli
</td>
<td style="text-align:left;">
Chievoverona
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
2015-16
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:right;">
0.53
</td>
<td style="text-align:right;">
0.47
</td>
<td style="text-align:left;">
2015-08-23
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
A
</td>
</tr>
<tr>
<td style="text-align:left;">
Fiorentina
</td>
<td style="text-align:left;">
Milan
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
<td style="text-align:left;">
2015-16
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:right;">
0.57
</td>
<td style="text-align:right;">
0.43
</td>
<td style="text-align:left;">
2015-08-23
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
H
</td>
</tr>
</tbody>
</table>
From this raw form the data has been processed in the following ways:

#### Replacing data with lagged averages

All the stats collected (with the exception of Elo), have been replaced
with lagged averages. The rationale for this is that we need historical
performance data to try and predict future match outcomes.

#### Data regrouped by team

The full set of records is broken up by teams so that there exist +20
datasets. One for each individual team with lagged average stats on
their and their opponents performance.

#### Splitting data

From the five season in the dataset, seasons 2015-16, 2016-17, and
2017-18 are used for training, the 2018-19 season will searve as the
validation set, and the 2019-20 season will serve as the holdout data.

As the matches occur in chronological order, each dataset will be broken
apart in such a way that the model will be built on the first *n*
observations and tested on the *n+1* match. This is accomplished using
the *time\_slice()* function in the **caret** package. A visual
representation of this partition can be seen below in the bottom left
quadrant. In the example below, there is a time series with 20 data
points. We can fix initialWindow = 5 and look at different settings of
the other two arguments. In the plot below, rows in each panel
correspond to different data splits (i.e. resamples) and the columns
correspond to different data points. Also, red indicates samples that
are in included in the training set and the blue indicates samples in
the test set. See more
[here](http://topepo.github.io/caret/data-splitting.html#data-splitting-for-time-series).

![](/assets/images/Split_time-1.svg)

------------------------------------------------------------------------

### 3. Feature Engineering

Before starting any modeling, there are some data process and feature
engineering steps to take on:

#### Feature selection with Random Forest

There are a lot of variables collected in the match report that are
likely not predictive of a matches outcome. To remove those from the
dataset, a random forest is used to determine which variables are
relatively unimportant. Ultimately, I drop information about penalties,
free kick shots off target, shots on target from free kicks, and
information about shots taken from set pieces.

In contrast, it appears that the number of shots within the penalty box,
total shots on target, and overall numbers of attacks are the most
predictive of match outcome.

![](/rblogging/2020/05/05/Feature%20Selection%20using%20Random%20Forest-1.png)

#### Feature Extraction with PCA

Even after removing 10 features from the dataset, there are still a
large number of predictors for each match’s outcome. To reduce the
number of features while maximizing the amount of variation still
explained, principal components analysis was applied as a
[pre-processing
technique](https://topepo.github.io/caret/pre-processing.html#transforming-predictors)
in caret.

------------------------------------------------------------------------

### 3. Distribution of Outcomes by Team

It’s worthwhile to point out that the distribution of outcomes is
naturally different by team. Dominant teams like Juventus, Napoli, Roma,
and Inter Milan all have win percentages over 50%. This class imbalance
has consequences for the models built. However, for the example below,
we’ll focus on Sampdoria which has a relatively balanced distribution of
outcomes for seasons 2015-16 - 2018-19: 34.8% Win, 23.6%, Loss 41.4%.

![](/rblogging/2020/05/05/outcome_viz-1.png)

### 4. Illustrative Example with U.C Sampdoria

The records for Sampdoria have been broken apart into three datasets:
`Samp_train` with records from the “2015-16”,“2016-17”,“2017-18”
seasons, `Samp_validation` with records from the “2018-19” season, and
`Samp_holdout` from the ’2019-20\` season. Each dataset has been
scrubbed of the first three records from each season as they do not have
lagged average values for the various features.

Various models will be trained on the `Samp_train` set, their parameters
tested and tuned on `Samp_validation`, and all will be tested
individually and in an ensemble on the `Samp_holdout` set.

``` r
# Partitioning Sampdoria Data

Samp <- final_data[[17]]

Samp <- Samp[complete.cases(Samp), ]

Samp_train <- Samp %>%
  filter(season%in%c("2015-16","2016-17","2017-18")) %>%
  select(-c(match_id,match_date,season,round,Team,Opp,Points_gained)) %>%
  as.data.frame() # 105 records

Samp_validation <- Samp %>%
  filter(season%in%c("2018-19")) %>%
  select(-c(match_id,match_date,season,round,Team,Opp,Points_gained)) # 35 records

Samp_holdout <- Samp %>%
  filter(season%in%c("2019-20")) %>%
  select(-c(match_id,match_date,season,round,Team,Opp,Points_gained)) # 21 records
```

#### Multinomial Logistic Regression

``` r
myTimeControl <- trainControl(method = "timeslice",
                              initialWindow = 10,
                              horizon = 1,
                              fixedWindow = FALSE,
                              allowParallel = TRUE)

samp_mult_log = train(
  outcome ~ .,
  data = Samp_train,
  method = "multinom",
  preProc = c("pca"),
  trControl = myTimeControl
)
```

    ## # weights:  27 (16 variable)
    ## initial  value 10.986123
    ## iter  10 value 0.002278
    ## iter  20 value 0.000258
    ## final  value 0.000098
    ## converged
    ## # weights:  27 (16 variable)
    ## initial  value 10.986123
    ## iter  10 value 1.569648
    ## final  value 1.569502
    ## converged
    ## # weights:  27 (16 variable)
    ## initial  value 10.986123
    ## iter  10 value 0.019792
    ## iter  20 value 0.016212
    ## iter  30 value 0.013786
    ## iter  40 value 0.012717
    ## iter  50 value 0.011533
    ## iter  60 value 0.010786
    ## iter  70 value 0.010597
    ## iter  80 value 0.010532
    ## iter  90 value 0.010460
    ## iter 100 value 0.010392
    ## final  value 0.010392
    ## stopped after 100 iterations
    ## # weights:  30 (18 variable)
    ## initial  value 12.084735
    ## final  value 0.000080
    ## converged
    ## # weights:  30 (18 variable)
    ## initial  value 12.084735
    ## iter  10 value 1.634830
    ## final  value 1.632383
    ## converged
    ## # weights:  30 (18 variable)
    ## initial  value 12.084735
    ## iter  10 value 0.023222
    ## iter  20 value 0.018318
    ## iter  30 value 0.014536
    ## iter  40 value 0.013103
    ## iter  50 value 0.011860
    ## iter  60 value 0.011058
    ## iter  70 value 0.010912
    ## iter  80 value 0.010849
    ## iter  90 value 0.010812
    ## iter 100 value 0.010782
    ## final  value 0.010782
    ## stopped after 100 iterations
    ## # weights:  30 (18 variable)
    ## initial  value 13.183347
    ## iter  10 value 0.041547
    ## final  value 0.000081
    ## converged
    ## # weights:  30 (18 variable)
    ## initial  value 13.183347
    ## iter  10 value 1.810559
    ## iter  20 value 1.803484
    ## iter  20 value 1.803484
    ## iter  20 value 1.803484
    ## final  value 1.803484
    ## converged
    ## # weights:  30 (18 variable)
    ## initial  value 13.183347
    ## iter  10 value 0.047106
    ## iter  20 value 0.014613
    ## iter  30 value 0.013528
    ## iter  40 value 0.012570
    ## iter  50 value 0.012421
    ## iter  60 value 0.012291
    ## iter  70 value 0.012218
    ## iter  80 value 0.012208
    ## iter  90 value 0.012206
    ## iter 100 value 0.012203
    ## final  value 0.012203
    ## stopped after 100 iterations
    ## # weights:  33 (20 variable)
    ## initial  value 14.281960
    ## iter  10 value 0.054489
    ## iter  20 value 0.001676
    ## iter  30 value 0.000157
    ## final  value 0.000073
    ## converged
    ## # weights:  33 (20 variable)
    ## initial  value 14.281960
    ## iter  10 value 2.238263
    ## iter  20 value 2.222038
    ## final  value 2.222038
    ## converged
    ## # weights:  33 (20 variable)
    ## initial  value 14.281960
    ## iter  10 value 0.061603
    ## iter  20 value 0.018526
    ## iter  30 value 0.016833
    ## iter  40 value 0.016333
    ## iter  50 value 0.015749
    ## iter  60 value 0.015349
    ## iter  70 value 0.015243
    ## iter  80 value 0.015193
    ## iter  90 value 0.015176
    ## iter 100 value 0.015174
    ## final  value 0.015174
    ## stopped after 100 iterations
    ## # weights:  33 (20 variable)
    ## initial  value 15.380572
    ## iter  10 value 0.178957
    ## iter  20 value 0.003155
    ## final  value 0.000091
    ## converged
    ## # weights:  33 (20 variable)
    ## initial  value 15.380572
    ## iter  10 value 3.228822
    ## iter  20 value 3.193510
    ## final  value 3.193510
    ## converged
    ## # weights:  33 (20 variable)
    ## initial  value 15.380572
    ## iter  10 value 0.188165
    ## iter  20 value 0.034272
    ## iter  30 value 0.030141
    ## iter  40 value 0.029179
    ## iter  50 value 0.028628
    ## iter  60 value 0.028462
    ## iter  70 value 0.028379
    ## iter  80 value 0.028344
    ## iter  90 value 0.028324
    ## iter 100 value 0.028314
    ## final  value 0.028314
    ## stopped after 100 iterations
    ## # weights:  36 (22 variable)
    ## initial  value 16.479184
    ## iter  10 value 0.190427
    ## iter  20 value 0.001152
    ## final  value 0.000099
    ## converged
    ## # weights:  36 (22 variable)
    ## initial  value 16.479184
    ## iter  10 value 3.453803
    ## iter  20 value 3.244117
    ## final  value 3.244112
    ## converged
    ## # weights:  36 (22 variable)
    ## initial  value 16.479184
    ## iter  10 value 0.199307
    ## iter  20 value 0.036303
    ## iter  30 value 0.030603
    ## iter  40 value 0.029151
    ## iter  50 value 0.028699
    ## iter  60 value 0.028522
    ## iter  70 value 0.028413
    ## iter  80 value 0.028319
    ## iter  90 value 0.028308
    ## iter 100 value 0.028298
    ## final  value 0.028298
    ## stopped after 100 iterations
    ## # weights:  36 (22 variable)
    ## initial  value 17.577797
    ## iter  10 value 0.341948
    ## iter  20 value 0.002651
    ## final  value 0.000075
    ## converged
    ## # weights:  36 (22 variable)
    ## initial  value 17.577797
    ## iter  10 value 3.662472
    ## iter  20 value 3.625011
    ## final  value 3.625008
    ## converged
    ## # weights:  36 (22 variable)
    ## initial  value 17.577797
    ## iter  10 value 0.351192
    ## iter  20 value 0.043810
    ## iter  30 value 0.037957
    ## iter  40 value 0.036028
    ## iter  50 value 0.035409
    ## iter  60 value 0.035268
    ## iter  70 value 0.035092
    ## iter  80 value 0.034967
    ## iter  90 value 0.034931
    ## iter 100 value 0.034894
    ## final  value 0.034894
    ## stopped after 100 iterations
    ## # weights:  39 (24 variable)
    ## initial  value 18.676409
    ## iter  10 value 0.267734
    ## iter  20 value 0.001983
    ## final  value 0.000077
    ## converged
    ## # weights:  39 (24 variable)
    ## initial  value 18.676409
    ## iter  10 value 3.401403
    ## iter  20 value 3.318870
    ## final  value 3.318861
    ## converged
    ## # weights:  39 (24 variable)
    ## initial  value 18.676409
    ## iter  10 value 0.275284
    ## iter  20 value 0.033963
    ## iter  30 value 0.028743
    ## iter  40 value 0.027452
    ## iter  50 value 0.026804
    ## iter  60 value 0.026493
    ## iter  70 value 0.026310
    ## iter  80 value 0.026177
    ## iter  90 value 0.026108
    ## iter 100 value 0.026064
    ## final  value 0.026064
    ## stopped after 100 iterations
    ## # weights:  39 (24 variable)
    ## initial  value 19.775021
    ## iter  10 value 0.102829
    ## iter  20 value 0.014717
    ## iter  30 value 0.003473
    ## iter  40 value 0.000534
    ## iter  50 value 0.000325
    ## iter  60 value 0.000176
    ## iter  70 value 0.000120
    ## final  value 0.000095
    ## converged
    ## # weights:  39 (24 variable)
    ## initial  value 19.775021
    ## iter  10 value 4.161941
    ## iter  20 value 4.087308
    ## final  value 4.087304
    ## converged
    ## # weights:  39 (24 variable)
    ## initial  value 19.775021
    ## iter  10 value 0.134844
    ## iter  20 value 0.056767
    ## iter  30 value 0.049117
    ## iter  40 value 0.047603
    ## iter  50 value 0.044059
    ## iter  60 value 0.042450
    ## iter  70 value 0.041801
    ## iter  80 value 0.041017
    ## iter  90 value 0.040406
    ## iter 100 value 0.040011
    ## final  value 0.040011
    ## stopped after 100 iterations
    ## # weights:  39 (24 variable)
    ## initial  value 20.873633
    ## iter  10 value 0.059739
    ## iter  20 value 0.013832
    ## iter  30 value 0.000652
    ## iter  40 value 0.000235
    ## iter  50 value 0.000166
    ## final  value 0.000089
    ## converged
    ## # weights:  39 (24 variable)
    ## initial  value 20.873633
    ## iter  10 value 4.309459
    ## iter  20 value 4.239544
    ## final  value 4.239544
    ## converged
    ## # weights:  39 (24 variable)
    ## initial  value 20.873633
    ## iter  10 value 0.095954
    ## iter  20 value 0.058423
    ## iter  30 value 0.051997
    ## iter  40 value 0.050203
    ## iter  50 value 0.043706
    ## iter  60 value 0.042532
    ## iter  70 value 0.042021
    ## iter  80 value 0.041331
    ## iter  90 value 0.041028
    ## iter 100 value 0.040595
    ## final  value 0.040595
    ## stopped after 100 iterations
    ## # weights:  42 (26 variable)
    ## initial  value 21.972246
    ## iter  10 value 0.041723
    ## iter  20 value 0.011754
    ## iter  30 value 0.002897
    ## iter  40 value 0.000750
    ## final  value 0.000099
    ## converged
    ## # weights:  42 (26 variable)
    ## initial  value 21.972246
    ## iter  10 value 4.171472
    ## iter  20 value 4.149155
    ## final  value 4.149153
    ## converged
    ## # weights:  42 (26 variable)
    ## initial  value 21.972246
    ## iter  10 value 0.082615
    ## iter  20 value 0.057709
    ## iter  30 value 0.053215
    ## iter  40 value 0.050208
    ## iter  50 value 0.046754
    ## iter  60 value 0.044559
    ## iter  70 value 0.041335
    ## iter  80 value 0.039700
    ## iter  90 value 0.038203
    ## iter 100 value 0.037980
    ## final  value 0.037980
    ## stopped after 100 iterations
    ## # weights:  45 (28 variable)
    ## initial  value 23.070858
    ## iter  10 value 0.015489
    ## iter  20 value 0.002859
    ## iter  30 value 0.000461
    ## iter  40 value 0.000243
    ## final  value 0.000088
    ## converged
    ## # weights:  45 (28 variable)
    ## initial  value 23.070858
    ## iter  10 value 3.706920
    ## iter  20 value 3.696997
    ## final  value 3.696992
    ## converged
    ## # weights:  45 (28 variable)
    ## initial  value 23.070858
    ## iter  10 value 0.057144
    ## iter  20 value 0.045839
    ## iter  30 value 0.040990
    ## iter  40 value 0.037894
    ## iter  50 value 0.034694
    ## iter  60 value 0.030220
    ## iter  70 value 0.029672
    ## iter  80 value 0.028563
    ## iter  90 value 0.028285
    ## iter 100 value 0.027930
    ## final  value 0.027930
    ## stopped after 100 iterations
    ## # weights:  45 (28 variable)
    ## initial  value 24.169470
    ## iter  10 value 0.070148
    ## iter  20 value 0.013548
    ## iter  30 value 0.001330
    ## iter  40 value 0.000741
    ## iter  50 value 0.000211
    ## final  value 0.000084
    ## converged
    ## # weights:  45 (28 variable)
    ## initial  value 24.169470
    ## iter  10 value 4.347452
    ## iter  20 value 4.306333
    ## final  value 4.306316
    ## converged
    ## # weights:  45 (28 variable)
    ## initial  value 24.169470
    ## iter  10 value 0.109917
    ## iter  20 value 0.058648
    ## iter  30 value 0.052030
    ## iter  40 value 0.049940
    ## iter  50 value 0.042200
    ## iter  60 value 0.039246
    ## iter  70 value 0.038791
    ## iter  80 value 0.038432
    ## iter  90 value 0.038021
    ## iter 100 value 0.037655
    ## final  value 0.037655
    ## stopped after 100 iterations
    ## # weights:  48 (30 variable)
    ## initial  value 25.268083
    ## iter  10 value 0.084647
    ## iter  20 value 0.008356
    ## iter  30 value 0.002169
    ## iter  40 value 0.000778
    ## iter  50 value 0.000221
    ## iter  60 value 0.000194
    ## final  value 0.000098
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 25.268083
    ## iter  10 value 4.616451
    ## iter  20 value 4.566475
    ## final  value 4.566459
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 25.268083
    ## iter  10 value 0.126169
    ## iter  20 value 0.057708
    ## iter  30 value 0.052989
    ## iter  40 value 0.048057
    ## iter  50 value 0.042068
    ## iter  60 value 0.039550
    ## iter  70 value 0.039110
    ## iter  80 value 0.038821
    ## iter  90 value 0.038485
    ## iter 100 value 0.038339
    ## final  value 0.038339
    ## stopped after 100 iterations
    ## # weights:  48 (30 variable)
    ## initial  value 26.366695
    ## iter  10 value 0.544977
    ## iter  20 value 0.008549
    ## iter  30 value 0.000183
    ## iter  30 value 0.000092
    ## iter  30 value 0.000091
    ## final  value 0.000091
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 26.366695
    ## iter  10 value 5.259189
    ## iter  20 value 4.912772
    ## final  value 4.912586
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 26.366695
    ## iter  10 value 0.555336
    ## iter  20 value 0.058836
    ## iter  30 value 0.048955
    ## iter  40 value 0.047299
    ## iter  50 value 0.045361
    ## iter  60 value 0.044278
    ## iter  70 value 0.043957
    ## iter  80 value 0.043791
    ## iter  90 value 0.043469
    ## iter 100 value 0.043222
    ## final  value 0.043222
    ## stopped after 100 iterations
    ## # weights:  48 (30 variable)
    ## initial  value 27.465307
    ## iter  10 value 1.156381
    ## iter  20 value 0.047411
    ## iter  30 value 0.000848
    ## final  value 0.000079
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 27.465307
    ## iter  10 value 5.249055
    ## iter  20 value 4.691173
    ## final  value 4.690753
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 27.465307
    ## iter  10 value 1.166338
    ## iter  20 value 0.072224
    ## iter  30 value 0.044008
    ## iter  40 value 0.041266
    ## iter  50 value 0.040291
    ## iter  60 value 0.039822
    ## iter  70 value 0.039356
    ## iter  80 value 0.039133
    ## iter  90 value 0.038886
    ## iter 100 value 0.038765
    ## final  value 0.038765
    ## stopped after 100 iterations
    ## # weights:  48 (30 variable)
    ## initial  value 28.563920
    ## iter  10 value 0.705509
    ## iter  20 value 0.032486
    ## iter  30 value 0.001579
    ## final  value 0.000091
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 28.563920
    ## iter  10 value 6.940650
    ## iter  20 value 5.660852
    ## final  value 5.654691
    ## converged
    ## # weights:  48 (30 variable)
    ## initial  value 28.563920
    ## iter  10 value 0.720028
    ## iter  20 value 0.089197
    ## iter  30 value 0.060652
    ## iter  40 value 0.058038
    ## iter  50 value 0.057426
    ## iter  60 value 0.057042
    ## iter  70 value 0.056906
    ## iter  80 value 0.056735
    ## iter  90 value 0.056590
    ## iter 100 value 0.056416
    ## final  value 0.056416
    ## stopped after 100 iterations
    ## # weights:  51 (32 variable)
    ## initial  value 29.662532
    ## iter  10 value 1.253119
    ## iter  20 value 0.011751
    ## final  value 0.000098
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 29.662532
    ## iter  10 value 7.701051
    ## iter  20 value 6.815373
    ## iter  30 value 6.807643
    ## final  value 6.807642
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 29.662532
    ## iter  10 value 1.266803
    ## iter  20 value 0.102859
    ## iter  30 value 0.086903
    ## iter  40 value 0.080311
    ## iter  50 value 0.077442
    ## iter  60 value 0.076433
    ## iter  70 value 0.076048
    ## iter  80 value 0.075512
    ## iter  90 value 0.074830
    ## iter 100 value 0.074569
    ## final  value 0.074569
    ## stopped after 100 iterations
    ## # weights:  51 (32 variable)
    ## initial  value 30.761144
    ## iter  10 value 2.128444
    ## iter  20 value 0.064317
    ## iter  30 value 0.000916
    ## iter  40 value 0.000164
    ## iter  40 value 0.000081
    ## iter  40 value 0.000081
    ## final  value 0.000081
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 30.761144
    ## iter  10 value 8.122305
    ## iter  20 value 7.062444
    ## iter  30 value 7.045206
    ## final  value 7.045202
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 30.761144
    ## iter  10 value 2.143367
    ## iter  20 value 0.127464
    ## iter  30 value 0.087826
    ## iter  40 value 0.083936
    ## iter  50 value 0.083102
    ## iter  60 value 0.082344
    ## iter  70 value 0.081949
    ## iter  80 value 0.081786
    ## iter  90 value 0.081261
    ## iter 100 value 0.081097
    ## final  value 0.081097
    ## stopped after 100 iterations
    ## # weights:  51 (32 variable)
    ## initial  value 31.859756
    ## iter  10 value 5.477953
    ## iter  20 value 0.016838
    ## final  value 0.000091
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 31.859756
    ## iter  10 value 8.626016
    ## iter  20 value 7.460865
    ## iter  30 value 7.440110
    ## final  value 7.440105
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 31.859756
    ## iter  10 value 5.482539
    ## iter  20 value 0.115629
    ## iter  30 value 0.107153
    ## iter  40 value 0.104542
    ## iter  50 value 0.101043
    ## iter  60 value 0.095988
    ## iter  70 value 0.092672
    ## iter  80 value 0.090894
    ## iter  90 value 0.090097
    ## iter 100 value 0.089476
    ## final  value 0.089476
    ## stopped after 100 iterations
    ## # weights:  51 (32 variable)
    ## initial  value 32.958369
    ## iter  10 value 7.406161
    ## iter  20 value 0.024844
    ## final  value 0.000057
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 32.958369
    ## iter  10 value 10.326495
    ## iter  20 value 8.799709
    ## iter  30 value 8.767276
    ## final  value 8.767245
    ## converged
    ## # weights:  51 (32 variable)
    ## initial  value 32.958369
    ## iter  10 value 7.410469
    ## iter  20 value 0.166883
    ## iter  30 value 0.157879
    ## iter  40 value 0.147061
    ## iter  50 value 0.139843
    ## iter  60 value 0.136815
    ## iter  70 value 0.130120
    ## iter  80 value 0.128437
    ## iter  90 value 0.126168
    ## iter 100 value 0.124053
    ## final  value 0.124053
    ## stopped after 100 iterations
    ## # weights:  54 (34 variable)
    ## initial  value 34.056981
    ## iter  10 value 7.688724
    ## iter  20 value 0.092455
    ## iter  30 value 0.000169
    ## iter  30 value 0.000092
    ## iter  30 value 0.000092
    ## final  value 0.000092
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 34.056981
    ## iter  10 value 11.557102
    ## iter  20 value 10.034894
    ## iter  30 value 10.008823
    ## final  value 10.008810
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 34.056981
    ## iter  10 value 7.697110
    ## iter  20 value 0.214269
    ## iter  30 value 0.179085
    ## iter  40 value 0.172889
    ## iter  50 value 0.167802
    ## iter  60 value 0.164419
    ## iter  70 value 0.161730
    ## iter  80 value 0.160180
    ## iter  90 value 0.157878
    ## iter 100 value 0.155648
    ## final  value 0.155648
    ## stopped after 100 iterations
    ## # weights:  54 (34 variable)
    ## initial  value 35.155593
    ## iter  10 value 8.153115
    ## iter  20 value 0.051257
    ## iter  30 value 0.001065
    ## final  value 0.000071
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 35.155593
    ## iter  10 value 11.640943
    ## iter  20 value 10.365999
    ## iter  30 value 10.339157
    ## final  value 10.339136
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 35.155593
    ## iter  10 value 8.158344
    ## iter  20 value 0.220096
    ## iter  30 value 0.201375
    ## iter  40 value 0.194939
    ## iter  50 value 0.184817
    ## iter  60 value 0.179178
    ## iter  70 value 0.174953
    ## iter  80 value 0.172517
    ## iter  90 value 0.171110
    ## iter 100 value 0.169859
    ## final  value 0.169859
    ## stopped after 100 iterations
    ## # weights:  54 (34 variable)
    ## initial  value 36.254206
    ## iter  10 value 9.753852
    ## iter  20 value 0.089267
    ## iter  30 value 0.000174
    ## iter  30 value 0.000092
    ## iter  30 value 0.000091
    ## final  value 0.000091
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 36.254206
    ## iter  10 value 11.804846
    ## iter  20 value 10.286183
    ## iter  30 value 10.239217
    ## final  value 10.239178
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 36.254206
    ## iter  10 value 9.763221
    ## iter  20 value 0.224526
    ## iter  30 value 0.189413
    ## iter  40 value 0.182649
    ## iter  50 value 0.171233
    ## iter  60 value 0.165011
    ## iter  70 value 0.163365
    ## iter  80 value 0.162527
    ## iter  90 value 0.161566
    ## iter 100 value 0.160512
    ## final  value 0.160512
    ## stopped after 100 iterations
    ## # weights:  54 (34 variable)
    ## initial  value 37.352818
    ## iter  10 value 8.771439
    ## iter  20 value 0.048576
    ## final  value 0.000066
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 37.352818
    ## iter  10 value 12.398771
    ## iter  20 value 10.575822
    ## iter  30 value 10.544276
    ## final  value 10.544224
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 37.352818
    ## iter  10 value 8.776826
    ## iter  20 value 0.200104
    ## iter  30 value 0.185921
    ## iter  40 value 0.178659
    ## iter  50 value 0.174415
    ## iter  60 value 0.170633
    ## iter  70 value 0.167411
    ## iter  80 value 0.165753
    ## iter  90 value 0.164502
    ## iter 100 value 0.163656
    ## final  value 0.163656
    ## stopped after 100 iterations
    ## # weights:  54 (34 variable)
    ## initial  value 38.451430
    ## iter  10 value 8.720562
    ## iter  20 value 0.084144
    ## iter  30 value 0.000108
    ## iter  30 value 0.000055
    ## iter  30 value 0.000055
    ## final  value 0.000055
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 38.451430
    ## iter  10 value 12.285517
    ## iter  20 value 10.891374
    ## iter  30 value 10.850415
    ## final  value 10.850293
    ## converged
    ## # weights:  54 (34 variable)
    ## initial  value 38.451430
    ## iter  10 value 8.725690
    ## iter  20 value 0.269572
    ## iter  30 value 0.242824
    ## iter  40 value 0.234073
    ## iter  50 value 0.226417
    ## iter  60 value 0.221439
    ## iter  70 value 0.219005
    ## iter  80 value 0.216107
    ## iter  90 value 0.213936
    ## iter 100 value 0.211600
    ## final  value 0.211600
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 39.550042
    ## iter  10 value 4.590583
    ## iter  20 value 0.307442
    ## iter  30 value 0.000533
    ## final  value 0.000083
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 39.550042
    ## iter  10 value 12.056408
    ## iter  20 value 10.707974
    ## iter  30 value 10.656987
    ## final  value 10.656683
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 39.550042
    ## iter  10 value 4.605510
    ## iter  20 value 0.417799
    ## iter  30 value 0.221835
    ## iter  40 value 0.208292
    ## iter  50 value 0.205117
    ## iter  60 value 0.202949
    ## iter  70 value 0.200713
    ## iter  80 value 0.198649
    ## iter  90 value 0.197993
    ## iter 100 value 0.197591
    ## final  value 0.197591
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 40.648655
    ## iter  10 value 4.657982
    ## iter  20 value 0.471743
    ## iter  30 value 0.003600
    ## final  value 0.000096
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 40.648655
    ## iter  10 value 12.335338
    ## iter  20 value 10.734694
    ## iter  30 value 10.682275
    ## final  value 10.682254
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 40.648655
    ## iter  10 value 4.673994
    ## iter  20 value 0.558151
    ## iter  30 value 0.220605
    ## iter  40 value 0.204112
    ## iter  50 value 0.196829
    ## iter  60 value 0.194572
    ## iter  70 value 0.192076
    ## iter  80 value 0.189560
    ## iter  90 value 0.187767
    ## iter 100 value 0.186510
    ## final  value 0.186510
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 41.747267
    ## iter  10 value 5.056204
    ## iter  20 value 0.620966
    ## iter  30 value 0.005226
    ## final  value 0.000093
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 41.747267
    ## iter  10 value 11.473702
    ## iter  20 value 10.737844
    ## iter  30 value 10.711537
    ## final  value 10.711531
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 41.747267
    ## iter  10 value 5.069064
    ## iter  20 value 0.697436
    ## iter  30 value 0.226434
    ## iter  40 value 0.210229
    ## iter  50 value 0.206032
    ## iter  60 value 0.203764
    ## iter  70 value 0.200516
    ## iter  80 value 0.199206
    ## iter  90 value 0.198362
    ## iter 100 value 0.197578
    ## final  value 0.197578
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 42.845879
    ## iter  10 value 4.764353
    ## iter  20 value 0.693418
    ## iter  30 value 0.016040
    ## final  value 0.000057
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 42.845879
    ## iter  10 value 11.830573
    ## iter  20 value 10.766497
    ## iter  30 value 10.733094
    ## final  value 10.733067
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 42.845879
    ## iter  10 value 4.780490
    ## iter  20 value 0.769034
    ## iter  30 value 0.236217
    ## iter  40 value 0.213728
    ## iter  50 value 0.208224
    ## iter  60 value 0.205960
    ## iter  70 value 0.201922
    ## iter  80 value 0.200934
    ## iter  90 value 0.199674
    ## iter 100 value 0.198640
    ## final  value 0.198640
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 43.944492
    ## iter  10 value 4.915890
    ## iter  20 value 0.525596
    ## iter  30 value 0.002932
    ## final  value 0.000063
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 43.944492
    ## iter  10 value 12.300893
    ## iter  20 value 10.846852
    ## iter  30 value 10.775002
    ## final  value 10.774937
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 43.944492
    ## iter  10 value 4.928810
    ## iter  20 value 0.597175
    ## iter  30 value 0.208028
    ## iter  40 value 0.196501
    ## iter  50 value 0.190097
    ## iter  60 value 0.186476
    ## iter  70 value 0.184768
    ## iter  80 value 0.184162
    ## iter  90 value 0.183298
    ## iter 100 value 0.181820
    ## final  value 0.181820
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 45.043104
    ## iter  10 value 9.794832
    ## iter  20 value 0.450070
    ## iter  30 value 0.000601
    ## final  value 0.000084
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 45.043104
    ## iter  10 value 12.874127
    ## iter  20 value 11.134985
    ## iter  30 value 11.092768
    ## final  value 11.092726
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 45.043104
    ## iter  10 value 9.798967
    ## iter  20 value 0.626012
    ## iter  30 value 0.352061
    ## iter  40 value 0.310891
    ## iter  50 value 0.283649
    ## iter  60 value 0.259792
    ## iter  70 value 0.243160
    ## iter  80 value 0.231655
    ## iter  90 value 0.225501
    ## iter 100 value 0.220722
    ## final  value 0.220722
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 46.141716
    ## iter  10 value 11.385529
    ## iter  20 value 0.165693
    ## iter  30 value 0.000464
    ## final  value 0.000069
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 46.141716
    ## iter  10 value 14.044013
    ## iter  20 value 11.892768
    ## iter  30 value 11.829057
    ## final  value 11.828910
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 46.141716
    ## iter  10 value 11.389044
    ## iter  20 value 0.371404
    ## iter  30 value 0.301295
    ## iter  40 value 0.271631
    ## iter  50 value 0.250879
    ## iter  60 value 0.241850
    ## iter  70 value 0.228853
    ## iter  80 value 0.224353
    ## iter  90 value 0.221553
    ## iter 100 value 0.218992
    ## final  value 0.218992
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 47.240328
    ## iter  10 value 12.118443
    ## iter  20 value 0.674704
    ## iter  30 value 0.002495
    ## final  value 0.000051
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 47.240328
    ## iter  10 value 14.780685
    ## iter  20 value 12.383403
    ## iter  30 value 12.284605
    ## final  value 12.284373
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 47.240328
    ## iter  10 value 12.122012
    ## iter  20 value 0.776992
    ## iter  30 value 0.260496
    ## iter  40 value 0.252951
    ## iter  50 value 0.245899
    ## iter  60 value 0.238175
    ## iter  70 value 0.233901
    ## iter  80 value 0.230511
    ## iter  90 value 0.229148
    ## iter 100 value 0.228037
    ## final  value 0.228037
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 48.338941
    ## iter  10 value 19.100799
    ## iter  20 value 13.494158
    ## iter  30 value 11.155913
    ## iter  40 value 10.919875
    ## iter  50 value 10.912011
    ## final  value 10.911989
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 48.338941
    ## iter  10 value 21.170583
    ## iter  20 value 19.853576
    ## iter  30 value 19.831017
    ## final  value 19.830774
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 48.338941
    ## iter  10 value 19.103493
    ## iter  20 value 13.518742
    ## iter  30 value 11.290366
    ## iter  40 value 11.149406
    ## iter  50 value 11.143579
    ## iter  60 value 11.142366
    ## iter  70 value 11.142035
    ## iter  80 value 11.141799
    ## iter  90 value 11.141697
    ## iter 100 value 11.141416
    ## final  value 11.141416
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 49.437553
    ## iter  10 value 19.289974
    ## iter  20 value 13.880458
    ## iter  30 value 11.301239
    ## iter  40 value 11.121558
    ## iter  50 value 11.118869
    ## final  value 11.118850
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 49.437553
    ## iter  10 value 21.191954
    ## iter  20 value 19.814324
    ## iter  30 value 19.790561
    ## final  value 19.790388
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 49.437553
    ## iter  10 value 19.292366
    ## iter  20 value 13.903548
    ## iter  30 value 11.425944
    ## iter  40 value 11.322826
    ## iter  50 value 11.317339
    ## iter  60 value 11.316553
    ## iter  70 value 11.316298
    ## iter  80 value 11.316214
    ## final  value 11.316205
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 50.536165
    ## iter  10 value 20.156305
    ## iter  20 value 14.735867
    ## iter  30 value 11.831093
    ## iter  40 value 11.620552
    ## iter  50 value 11.613573
    ## final  value 11.613560
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 50.536165
    ## iter  10 value 22.063585
    ## iter  20 value 20.729306
    ## iter  30 value 20.668509
    ## final  value 20.668371
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 50.536165
    ## iter  10 value 20.158654
    ## iter  20 value 14.765381
    ## iter  30 value 11.971314
    ## iter  40 value 11.844956
    ## iter  50 value 11.840329
    ## iter  60 value 11.839250
    ## iter  70 value 11.838603
    ## iter  80 value 11.838370
    ## iter  90 value 11.838276
    ## iter 100 value 11.837911
    ## final  value 11.837911
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 51.634778
    ## iter  10 value 20.412456
    ## iter  20 value 14.937104
    ## iter  30 value 11.797706
    ## iter  40 value 11.652522
    ## iter  50 value 11.651038
    ## final  value 11.651030
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 51.634778
    ## iter  10 value 22.257481
    ## iter  20 value 21.009789
    ## iter  30 value 20.985032
    ## final  value 20.985013
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 51.634778
    ## iter  10 value 20.414688
    ## iter  20 value 14.970708
    ## iter  30 value 12.010451
    ## iter  40 value 11.949216
    ## iter  50 value 11.942652
    ## iter  60 value 11.941029
    ## iter  70 value 11.938698
    ## iter  80 value 11.937820
    ## iter  90 value 11.937562
    ## iter 100 value 11.936745
    ## final  value 11.936745
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 52.733390
    ## iter  10 value 21.651621
    ## iter  20 value 15.306520
    ## iter  30 value 11.892949
    ## iter  40 value 11.772970
    ## iter  50 value 11.769375
    ## final  value 11.769364
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 52.733390
    ## iter  10 value 23.742790
    ## iter  20 value 22.579529
    ## iter  30 value 22.540926
    ## final  value 22.540746
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 52.733390
    ## iter  10 value 21.654213
    ## iter  20 value 15.345309
    ## iter  30 value 12.179108
    ## iter  40 value 12.128148
    ## iter  50 value 12.115368
    ## iter  60 value 12.108158
    ## iter  70 value 12.100650
    ## iter  80 value 12.097905
    ## iter  90 value 12.096684
    ## iter 100 value 12.094494
    ## final  value 12.094494
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 53.832002
    ## iter  10 value 22.340244
    ## iter  20 value 16.359095
    ## iter  30 value 12.139305
    ## iter  40 value 11.817298
    ## iter  50 value 11.809152
    ## iter  60 value 11.808806
    ## final  value 11.808806
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 53.832002
    ## iter  10 value 24.303785
    ## iter  20 value 23.011201
    ## iter  30 value 22.970297
    ## final  value 22.970016
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 53.832002
    ## iter  10 value 22.342652
    ## iter  20 value 16.386467
    ## iter  30 value 12.348983
    ## iter  40 value 12.163525
    ## iter  50 value 12.156717
    ## iter  60 value 12.149415
    ## iter  70 value 12.146637
    ## iter  80 value 12.145467
    ## iter  90 value 12.144525
    ## iter 100 value 12.142399
    ## final  value 12.142399
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 54.930614
    ## iter  10 value 24.747579
    ## iter  20 value 20.014322
    ## iter  30 value 14.624277
    ## iter  40 value 13.767716
    ## iter  50 value 13.764565
    ## final  value 13.764559
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 54.930614
    ## iter  10 value 26.653643
    ## iter  20 value 25.777310
    ## iter  30 value 25.747705
    ## final  value 25.747522
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 54.930614
    ## iter  10 value 24.749845
    ## iter  20 value 20.037020
    ## iter  30 value 15.216543
    ## iter  40 value 14.768779
    ## iter  50 value 14.671979
    ## iter  60 value 14.621770
    ## iter  70 value 14.616161
    ## iter  80 value 14.613470
    ## iter  90 value 14.611136
    ## iter 100 value 14.606072
    ## final  value 14.606072
    ## stopped after 100 iterations
    ## # weights:  57 (36 variable)
    ## initial  value 56.029227
    ## iter  10 value 25.602583
    ## iter  20 value 21.172252
    ## iter  30 value 15.651459
    ## iter  40 value 14.651110
    ## iter  50 value 14.647141
    ## final  value 14.647135
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 56.029227
    ## iter  10 value 27.464800
    ## iter  20 value 26.516628
    ## iter  30 value 26.488370
    ## final  value 26.488313
    ## converged
    ## # weights:  57 (36 variable)
    ## initial  value 56.029227
    ## iter  10 value 25.604805
    ## iter  20 value 21.192446
    ## iter  30 value 16.150972
    ## iter  40 value 15.636308
    ## iter  50 value 15.523018
    ## iter  60 value 15.467224
    ## iter  70 value 15.451902
    ## iter  80 value 15.449995
    ## final  value 15.449955
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 57.127839
    ## iter  10 value 26.160156
    ## iter  20 value 21.595250
    ## iter  30 value 15.996774
    ## iter  40 value 15.533228
    ## iter  50 value 15.530795
    ## final  value 15.530789
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 57.127839
    ## iter  10 value 28.060078
    ## iter  20 value 26.956618
    ## iter  30 value 26.920699
    ## final  value 26.920655
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 57.127839
    ## iter  10 value 26.162427
    ## iter  20 value 21.614348
    ## iter  30 value 16.523380
    ## iter  40 value 16.291261
    ## iter  50 value 16.261524
    ## iter  60 value 16.218110
    ## iter  70 value 16.209014
    ## iter  80 value 16.204729
    ## iter  90 value 16.202128
    ## iter 100 value 16.200711
    ## final  value 16.200711
    ## stopped after 100 iterations
    ## # weights:  60 (38 variable)
    ## initial  value 58.226451
    ## iter  10 value 28.766016
    ## iter  20 value 26.376949
    ## iter  30 value 21.359753
    ## iter  40 value 16.744461
    ## iter  50 value 16.702031
    ## final  value 16.701972
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 58.226451
    ## iter  10 value 30.400000
    ## iter  20 value 29.673900
    ## iter  30 value 29.641478
    ## final  value 29.641454
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 58.226451
    ## iter  10 value 28.767951
    ## iter  20 value 26.383817
    ## iter  30 value 21.690202
    ## iter  40 value 18.486677
    ## iter  50 value 18.372082
    ## iter  60 value 18.350160
    ## iter  70 value 18.344323
    ## iter  80 value 18.341392
    ## iter  90 value 18.340810
    ## iter 100 value 18.340351
    ## final  value 18.340351
    ## stopped after 100 iterations
    ## # weights:  60 (38 variable)
    ## initial  value 59.325064
    ## iter  10 value 29.622063
    ## iter  20 value 27.746044
    ## iter  30 value 26.104563
    ## iter  40 value 22.459657
    ## iter  50 value 16.399996
    ## iter  60 value 16.315566
    ## iter  70 value 16.315455
    ## iter  70 value 16.315455
    ## iter  70 value 16.315455
    ## final  value 16.315455
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 59.325064
    ## iter  10 value 31.014227
    ## iter  20 value 30.395658
    ## iter  30 value 30.362778
    ## final  value 30.362761
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 59.325064
    ## iter  10 value 29.624045
    ## iter  20 value 27.750433
    ## iter  30 value 26.187190
    ## iter  40 value 24.530890
    ## iter  50 value 23.990824
    ## iter  60 value 23.232163
    ## iter  70 value 23.215348
    ## final  value 23.183741
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 60.423676
    ## iter  10 value 30.315849
    ## iter  20 value 28.435696
    ## iter  30 value 27.659729
    ## iter  40 value 27.010301
    ## iter  50 value 27.000080
    ## iter  50 value 27.000080
    ## iter  50 value 27.000080
    ## final  value 27.000080
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 60.423676
    ## iter  10 value 31.618657
    ## iter  20 value 30.962416
    ## iter  30 value 30.947265
    ## final  value 30.947221
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 60.423676
    ## iter  10 value 30.318213
    ## iter  20 value 28.439565
    ## iter  30 value 27.675985
    ## iter  40 value 27.128795
    ## final  value 27.122378
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 61.522288
    ## iter  10 value 27.794402
    ## iter  20 value 26.610611
    ## iter  30 value 26.130298
    ## iter  40 value 26.082032
    ## final  value 26.081992
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 61.522288
    ## iter  10 value 30.407978
    ## iter  20 value 29.814812
    ## iter  30 value 29.803591
    ## final  value 29.803471
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 61.522288
    ## iter  10 value 27.797478
    ## iter  20 value 26.616186
    ## iter  30 value 26.140757
    ## iter  40 value 26.093307
    ## final  value 26.093269
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 62.620900
    ## iter  10 value 28.447773
    ## iter  20 value 26.897317
    ## iter  30 value 26.433295
    ## iter  40 value 26.416274
    ## final  value 26.416272
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 62.620900
    ## iter  10 value 30.874180
    ## iter  20 value 30.164311
    ## iter  30 value 30.156286
    ## final  value 30.156268
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 62.620900
    ## iter  10 value 28.451105
    ## iter  20 value 26.902998
    ## iter  30 value 26.441421
    ## iter  40 value 26.424538
    ## final  value 26.424536
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 63.719513
    ## iter  10 value 27.995242
    ## iter  20 value 26.197390
    ## iter  30 value 25.766488
    ## iter  40 value 25.742525
    ## final  value 25.742519
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 63.719513
    ## iter  10 value 30.765638
    ## iter  20 value 29.844046
    ## iter  30 value 29.836011
    ## final  value 29.835986
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 63.719513
    ## iter  10 value 27.998735
    ## iter  20 value 26.203676
    ## iter  30 value 25.776297
    ## iter  40 value 25.752476
    ## final  value 25.752469
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 64.818125
    ## iter  10 value 33.425037
    ## iter  20 value 32.406192
    ## iter  30 value 32.376144
    ## iter  40 value 32.376016
    ## final  value 32.376016
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 64.818125
    ## iter  10 value 35.495602
    ## iter  20 value 34.519829
    ## iter  30 value 34.512070
    ## final  value 34.512061
    ## converged
    ## # weights:  60 (38 variable)
    ## initial  value 64.818125
    ## iter  10 value 33.427553
    ## iter  20 value 32.409084
    ## iter  30 value 32.379236
    ## iter  40 value 32.379109
    ## final  value 32.379108
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 65.916737
    ## iter  10 value 31.461280
    ## iter  20 value 29.301663
    ## iter  30 value 28.728935
    ## iter  40 value 28.726765
    ## final  value 28.726745
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 65.916737
    ## iter  10 value 34.194214
    ## iter  20 value 33.048893
    ## iter  30 value 33.044092
    ## final  value 33.044056
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 65.916737
    ## iter  10 value 31.464693
    ## iter  20 value 29.307948
    ## iter  30 value 28.738775
    ## iter  40 value 28.736629
    ## final  value 28.736609
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 67.015350
    ## iter  10 value 32.612850
    ## iter  20 value 31.199493
    ## iter  30 value 31.022822
    ## iter  40 value 31.020950
    ## final  value 31.020943
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 67.015350
    ## iter  10 value 35.436021
    ## iter  20 value 34.464849
    ## iter  30 value 34.463050
    ## final  value 34.463047
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 67.015350
    ## iter  10 value 32.616355
    ## iter  20 value 31.204499
    ## iter  30 value 31.028761
    ## iter  40 value 31.026905
    ## final  value 31.026898
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 68.113962
    ## iter  10 value 32.648016
    ## iter  20 value 26.890698
    ## iter  30 value 24.081058
    ## iter  40 value 22.832106
    ## iter  50 value 22.605015
    ## iter  60 value 22.557044
    ## iter  70 value 22.555710
    ## final  value 22.555541
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 68.113962
    ## iter  10 value 35.745044
    ## iter  20 value 32.658046
    ## iter  30 value 32.639392
    ## final  value 32.639273
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 68.113962
    ## iter  10 value 32.651812
    ## iter  20 value 26.902154
    ## iter  30 value 24.142020
    ## iter  40 value 23.055048
    ## iter  50 value 22.929719
    ## iter  60 value 22.919818
    ## final  value 22.919778
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 69.212574
    ## iter  10 value 31.330950
    ## iter  20 value 28.243310
    ## iter  30 value 26.253053
    ## iter  40 value 25.917134
    ## iter  50 value 25.913099
    ## final  value 25.913063
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 69.212574
    ## iter  10 value 34.870949
    ## iter  20 value 33.577822
    ## iter  30 value 33.557018
    ## final  value 33.556960
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 69.212574
    ## iter  10 value 31.335296
    ## iter  20 value 28.254894
    ## iter  30 value 26.293411
    ## iter  40 value 25.984200
    ## iter  50 value 25.980356
    ## final  value 25.980331
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 70.311186
    ## iter  10 value 32.020919
    ## iter  20 value 29.022901
    ## iter  30 value 27.407693
    ## iter  40 value 26.974765
    ## iter  50 value 26.965470
    ## final  value 26.965469
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 70.311186
    ## iter  10 value 35.467369
    ## iter  20 value 34.098315
    ## iter  30 value 34.084285
    ## final  value 34.084186
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 70.311186
    ## iter  10 value 32.025126
    ## iter  20 value 29.032091
    ## iter  30 value 27.440568
    ## iter  40 value 27.045724
    ## iter  50 value 27.037866
    ## iter  50 value 27.037866
    ## iter  50 value 27.037866
    ## final  value 27.037866
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 71.409799
    ## iter  10 value 33.713457
    ## iter  20 value 30.338659
    ## iter  30 value 28.549611
    ## iter  40 value 28.006682
    ## iter  50 value 27.997436
    ## final  value 27.997433
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 71.409799
    ## iter  10 value 36.942349
    ## iter  20 value 35.282971
    ## iter  30 value 35.272123
    ## final  value 35.272028
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 71.409799
    ## iter  10 value 33.717384
    ## iter  20 value 30.347152
    ## iter  30 value 28.583576
    ## iter  40 value 28.079173
    ## iter  50 value 28.071449
    ## final  value 28.071447
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 72.508411
    ## iter  10 value 35.801477
    ## iter  20 value 30.524032
    ## iter  30 value 29.159167
    ## iter  40 value 28.629476
    ## iter  50 value 28.612697
    ## final  value 28.612692
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 72.508411
    ## iter  10 value 37.694324
    ## iter  20 value 35.657741
    ## iter  30 value 35.647029
    ## final  value 35.646936
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 72.508411
    ## iter  10 value 35.806385
    ## iter  20 value 30.532660
    ## iter  30 value 29.181661
    ## iter  40 value 28.684857
    ## iter  50 value 28.671221
    ## final  value 28.671219
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 73.607023
    ## iter  10 value 34.730536
    ## iter  20 value 31.322283
    ## iter  30 value 30.154829
    ## iter  40 value 30.039512
    ## final  value 30.038715
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 73.607023
    ## iter  10 value 38.358501
    ## iter  20 value 36.057182
    ## iter  30 value 36.034942
    ## final  value 36.034891
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 73.607023
    ## iter  10 value 34.735066
    ## iter  20 value 31.329865
    ## iter  30 value 30.173239
    ## iter  40 value 30.063155
    ## final  value 30.062409
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 74.705636
    ## iter  10 value 36.866326
    ## iter  20 value 34.563173
    ## iter  30 value 34.001197
    ## final  value 33.998684
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 74.705636
    ## iter  10 value 40.226700
    ## iter  20 value 38.649314
    ## iter  30 value 38.642601
    ## final  value 38.642585
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 74.705636
    ## iter  10 value 36.870302
    ## iter  20 value 34.569582
    ## iter  30 value 34.011222
    ## iter  40 value 34.008733
    ## final  value 34.008721
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 75.804248
    ## iter  10 value 37.049898
    ## iter  20 value 34.899795
    ## iter  30 value 34.402791
    ## iter  40 value 34.398999
    ## final  value 34.398957
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 75.804248
    ## iter  10 value 40.316051
    ## iter  20 value 38.952837
    ## iter  30 value 38.949364
    ## final  value 38.949359
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 75.804248
    ## iter  10 value 37.053809
    ## iter  20 value 34.906094
    ## iter  30 value 34.411926
    ## iter  40 value 34.408195
    ## final  value 34.408154
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 76.902860
    ## iter  10 value 37.989051
    ## iter  20 value 36.227435
    ## iter  30 value 35.890233
    ## iter  40 value 35.886474
    ## final  value 35.886469
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 76.902860
    ## iter  10 value 40.775406
    ## iter  20 value 39.862354
    ## iter  30 value 39.858449
    ## final  value 39.858413
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 76.902860
    ## iter  10 value 37.992358
    ## iter  20 value 36.233030
    ## iter  30 value 35.897446
    ## iter  40 value 35.893736
    ## final  value 35.893731
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 78.001472
    ## iter  10 value 39.121368
    ## iter  20 value 37.824880
    ## iter  30 value 37.584338
    ## iter  40 value 37.582647
    ## final  value 37.582636
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 78.001472
    ## iter  10 value 41.637403
    ## iter  20 value 40.965747
    ## iter  30 value 40.957499
    ## final  value 40.957492
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 78.001472
    ## iter  10 value 39.124310
    ## iter  20 value 37.829451
    ## iter  30 value 37.590044
    ## iter  40 value 37.588341
    ## final  value 37.588331
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 79.100085
    ## iter  10 value 42.062401
    ## iter  20 value 38.193268
    ## iter  30 value 38.024602
    ## iter  40 value 38.021784
    ## final  value 38.021772
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 79.100085
    ## iter  10 value 44.226661
    ## iter  20 value 41.458360
    ## iter  30 value 41.442510
    ## final  value 41.442403
    ## converged
    ## # weights:  63 (40 variable)
    ## initial  value 79.100085
    ## iter  10 value 42.064823
    ## iter  20 value 38.198001
    ## iter  30 value 38.030270
    ## iter  40 value 38.027491
    ## final  value 38.027480
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 80.198697
    ## iter  10 value 40.391388
    ## iter  20 value 38.597700
    ## iter  30 value 38.426767
    ## iter  40 value 38.425743
    ## final  value 38.425737
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 80.198697
    ## iter  10 value 42.924251
    ## iter  20 value 41.681410
    ## iter  30 value 41.675026
    ## final  value 41.674971
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 80.198697
    ## iter  10 value 40.394323
    ## iter  20 value 38.602074
    ## iter  30 value 38.431998
    ## final  value 38.430981
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 81.297309
    ## iter  10 value 43.402926
    ## iter  20 value 41.237084
    ## iter  30 value 41.216418
    ## final  value 41.216277
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 81.297309
    ## iter  10 value 46.194548
    ## iter  20 value 43.861060
    ## iter  30 value 43.784712
    ## final  value 43.784620
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 81.297309
    ## iter  10 value 43.406195
    ## iter  20 value 41.240706
    ## iter  30 value 41.220076
    ## final  value 41.219941
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 82.395922
    ## iter  10 value 44.935063
    ## iter  20 value 42.067324
    ## iter  30 value 42.035241
    ## iter  40 value 42.034638
    ## final  value 42.034635
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 82.395922
    ## iter  10 value 47.253404
    ## iter  20 value 44.586047
    ## iter  30 value 44.487306
    ## final  value 44.487239
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 82.395922
    ## iter  10 value 44.937732
    ## iter  20 value 42.070839
    ## iter  30 value 42.038646
    ## iter  40 value 42.038044
    ## final  value 42.038041
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 83.494534
    ## iter  10 value 43.174518
    ## iter  20 value 40.540034
    ## iter  30 value 40.483205
    ## iter  40 value 40.482577
    ## final  value 40.482572
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 83.494534
    ## iter  10 value 45.924621
    ## iter  20 value 43.479751
    ## iter  30 value 43.400269
    ## final  value 43.400204
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 83.494534
    ## iter  10 value 43.177726
    ## iter  20 value 40.544262
    ## iter  30 value 40.487526
    ## iter  40 value 40.486918
    ## final  value 40.486913
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 84.593146
    ## iter  10 value 48.855551
    ## iter  20 value 46.519286
    ## iter  30 value 46.398488
    ## final  value 46.397924
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 84.593146
    ## iter  10 value 51.180310
    ## iter  20 value 48.328678
    ## iter  30 value 48.211959
    ## final  value 48.211788
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 84.593146
    ## iter  10 value 48.858549
    ## iter  20 value 46.521541
    ## iter  30 value 46.400772
    ## final  value 46.400207
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 85.691759
    ## iter  10 value 49.851299
    ## iter  20 value 47.284190
    ## iter  30 value 47.208386
    ## final  value 47.207610
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 85.691759
    ## iter  10 value 51.897546
    ## iter  20 value 49.225911
    ## iter  30 value 49.092905
    ## final  value 49.092686
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 85.691759
    ## iter  10 value 49.853629
    ## iter  20 value 47.286676
    ## iter  30 value 47.210762
    ## final  value 47.209988
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 86.790371
    ## iter  10 value 52.047150
    ## iter  20 value 48.162435
    ## iter  30 value 47.930068
    ## final  value 47.929005
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 86.790371
    ## iter  10 value 54.581300
    ## iter  20 value 50.174243
    ## iter  30 value 49.867208
    ## final  value 49.866499
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 86.790371
    ## iter  10 value 52.050132
    ## iter  20 value 48.165000
    ## iter  30 value 47.932513
    ## final  value 47.931454
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 87.888983
    ## iter  10 value 50.883386
    ## iter  20 value 48.122237
    ## iter  30 value 47.923883
    ## final  value 47.922969
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 87.888983
    ## iter  10 value 53.517590
    ## iter  20 value 50.143054
    ## iter  30 value 49.889884
    ## final  value 49.889367
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 87.888983
    ## iter  10 value 50.886467
    ## iter  20 value 48.124912
    ## iter  30 value 47.926352
    ## final  value 47.925439
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 88.987595
    ## iter  10 value 52.635809
    ## iter  20 value 49.021978
    ## iter  30 value 48.847313
    ## final  value 48.846014
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 88.987595
    ## iter  10 value 55.457077
    ## iter  20 value 51.048406
    ## iter  30 value 50.809208
    ## final  value 50.808681
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 88.987595
    ## iter  10 value 52.639048
    ## iter  20 value 49.024583
    ## iter  30 value 48.849793
    ## final  value 48.848498
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 90.086208
    ## iter  10 value 53.447784
    ## iter  20 value 50.389896
    ## iter  30 value 50.271302
    ## final  value 50.270778
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 90.086208
    ## iter  10 value 56.299610
    ## iter  20 value 52.473935
    ## iter  30 value 52.231179
    ## final  value 52.230675
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 90.086208
    ## iter  10 value 53.450730
    ## iter  20 value 50.392465
    ## iter  30 value 50.273773
    ## final  value 50.273249
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 91.184820
    ## iter  10 value 59.610651
    ## iter  20 value 53.412898
    ## iter  30 value 53.065538
    ## final  value 53.062626
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 91.184820
    ## iter  10 value 58.641630
    ## iter  20 value 54.891925
    ## iter  30 value 54.706679
    ## final  value 54.706247
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 91.184820
    ## iter  10 value 59.613494
    ## iter  20 value 53.415257
    ## iter  30 value 53.067518
    ## final  value 53.064610
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 92.283432
    ## iter  10 value 58.200594
    ## iter  20 value 53.558980
    ## iter  30 value 53.340174
    ## final  value 53.338732
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 92.283432
    ## iter  10 value 60.582289
    ## iter  20 value 55.352123
    ## iter  30 value 55.011954
    ## final  value 55.011094
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 92.283432
    ## iter  10 value 58.203389
    ## iter  20 value 53.561174
    ## iter  30 value 53.342190
    ## final  value 53.340749
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 93.382045
    ## iter  10 value 58.979607
    ## iter  20 value 54.971165
    ## iter  30 value 54.673820
    ## iter  40 value 54.669223
    ## iter  40 value 54.669223
    ## iter  40 value 54.669223
    ## final  value 54.669223
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 93.382045
    ## iter  10 value 61.028194
    ## iter  20 value 56.710008
    ## iter  30 value 56.396293
    ## final  value 56.394925
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 93.382045
    ## iter  10 value 58.981903
    ## iter  20 value 54.973381
    ## iter  30 value 54.675913
    ## iter  40 value 54.671322
    ## iter  40 value 54.671321
    ## iter  40 value 54.671321
    ## final  value 54.671321
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 94.480657
    ## iter  10 value 59.536977
    ## iter  20 value 56.938568
    ## iter  30 value 56.813185
    ## iter  40 value 56.810411
    ## iter  40 value 56.810410
    ## iter  40 value 56.810410
    ## final  value 56.810410
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 94.480657
    ## iter  10 value 61.488132
    ## iter  20 value 58.804698
    ## iter  30 value 58.463322
    ## iter  40 value 58.460586
    ## iter  40 value 58.460586
    ## iter  40 value 58.460586
    ## final  value 58.460586
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 94.480657
    ## iter  10 value 59.539143
    ## iter  20 value 56.940650
    ## iter  30 value 56.815175
    ## iter  40 value 56.812404
    ## iter  40 value 56.812403
    ## iter  40 value 56.812403
    ## final  value 56.812403
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 95.579269
    ## iter  10 value 60.107676
    ## iter  20 value 56.981331
    ## iter  30 value 56.830854
    ## final  value 56.828437
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 95.579269
    ## iter  10 value 62.146540
    ## iter  20 value 58.800520
    ## iter  30 value 58.512298
    ## iter  40 value 58.510496
    ## iter  40 value 58.510496
    ## iter  40 value 58.510496
    ## final  value 58.510496
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 95.579269
    ## iter  10 value 60.109942
    ## iter  20 value 56.983487
    ## iter  30 value 56.832884
    ## final  value 56.830472
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 96.677881
    ## iter  10 value 64.074155
    ## iter  20 value 59.178433
    ## iter  30 value 58.934629
    ## final  value 58.932628
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 96.677881
    ## iter  10 value 65.768129
    ## iter  20 value 60.644341
    ## iter  30 value 60.358592
    ## final  value 60.357136
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 96.677881
    ## iter  10 value 64.076025
    ## iter  20 value 59.180107
    ## iter  30 value 58.936289
    ## final  value 58.934291
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 97.776494
    ## iter  10 value 66.546887
    ## iter  20 value 60.791408
    ## iter  30 value 60.434768
    ## iter  40 value 60.433358
    ## final  value 60.433357
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 97.776494
    ## iter  10 value 67.081788
    ## iter  20 value 62.176227
    ## iter  30 value 61.861120
    ## iter  40 value 61.859656
    ## final  value 61.859655
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 97.776494
    ## iter  10 value 66.547449
    ## iter  20 value 60.792943
    ## iter  30 value 60.436430
    ## iter  40 value 60.435022
    ## final  value 60.435021
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 98.875106
    ## iter  10 value 67.614034
    ## iter  20 value 62.856680
    ## iter  30 value 62.617299
    ## iter  40 value 62.616088
    ## iter  40 value 62.616087
    ## iter  40 value 62.616087
    ## final  value 62.616087
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 98.875106
    ## iter  10 value 68.245275
    ## iter  20 value 64.050078
    ## iter  30 value 63.877940
    ## final  value 63.877064
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 98.875106
    ## iter  10 value 67.614703
    ## iter  20 value 62.858029
    ## iter  30 value 62.618755
    ## iter  40 value 62.617545
    ## iter  40 value 62.617545
    ## iter  40 value 62.617545
    ## final  value 62.617545
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 99.973718
    ## iter  10 value 68.109369
    ## iter  20 value 63.387049
    ## iter  30 value 63.104852
    ## iter  40 value 63.103439
    ## iter  40 value 63.103439
    ## iter  40 value 63.103439
    ## final  value 63.103439
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 99.973718
    ## iter  10 value 68.739498
    ## iter  20 value 64.606148
    ## iter  30 value 64.338907
    ## final  value 64.337960
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 99.973718
    ## iter  10 value 68.110036
    ## iter  20 value 63.388421
    ## iter  30 value 63.106274
    ## iter  40 value 63.104859
    ## iter  40 value 63.104858
    ## iter  40 value 63.104858
    ## final  value 63.104858
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 101.072331
    ## iter  10 value 68.698505
    ## iter  20 value 63.632788
    ## iter  30 value 63.410686
    ## iter  40 value 63.408307
    ## final  value 63.408306
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 101.072331
    ## iter  10 value 69.346571
    ## iter  20 value 64.893954
    ## iter  30 value 64.659753
    ## final  value 64.658407
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 101.072331
    ## iter  10 value 68.699192
    ## iter  20 value 63.634220
    ## iter  30 value 63.412116
    ## iter  40 value 63.409739
    ## final  value 63.409738
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 102.170943
    ## iter  10 value 68.076998
    ## iter  20 value 64.622912
    ## iter  30 value 64.154144
    ## final  value 64.152396
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 102.170943
    ## iter  10 value 69.859738
    ## iter  20 value 65.999356
    ## iter  30 value 65.443480
    ## final  value 65.441990
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 102.170943
    ## iter  10 value 68.078974
    ## iter  20 value 64.624616
    ## iter  30 value 64.155623
    ## final  value 64.153877
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 103.269555
    ## iter  10 value 70.386377
    ## iter  20 value 65.318159
    ## iter  30 value 64.862659
    ## final  value 64.860146
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 103.269555
    ## iter  10 value 72.493034
    ## iter  20 value 66.811384
    ## iter  30 value 66.214441
    ## iter  40 value 66.210551
    ## final  value 66.210550
    ## converged
    ## # weights:  66 (42 variable)
    ## initial  value 103.269555
    ## iter  10 value 70.388718
    ## iter  20 value 65.319715
    ## iter  30 value 64.864215
    ## final  value 64.861702
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 104.368167
    ## iter  10 value 69.178963
    ## iter  20 value 63.614255
    ## iter  30 value 63.316946
    ## iter  40 value 63.315421
    ## final  value 63.315419
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 104.368167
    ## iter  10 value 69.956983
    ## iter  20 value 65.076262
    ## iter  30 value 64.827404
    ## iter  40 value 64.824775
    ## final  value 64.824774
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 104.368167
    ## iter  10 value 69.179788
    ## iter  20 value 63.615887
    ## iter  30 value 63.318698
    ## iter  40 value 63.317169
    ## final  value 63.317168
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 105.466780
    ## iter  10 value 68.575125
    ## iter  20 value 62.715205
    ## iter  30 value 62.503009
    ## iter  40 value 62.501208
    ## iter  40 value 62.501208
    ## iter  40 value 62.501207
    ## final  value 62.501207
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 105.466780
    ## iter  10 value 69.376636
    ## iter  20 value 64.282180
    ## iter  30 value 64.099984
    ## final  value 64.098695
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 105.466780
    ## iter  10 value 68.575973
    ## iter  20 value 62.716994
    ## iter  30 value 62.504856
    ## iter  40 value 62.503055
    ## iter  40 value 62.503055
    ## iter  40 value 62.503055
    ## final  value 62.503055
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 106.565392
    ## iter  10 value 71.245512
    ## iter  20 value 66.340009
    ## iter  30 value 65.956556
    ## iter  40 value 65.953262
    ## final  value 65.953260
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 106.565392
    ## iter  10 value 71.994810
    ## iter  20 value 67.671553
    ## iter  30 value 67.391831
    ## iter  40 value 67.387523
    ## final  value 67.387521
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 106.565392
    ## iter  10 value 71.246303
    ## iter  20 value 66.341520
    ## iter  30 value 65.958206
    ## iter  40 value 65.954912
    ## final  value 65.954910
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 107.664004
    ## iter  10 value 72.148258
    ## iter  20 value 67.239296
    ## iter  30 value 67.089479
    ## iter  40 value 67.086364
    ## iter  40 value 67.086363
    ## iter  40 value 67.086363
    ## final  value 67.086363
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 107.664004
    ## iter  10 value 72.865265
    ## iter  20 value 68.520843
    ## iter  30 value 68.386373
    ## iter  40 value 68.382114
    ## final  value 68.382112
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 107.664004
    ## iter  10 value 72.149012
    ## iter  20 value 67.240731
    ## iter  30 value 67.090937
    ## iter  40 value 67.087820
    ## iter  40 value 67.087819
    ## iter  40 value 67.087819
    ## final  value 67.087819
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 108.762617
    ## iter  10 value 72.315938
    ## iter  20 value 67.356585
    ## iter  30 value 67.058340
    ## iter  40 value 67.054924
    ## iter  40 value 67.054923
    ## iter  40 value 67.054923
    ## final  value 67.054923
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 108.762617
    ## iter  10 value 73.043506
    ## iter  20 value 68.621163
    ## iter  30 value 68.364695
    ## iter  40 value 68.361749
    ## iter  40 value 68.361748
    ## iter  40 value 68.361748
    ## final  value 68.361748
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 108.762617
    ## iter  10 value 72.316704
    ## iter  20 value 67.357987
    ## iter  30 value 67.059809
    ## iter  40 value 67.056392
    ## iter  40 value 67.056392
    ## iter  40 value 67.056391
    ## final  value 67.056391
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 109.861229
    ## iter  10 value 73.241830
    ## iter  20 value 68.173685
    ## iter  30 value 67.888213
    ## iter  40 value 67.883045
    ## final  value 67.883043
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 109.861229
    ## iter  10 value 73.972386
    ## iter  20 value 69.438094
    ## iter  30 value 69.213174
    ## iter  40 value 69.206274
    ## final  value 69.206273
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 109.861229
    ## iter  10 value 73.242599
    ## iter  20 value 68.175084
    ## iter  30 value 67.889703
    ## iter  40 value 67.884534
    ## final  value 67.884532
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 110.959841
    ## iter  10 value 73.808390
    ## iter  20 value 68.493362
    ## iter  30 value 67.968255
    ## iter  40 value 67.963330
    ## iter  40 value 67.963330
    ## iter  40 value 67.963330
    ## final  value 67.963330
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 110.959841
    ## iter  10 value 74.383802
    ## iter  20 value 69.533595
    ## iter  30 value 69.308720
    ## iter  40 value 69.304411
    ## final  value 69.304409
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 110.959841
    ## iter  10 value 73.810465
    ## iter  20 value 68.494900
    ## iter  30 value 67.969741
    ## iter  40 value 67.964840
    ## iter  40 value 67.964840
    ## iter  40 value 67.964839
    ## final  value 67.964839
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 112.058453
    ## iter  10 value 77.543206
    ## iter  20 value 71.012220
    ## iter  30 value 70.432920
    ## iter  40 value 70.421104
    ## final  value 70.421100
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 112.058453
    ## iter  10 value 79.243119
    ## iter  20 value 72.305124
    ## iter  30 value 71.587261
    ## iter  40 value 71.568780
    ## final  value 71.568772
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 112.058453
    ## iter  10 value 77.545083
    ## iter  20 value 71.013668
    ## iter  30 value 70.434193
    ## iter  40 value 70.422370
    ## final  value 70.422365
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 113.157066
    ## iter  10 value 78.429495
    ## iter  20 value 71.664776
    ## iter  30 value 71.273878
    ## iter  40 value 71.269351
    ## final  value 71.269349
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 113.157066
    ## iter  10 value 80.036879
    ## iter  20 value 72.774051
    ## iter  30 value 72.379860
    ## iter  40 value 72.374311
    ## final  value 72.374310
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 113.157066
    ## iter  10 value 78.431266
    ## iter  20 value 71.665977
    ## iter  30 value 71.275089
    ## iter  40 value 71.270563
    ## final  value 71.270561
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 114.255678
    ## iter  10 value 79.684957
    ## iter  20 value 74.344229
    ## iter  30 value 73.318536
    ## iter  40 value 73.307526
    ## final  value 73.307518
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 114.255678
    ## iter  10 value 81.404862
    ## iter  20 value 75.675273
    ## iter  30 value 74.404460
    ## iter  40 value 74.371000
    ## final  value 74.370979
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 114.255678
    ## iter  10 value 79.686847
    ## iter  20 value 74.345552
    ## iter  30 value 73.319710
    ## iter  40 value 73.308694
    ## final  value 73.308686
    ## converged
    ## # weights:  69 (44 variable)
    ## initial  value 115.354290
    ## iter  10 value 82.109431
    ## iter  20 value 76.114346
    ## iter  30 value 74.888833
    ## iter  40 value 74.854966
    ## final  value 74.854948
    ## converged

``` r
Predictions1 <- predict(samp_mult_log,Samp_validation)
confusionMatrix(Predictions1, as.factor(Samp_validation$outcome),mode = "prec_recall")
```

    ## Confusion Matrix and Statistics
    ##
    ##           Reference
    ## Prediction  D  L  W
    ##          D  0  0  0
    ##          L  2  3  0
    ##          W  6 11 13
    ##
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.4571          
    ##                  95% CI : (0.2883, 0.6335)
    ##     No Information Rate : 0.4             
    ##     P-Value [Acc > NIR] : 0.2997392       
    ##                                           
    ##                   Kappa : 0.1307          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.0002734       
    ##
    ## Statistics by Class:
    ##
    ##                      Class: D Class: L Class: W
    ## Precision                  NA  0.60000   0.4333
    ## Recall                 0.0000  0.21429   1.0000
    ## F1                         NA  0.31579   0.6047
    ## Prevalence             0.2286  0.40000   0.3714
    ## Detection Rate         0.0000  0.08571   0.3714
    ## Detection Prevalence   0.0000  0.14286   0.8571
    ## Balanced Accuracy      0.5000  0.55952   0.6136

``` r
Predictions1_p <- predict(samp_mult_log,Samp_validation,type = "prob")
Predictions1_p <- round(Predictions1_p,5)
```

#### SVM

``` r
samp_svm = train(
  outcome ~ .,
  data = Samp_train,
  method = "svmLinear",
  preProc = c("pca"),
  trControl = myTimeControl
)

Predictions2 <- predict(samp_svm,Samp_validation)
confusionMatrix(Predictions2, as.factor(Samp_validation$outcome),mode = "prec_recall")
```

    ## Confusion Matrix and Statistics
    ##
    ##           Reference
    ## Prediction D L W
    ##          D 3 4 4
    ##          L 1 1 0
    ##          W 4 9 9
    ##
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.3714          
    ##                  95% CI : (0.2147, 0.5508)
    ##     No Information Rate : 0.4             
    ##     P-Value [Acc > NIR] : 0.69427         
    ##                                           
    ##                   Kappa : 0.0644          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.01286         
    ##
    ## Statistics by Class:
    ##
    ##                      Class: D Class: L Class: W
    ## Precision             0.27273  0.50000   0.4091
    ## Recall                0.37500  0.07143   0.6923
    ## F1                    0.31579  0.12500   0.5143
    ## Prevalence            0.22857  0.40000   0.3714
    ## Detection Rate        0.08571  0.02857   0.2571
    ## Detection Prevalence  0.31429  0.05714   0.6286
    ## Balanced Accuracy     0.53935  0.51190   0.5507

#### Random Forest

``` r
samp_rf = train(
  outcome ~ .,
  data = Samp_train,
  method = "ranger",
  preProc = c("pca"),
  trControl = myTimeControl
)

Predictions3 <- predict(samp_rf,Samp_validation)
confusionMatrix(Predictions3, as.factor(Samp_validation$outcome),mode = "prec_recall")
```

#### Naive-Bayes

``` r
samp_nb = train(
  outcome ~ .,
  data = Samp_train,
  method = "naive_bayes",
  preProc = c("pca"),
  trControl = myTimeControl
)

Predictions4 <- predict(samp_nb,Samp_validation)
confusionMatrix(Predictions4, as.factor(Samp_validation$outcome),mode = "prec_recall")
```

    ## Confusion Matrix and Statistics
    ##
    ##           Reference
    ## Prediction  D  L  W
    ##          D  8 13 13
    ##          L  0  1  0
    ##          W  0  0  0
    ##
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.2571          
    ##                  95% CI : (0.1249, 0.4326)
    ##     No Information Rate : 0.4             
    ##     P-Value [Acc > NIR] : 0.974           
    ##                                           
    ##                   Kappa : 0.0309          
    ##                                           
    ##  Mcnemar's Test P-Value : NA              
    ##
    ## Statistics by Class:
    ##
    ##                      Class: D Class: L Class: W
    ## Precision              0.2353  1.00000       NA
    ## Recall                 1.0000  0.07143   0.0000
    ## F1                     0.3810  0.13333       NA
    ## Prevalence             0.2286  0.40000   0.3714
    ## Detection Rate         0.2286  0.02857   0.0000
    ## Detection Prevalence   0.9714  0.02857   0.0000
    ## Balanced Accuracy      0.5185  0.53571   0.5000

#### Ensemble

#### Results

------------------------------------------------------------------------

### 5. Overall Results

### 6. Conclusion and Next Steps

-   Include xG data

    -   Find a way to account for class imbalance

    -   Add player-level data

    -   Try a generalizable model
