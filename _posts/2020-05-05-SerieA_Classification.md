---
title: "Predicting of Soccer Matches Outcomes using caret"
categories: [R, caret]
date: 2020-05-05
excerpt: "Serie A Classification"
toc: true
toc_label: "Content"
toc_sticky: true
toc_icon: 'futbol'
---

### Predicting of Soccer Matches Outcomes using caret
![Stadio Olimpico](/assets/images/remi-jacquaint.jpg)

_Code for this project can be found on my [GitHub](https://github.com/rsolter/Serie-A-Predictions)_

****

### 1. Gathering Data

The data for this project was gathered from the official [Serie A website](http://www.legaseriea.it/en) and its match reports from the 2015-16 season through the current 2019-20 season. Note that due to the effects of the Covid-19, all matches were postponed in Italy after the first week of March, 2020. The scrapers used the `rvest` package and can be found [here](https://github.com/rsolter/Serie-A-Predictions/tree/master/01%20Scrapers). Weekly [Elo](https://en.wikipedia.org/wiki/Elo_rating_system) scores for each team were also downloaded from the website Clubelo.com using their [API](http://clubelo.com/API).


**Initial feature list:**

  - `Goals`
  - `Saves`
  - `Penalties`
  - `Total Shots`
  - `Shots on Target`
  - `Shots on Target from Free Kicks`
  - `Shots off Target`
  - `Shots off Target from Free Kicks`
  - `Shots from within the Box`
  - `Shots on Target from Set Pieces`
  - `Shots off Target from Set Pieces`
  - `Fouls`
  - `Scoring Chances`
  - `Offsides`
  - `Corners`
  - `Red Cards`
  - `Yellow Cards`
  - `Fast Breaks`
  - `Crosses`
  - `Long Balls`
  - `Attacks (Middle)`
  - `Attacks (Left)`
  - `Attacks (Right)`
  - `Possession`
  - `Completed Passes`
  - `Passing Accuracy`
  - `Key Passes`
  - `Recoveries`


#### A Note on Expected goals

[Expected Goals](https://wikieducator.org/Sport_Informatics_and_Analytics/Performance_Monitoring/Expected_Goals) (_xG_) are not included in this analysis. xG measures the quality of goalscoring chances and the likelihood of them being scored. Factors influencing the probability of a goal being scored from a shot include distance from the goal; angle from the goal; and whether or not the player taking the shot was at least 1 m away from the nearest defender. Although very popular, I have not included these stats in the analysis but may do so later from a site like [understat](https://understat.com/)

****

### 2. Processing the Data

In its raw form the observations gathered are grouped by match, with stats for both the home and away teams:

From this raw form the data has been processed in the following ways:


- **Replacing data with lagged averages** - all the stats collected (with the exception of Elo), have been replaced with lagged averages. The rationale for this is that we need historical performance data to try and predict future match outcomes.

- **Data regrouped by team** - The full set of records is broken up by teams so that there exist +20 datasets. One for each individual team with lagged average stats on their and their opponents performance.

- **Data Validation** - For testing and validation purposes, models are built upon historical data and then tested on the next chronological match. This is accomplished using the _time_slice()_ function in the **caret** package. A visual representation of this partition can be seen below in the bottom left quadrant.

![](/assets/images/Split_time-1.svg)






****

### 3. Feature Engineering

Before starting any modeling, there are some data process and feature engineering steps to take on:

1. Creating target variable `outcome` based off of the goals scored by home and away teams: {W,D,L}
2. Combined left, right, and middle attack variables into `total attacks`
3. Feature selection with Random Forest
4. Dimensionality Reduction with PCA

****

### 4. Illustrative Example with AS Roma

#### Multinomial Regression

#### SVM

#### Random Forest

#### Naive-Bayes

#### Ensemble

#### Results
****

### 5. Overall Results


### 6. Conclusion and Next Steps
