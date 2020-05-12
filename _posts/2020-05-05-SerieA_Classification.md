---
title: "Prediction of Soccer Matches using caretEnsemble"
categories: [R, caret]
date: 2020-05-05
excerpt: "Serie A Classification"
---


![Stadio Olimpico](/assets/images/stadio_olimpico.jpg)
_Liam McKay on Unsplash_



### Contents

1. [Gathering Data](https://rsolter.github.io/r/caret/SerieA_Classification/#1-gathering-data)
2. [Feature Engineering](https://rsolter.github.io/r/caret/SerieA_Classification/#2-feature-engineering)
3. [Illustrative Example with AS Roma](https://rsolter.github.io/r/caret/SerieA_Classification/#3-illustrative-example-with-as-roma)
4. [Overall Results](https://rsolter.github.io/r/caret/SerieA_Classification/#2-overall-results)


_Code for this project can be found on my [GitHub](https://github.com/rsolter/Serie-A-Predictions)_

****

### 1. Gathering Data

The data for this project was gathered from the official [Serie A website](http://www.legaseriea.it/en) and its match reports from the 2015-16 season through the current 2019-20 season. Note that due to the effects of the corona virus, all matches were postponed in Italy after the first week of March, 2020. The scrapers used the `rvest` package and can be found [here](https://github.com/rsolter/Serie-A-Predictions/tree/master/01%20Scrapers). Note that there three separate scrapers:

  - Scraper_SerieA_Archive_1.R for seasons 2015-16 through 2017-18
  - Scraper_SerieA_Archive_2.R for the 2018-19 season
  - Scraper_SerieA_Current.R for the current seasons - 2019-20

Weekly elo scores for each team were also downloaded from the website Clubelo.com using their [API](http://clubelo.com/API)

**Initial variable list:**

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

****

### 2. Feature Engineering

Before starting any modeling, there are some data process and feature engineering steps to take on:

1. Creating target variable `outcome` based off of the goals scored by home and away teams: {W,D,L}
2. Combined left, right, and middle attack variables into `total attacks`
3. Feature selection with Random Forest
4. Trailing Variables
5. Dimensionality Reduction with PCA

****

### 3. Illustrative Example with AS Roma


****

### 2. Overall Results
