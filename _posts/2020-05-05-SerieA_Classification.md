---
title: "Predicting Soccer Match Outcomes using caret"
categories: [R, caret]
excerpt: "A collection of attempts to predict outcomes of Italian soccer matches from 2015-2020 using the caret package. Predictions are compared against historical betting odds to determine if a profitable betting strategy  can be derived using various classification algorithms"
toc: true
toc_label: "Content"
toc_sticky: true
toc_icon: 'futbol'
---


![Stadio Olimpico](/assets/images/remi-jacquaint.jpg){: .align-center}

### Predicting Soccer Match Outcomes using caret

### 1. Objective

The goal of this post is to try and predict the outcome of soccer
matches in the Italian top-flight division, *Serie A*. There are three
possible outcomes for each match: a home win, an away win, or a draw. To
make things more interesting, the predicted outcomes and their
associated probabilities will be compared to historical odds offered by
bookmakers in Europe which were gathered from
<https://www.football-data.co.uk>.

As an example, the odds given for a single match between Parma and
Juventus in August, 2019 are listed below along with those same odds
converted to probabilities. These odds were offered by the company
Bet365.

<table>
<caption>
Example Odds from Bet365
</caption>
<thead>
<tr>
<th style="text-align:left;">
Date
</th>
<th style="text-align:left;">
HomeTeam
</th>
<th style="text-align:left;">
AwayTeam
</th>
<th style="text-align:right;">
B365H
</th>
<th style="text-align:right;">
B365A
</th>
<th style="text-align:right;">
B365D
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
24/08/2019
</td>
<td style="text-align:left;">
Parma
</td>
<td style="text-align:left;">
Juventus
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
1.33
</td>
<td style="text-align:right;">
5.25
</td>
</tr>
</tbody>
</table>
<table>
<caption>
Converted to Probabilities
</caption>
<thead>
<tr>
<th style="text-align:left;">
Date
</th>
<th style="text-align:left;">
HomeTeam
</th>
<th style="text-align:left;">
AwayTeam
</th>
<th style="text-align:right;">
B365H
</th>
<th style="text-align:right;">
B365A
</th>
<th style="text-align:right;">
B365D
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
24/08/2019
</td>
<td style="text-align:left;">
Parma
</td>
<td style="text-align:left;">
Juventus
</td>
<td style="text-align:right;">
0.1111111
</td>
<td style="text-align:right;">
0.7518797
</td>
<td style="text-align:right;">
0.1904762
</td>
</tr>
</tbody>
</table>
Note that the sum of the three probabilities is equal to 1.0534, so the
odds offered by Bet365 are not true odds. This practice is standard
among all odds offered by bookmakers. The ‘extra’ 5.34% is known as “the
vig” and helps bookmakers ensure a profit across all the matches on
which they’re offering odds. Wikipedia has a better explanation of how
the vig plays out
[here](https://en.wikipedia.org/wiki/Vigorish#The_simplest_wager).

------------------------------------------------------------------------

### 2. Gathering Data

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

### 3. Processing the Data

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

### 4. Feature Engineering

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

![](SerieA_Blog_Post_files/figure-markdown_github/Feature%20Selection%20using%20Random%20Forest-1.png)

#### Feature Extraction with PCA

Even after removing 10 features from the dataset, there are still a
large number of predictors for each match’s outcome. To reduce the
number of features while maximizing the amount of variation still
explained, principal components analysis was applied as a
[pre-processing
technique](https://topepo.github.io/caret/pre-processing.html#transforming-predictors)
in caret.

------------------------------------------------------------------------

### 5. Distribution of Outcomes by Team

It’s worthwhile to point out that the distribution of outcomes is
naturally different by team. Dominant teams like Juventus, Napoli, Roma,
and Inter Milan all have win percentages over 50%. This class imbalance
has consequences for the models built. However, for the example below,
we’ll focus on Sampdoria which has a relatively balanced distribution of
outcomes for seasons 2015-16 - 2018-19: 34.8% Win, 23.6%, Loss 41.4%.

![](SerieA_Blog_Post_files/figure-markdown_github/outcome_viz-1.png)

### 6. Illustrative Example with U.C Sampdoria

The records for Sampdoria have been broken apart into two datasets:
`Samp_train` with records from the “2015-16”,“2016-17”,“2017-18”, and
“2018-19” seasons and `Samp_test` which has records from the
’2019-20`season. Each dataset has been scrubbed of the first three records from each season as they do not have lagged average values for the various features. Various models will be trained on the`Samp\_train`set and then tested individually and in an ensemble on the`Samp\_holdout\`
set.

#### Multinomial Logistic Regression

The multinomial logistic regression approach is done using the ‘nnet’
package with the decay hyperparamter optimized on the basis of logLoss.

Below you can see the summary of the model run, the confusion matrix is
printed for a quick evaluation of this model.

-   The overall test accuracy of the model is 42.86% *(True
    positives+True negaives/N)*
-   Precision among the classes is 25% for draws, 66.7% for losses, and
    42.8% for wins. This is a key metric because precision measures the
    number of correct predictions by class (e.g. of the 8 draws we
    predicted, 2 were correctly predicted)
-   Of equal importance is the recall which identifies the proportion of
    true cases that were correctly identified. In the case of our model,
    recall is 40% for draws, 40% for losses, and 50% for wins. So based
    upon our test set, the mode model is slightly better at predicting
    wins than losses or draws.

<!-- -->

    ## Penalized Multinomial Regression
    ##
    ## 140 samples
    ##  31 predictor
    ##   3 classes: 'D', 'L', 'W'
    ##
    ## Pre-processing: principal component signal extraction (31), centered
    ##  (31), scaled (31)
    ## Resampling: Rolling Forecasting Origin Resampling (1 held-out with no fixed window)
    ## Summary of sample sizes: 10, 11, 12, 13, 14, 15, ...
    ## Resampling results across tuning parameters:
    ##
    ##   decay  logLoss
    ##   0e+00  6.806787
    ##   1e-04  4.632432
    ##   1e-01  1.921418
    ##
    ## logLoss was used to select the optimal model using the smallest value.
    ## The final value used for the model was decay = 0.1.

    ## Confusion Matrix and Statistics
    ##
    ##           Reference
    ## Prediction D L W
    ##          D 2 3 3
    ##          L 2 4 0
    ##          W 1 3 3
    ##
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.4286          
    ##                  95% CI : (0.2182, 0.6598)
    ##     No Information Rate : 0.4762          
    ##     P-Value [Acc > NIR] : 0.7427          
    ##                                           
    ##                   Kappa : 0.1572          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.2407          
    ##
    ## Statistics by Class:
    ##
    ##                      Class: D Class: L Class: W
    ## Precision             0.25000   0.6667   0.4286
    ## Recall                0.40000   0.4000   0.5000
    ## F1                    0.30769   0.5000   0.4615
    ## Prevalence            0.23810   0.4762   0.2857
    ## Detection Rate        0.09524   0.1905   0.1429
    ## Detection Prevalence  0.38095   0.2857   0.3333
    ## Balanced Accuracy     0.51250   0.6091   0.6167

Also, the individual predictions and associated probabilities for
Sampdoria’s first 21 matches in the 2019-20 season:

    ##    Actual Pred Accuracy          D          L          W
    ## 1       W    W        1 0.06887713 0.44063949 0.49048339
    ## 2       L    D        0 0.36585936 0.33011890 0.30402174
    ## 3       L    L        1 0.18971648 0.66795494 0.14232857
    ## 4       L    D        0 0.74400260 0.10408880 0.15190860
    ## 5       D    D        1 0.43397850 0.14467471 0.42134679
    ## 6       L    W        0 0.34001507 0.19228764 0.46769729
    ## 7       D    W        0 0.16928031 0.15655922 0.67416048
    ## 8       W    D        0 0.68714465 0.09674955 0.21610580
    ## 9       D    L        0 0.06354499 0.73351542 0.20293958
    ## 10      W    W        1 0.29975104 0.07681729 0.62343167
    ## 11      L    L        1 0.03806716 0.81198931 0.14994354
    ## 12      L    W        0 0.18501137 0.10127750 0.71371112
    ## 13      W    D        0 0.85456378 0.04731457 0.09812165
    ## 14      L    L        1 0.03189414 0.95813745 0.00996841
    ## 15      D    L        0 0.17795686 0.75893398 0.06310916
    ## 16      W    W        1 0.35362009 0.22578018 0.42059974
    ## 17      L    L        1 0.27087522 0.64390458 0.08522020
    ## 18      D    D        1 0.65329225 0.30309618 0.04361158
    ## 19      L    D        0 0.47724780 0.21781474 0.30493746
    ## 20      W    D        0 0.42689824 0.35668860 0.21641315
    ## 21      L    W        0 0.34192660 0.12450207 0.53357133

#### SVM

The second model tried out is a support vector machine from the
‘kernlab’ package.

-   The overall test accuracy of the model is 42.86%
-   Precision among the classes is 25% for draws, 66.7% for losses, and
    42.8% for wins.
-   Of equal importance is the recall which identifies the proportion of
    true cases that were correctly identified. In the case of our model,
    recall is 40% for draws, 40% for losses, and 50% for wins. So based
    upon our test set, the mode model is slightly better at predicting
    wins than losses or draws.

<!-- -->

    ## Penalized Multinomial Regression
    ##
    ## 140 samples
    ##  31 predictor
    ##   3 classes: 'D', 'L', 'W'
    ##
    ## Pre-processing: principal component signal extraction (31), centered
    ##  (31), scaled (31)
    ## Resampling: Rolling Forecasting Origin Resampling (1 held-out with no fixed window)
    ## Summary of sample sizes: 10, 11, 12, 13, 14, 15, ...
    ## Resampling results across tuning parameters:
    ##
    ##   decay  logLoss
    ##   0e+00  6.806787
    ##   1e-04  4.632432
    ##   1e-01  1.921418
    ##
    ## logLoss was used to select the optimal model using the smallest value.
    ## The final value used for the model was decay = 0.1.

    ## Confusion Matrix and Statistics
    ##
    ##           Reference
    ## Prediction D L W
    ##          D 2 3 3
    ##          L 2 4 0
    ##          W 1 3 3
    ##
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.4286          
    ##                  95% CI : (0.2182, 0.6598)
    ##     No Information Rate : 0.4762          
    ##     P-Value [Acc > NIR] : 0.7427          
    ##                                           
    ##                   Kappa : 0.1572          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.2407          
    ##
    ## Statistics by Class:
    ##
    ##                      Class: D Class: L Class: W
    ## Precision             0.25000   0.6667   0.4286
    ## Recall                0.40000   0.4000   0.5000
    ## F1                    0.30769   0.5000   0.4615
    ## Prevalence            0.23810   0.4762   0.2857
    ## Detection Rate        0.09524   0.1905   0.1429
    ## Detection Prevalence  0.38095   0.2857   0.3333
    ## Balanced Accuracy     0.51250   0.6091   0.6167

``` r
svm_pred_prob <- predict(svm_mod,Samp_test,type="prob")

svm_pred_out <- cbind(svm_pred_prob,Samp_test$outcome,svm_pred) %>% as.data.frame()

names(svm_pred_out) <- c("D","L","W","Actual","Pred")

svm_pred_out %>%
  mutate(Accuracy=ifelse(Actual==Pred,1,0)) %>%
  select(Actual,Pred,Accuracy,D,L,W)
```

    ##    Actual Pred Accuracy          D          L          W
    ## 1       W    W        1 0.06887713 0.44063949 0.49048339
    ## 2       L    D        0 0.36585936 0.33011890 0.30402174
    ## 3       L    L        1 0.18971648 0.66795494 0.14232857
    ## 4       L    D        0 0.74400260 0.10408880 0.15190860
    ## 5       D    D        1 0.43397850 0.14467471 0.42134679
    ## 6       L    W        0 0.34001507 0.19228764 0.46769729
    ## 7       D    W        0 0.16928031 0.15655922 0.67416048
    ## 8       W    D        0 0.68714465 0.09674955 0.21610580
    ## 9       D    L        0 0.06354499 0.73351542 0.20293958
    ## 10      W    W        1 0.29975104 0.07681729 0.62343167
    ## 11      L    L        1 0.03806716 0.81198931 0.14994354
    ## 12      L    W        0 0.18501137 0.10127750 0.71371112
    ## 13      W    D        0 0.85456378 0.04731457 0.09812165
    ## 14      L    L        1 0.03189414 0.95813745 0.00996841
    ## 15      D    L        0 0.17795686 0.75893398 0.06310916
    ## 16      W    W        1 0.35362009 0.22578018 0.42059974
    ## 17      L    L        1 0.27087522 0.64390458 0.08522020
    ## 18      D    D        1 0.65329225 0.30309618 0.04361158
    ## 19      L    D        0 0.47724780 0.21781474 0.30493746
    ## 20      W    D        0 0.42689824 0.35668860 0.21641315
    ## 21      L    W        0 0.34192660 0.12450207 0.53357133

#### Random Forest

The random forest model is impemented with the ‘ranger’ package and
tuned on the following hyper parameters:

-   *Number of Randomly Selected Predictors (mtry, numeric)*

-   *Splitting Rule (splitrule, character)*

-   *Minimal Node Size (min.node.size, numeric)*

-   The overall test accuracy of the random forest model is equivalent
    to the multinomial logistic model: 42.86%\_
-   Precision among the classes is 0% for draws, 50% for losses, and 40%
    for wins.
-   Finally, recall is 0% for draws, 30% for losses, and 100% for wins.
    So based upon our test set, the mode model is slightly better at
    predicting wins than losses or draws.

<!-- -->

    ## Random Forest
    ##
    ## 140 samples
    ##  31 predictor
    ##   3 classes: 'D', 'L', 'W'
    ##
    ## Pre-processing: principal component signal extraction (31), centered
    ##  (31), scaled (31)
    ## Resampling: Rolling Forecasting Origin Resampling (1 held-out with no fixed window)
    ## Summary of sample sizes: 10, 11, 12, 13, 14, 15, ...
    ## Resampling results across tuning parameters:
    ##
    ##   mtry  splitrule   min.node.size  logLoss
    ##    4    gini        1              1.078672
    ##    4    gini        2              1.065674
    ##    4    gini        3              1.073515
    ##    4    extratrees  1              1.083181
    ##    4    extratrees  2              1.071212
    ##    4    extratrees  3              1.082491
    ##    4    hellinger   1                   NaN
    ##    4    hellinger   2                   NaN
    ##    4    hellinger   3                   NaN
    ##    5    gini        1              1.073001
    ##    5    gini        2              1.071911
    ##    5    gini        3              1.070481
    ##    5    extratrees  1              1.087030
    ##    5    extratrees  2              1.078439
    ##    5    extratrees  3              1.072105
    ##    5    hellinger   1                   NaN
    ##    5    hellinger   2                   NaN
    ##    5    hellinger   3                   NaN
    ##    6    gini        1              1.075395
    ##    6    gini        2              1.071450
    ##    6    gini        3              1.074042
    ##    6    extratrees  1              1.074971
    ##    6    extratrees  2              1.071136
    ##    6    extratrees  3              1.073233
    ##    6    hellinger   1                   NaN
    ##    6    hellinger   2                   NaN
    ##    6    hellinger   3                   NaN
    ##    7    gini        1              1.074103
    ##    7    gini        2              1.079132
    ##    7    gini        3              1.070184
    ##    7    extratrees  1              1.069047
    ##    7    extratrees  2              1.078071
    ##    7    extratrees  3              1.077845
    ##    7    hellinger   1                   NaN
    ##    7    hellinger   2                   NaN
    ##    7    hellinger   3                   NaN
    ##    8    gini        1              1.077058
    ##    8    gini        2              1.077644
    ##    8    gini        3              1.092078
    ##    8    extratrees  1              1.077891
    ##    8    extratrees  2              1.066978
    ##    8    extratrees  3              1.079173
    ##    8    hellinger   1                   NaN
    ##    8    hellinger   2                   NaN
    ##    8    hellinger   3                   NaN
    ##    9    gini        1              1.080256
    ##    9    gini        2              1.080160
    ##    9    gini        3              1.077094
    ##    9    extratrees  1              1.078441
    ##    9    extratrees  2              1.074952
    ##    9    extratrees  3              1.075924
    ##    9    hellinger   1                   NaN
    ##    9    hellinger   2                   NaN
    ##    9    hellinger   3                   NaN
    ##   10    gini        1              1.073616
    ##   10    gini        2              1.071328
    ##   10    gini        3              1.081807
    ##   10    extratrees  1              1.069929
    ##   10    extratrees  2              1.057677
    ##   10    extratrees  3              1.062313
    ##   10    hellinger   1                   NaN
    ##   10    hellinger   2                   NaN
    ##   10    hellinger   3                   NaN
    ##
    ## logLoss was used to select the optimal model using the smallest value.
    ## The final values used for the model were mtry = 10, splitrule = extratrees
    ##  and min.node.size = 2.

    ## Confusion Matrix and Statistics
    ##
    ##           Reference
    ## Prediction D L W
    ##          D 0 0 1
    ##          L 3 4 1
    ##          W 2 6 4
    ##
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.381           
    ##                  95% CI : (0.1811, 0.6156)
    ##     No Information Rate : 0.4762          
    ##     P-Value [Acc > NIR] : 0.8629          
    ##                                           
    ##                   Kappa : 0.0387          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.0750          
    ##
    ## Statistics by Class:
    ##
    ##                      Class: D Class: L Class: W
    ## Precision             0.00000   0.5000   0.3333
    ## Recall                0.00000   0.4000   0.6667
    ## F1                        NaN   0.4444   0.4444
    ## Prevalence            0.23810   0.4762   0.2857
    ## Detection Rate        0.00000   0.1905   0.1905
    ## Detection Prevalence  0.04762   0.3810   0.5714
    ## Balanced Accuracy     0.46875   0.5182   0.5667

In general, the random forest over-estimates wins, and doesn’t predict a
single draw.

    ##    Actual Pred Accuracy     D     L     W
    ## 1       W    W        1 0.138 0.368 0.494
    ## 2       L    W        0 0.245 0.282 0.473
    ## 3       L    L        1 0.183 0.422 0.395
    ## 4       L    W        0 0.346 0.284 0.370
    ## 5       D    W        0 0.210 0.394 0.396
    ## 6       L    W        0 0.246 0.335 0.419
    ## 7       D    W        0 0.220 0.276 0.504
    ## 8       W    D        0 0.357 0.297 0.346
    ## 9       D    L        0 0.157 0.503 0.340
    ## 10      W    W        1 0.234 0.274 0.492
    ## 11      L    L        1 0.151 0.559 0.290
    ## 12      L    W        0 0.203 0.344 0.453
    ## 13      W    L        0 0.311 0.348 0.341
    ## 14      L    L        1 0.254 0.528 0.218
    ## 15      D    L        0 0.297 0.389 0.314
    ## 16      W    W        1 0.264 0.284 0.452
    ## 17      L    L        1 0.178 0.466 0.356
    ## 18      D    L        0 0.224 0.502 0.274
    ## 19      L    W        0 0.273 0.346 0.381
    ## 20      W    W        1 0.214 0.328 0.458
    ## 21      L    W        0 0.286 0.287 0.427

#### Ensemble

In statistics and machine learning, ensemble methods use multiple
learning algorithms to obtain better predictive performance than could
be obtained from any of the constituent learning algorithms alone. Using
the ‘caretEnsemble’ package, a ensemble model will be created that uses
the three models above.

------------------------------------------------------------------------

#### Results

------------------------------------------------------------------------

### 7. Considering Betting on Draws

Soccer is different from every other popular sport in that it allows for
draws. For the casual sports fan who is drawn to watching sports to see
two teams compete and a winner declared, this can seem incredibly
boring.

This mentality is reflected in the odds offered by odds makers who
consistently offer slightly better odds on draws, because casual punters
are more likely to bet on one of the two teams winning. This is
reflected in the historical odds data for Serie A. Draws have a minimum
odds of 2.4, over twice the minimum of either home or away outcomes.
Average returns are highest for an away win (4.86), then a draw (4.06),
and last a home win (2.86). This is unsurprisngly if we consider how
home advantage affects matches.

![](SerieA_Blog_Post_files/figure-markdown_github/Betting%20on%20Draws-1.png)

A similarly striking pattern on returns is observed when filtering for
odds that actually paid out. The mean payout among draws is now higher
than away wins and the minimum payout is greater than twice that of away
or home wins.

![](SerieA_Blog_Post_files/figure-markdown_github/Betting%20on%20Draws%202-1.png)

Given the higher payout of draws on average, it may make sense to
re-cast the multi-nomial classification problem (Win, Loss, Draw) to a
binomial classification problem with a focus on identifying draws
(identifying non-draws wouldn’t be helpful for betting purposes).

------------------------------------------------------------------------

### 7. Overall Results

------------------------------------------------------------------------

### 8. Conclusion and Next Steps

-   Gather more data: xG, player-level data, etc.
    -   Find a way to account for class imbalance through over or under
        sampling
    -   Investigate other modeling approaches. Caret has over [238
        models](https://topepo.github.io/caret/train-models-by-tag.html)
        that can be incorporated along with all their tuning parameters.

_Code for this project can be found on my [GitHub](https://github.com/rsolter/Serie-A-Predictions)_
