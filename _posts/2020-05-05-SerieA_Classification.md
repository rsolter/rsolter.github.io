---
title: "Predicting Soccer Matches Outcomes using caret"
categories: [R, caret]
date: 2020-05-05
excerpt: "Building models to predict the outcome of Serie A matches"
toc: true
toc_label: "Content"
toc_sticky: true
toc_icon: 'futbol'
---

### Predicting Soccer Matches Outcomes using caret
![Stadio Olimpico](/assets/images/remi-jacquaint.jpg)

_Code for this project can be found on my [GitHub](https://github.com/rsolter/Serie-A-Predictions)_

****
------------------------------------------------------------------------

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

|                  |                                  |
|------------------|----------------------------------|
| Goals            | Total Shots                      |
| Saves            | Shots on Target                  |
| Penalties        | Shots on Target from Free Kicks  |
| Fouls            | Shots off Target                 |
| Scoring Chances  | Shots off Target from Free Kicks |
| Offsides         | Shots from within the Box        |
| Corners          | Shots on Target from Set Pieces  |
| Red Cards        | Shots off Target from Set Pieces |
| Yellow Cards     | Attacks (Middle)                 |
| Possession       | Attacks (Left)                   |
| Completed Passes | Attacks (Right)                  |
| Passing Accuracy | Fast Breaks                      |
| Key Passes       | Crosses                          |
|                  | Long Balls                       |

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
quadrant.

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

``` r
# Variable Importance Plot
raw_to_filter <- df_raw %>%
  select(-season,-round,-goals_h,-goals_a,-Team_h,-Team_a,-match_id,-match_date)

Filter_Forest <- randomForest(outcome ~ ., data=raw_to_filter)
# importance(Filter_Forest)
varImpPlot(Filter_Forest,
           main = "Feature Importance in Predicting Match Outcome",n.var = ncol(raw_to_filter))
```

![](/rblogging/2020/05/05/Feature%20Selection%20using%20Random%20Forest-1.png)

``` r
Variables_To_Drop <- c("pen_h","pen_a","shot_off_fk_a","shot_off_fk_h",
                       "shot_on_fk_h","shot_on_fk_a","shots_sp_on_h","shots_sp_on_a",
                       "shots_sp_off_h","shots_sp_off_a")

# removing 'unimportant' variables, drops from 14 features
df_raw <- df_raw %>% select(-Variables_To_Drop)
```

#### Feature Extraction with PCA

Even after removing 10 features from the dataset, there are still a
large number of predictors for each match’s outcome. To reduce the
number of features while maximizing the amount of variation still
explained, principal components analysis was applied as a
[pre-processing
technique](https://topepo.github.io/caret/pre-processing.html#transforming-predictors)
in caret.

``` r
train(
  outcome ~ .,
  data = romaTrain,
  method = "multinom",
  preProc = c("pca"),
  trControl = trainControl(method = "cv", number = 5),
  trace = FALSE
)
```

------------------------------------------------------------------------

### 3. Distribution of Outcomes by Team

It’s worthwhile to point out that the distribution of outcomes is
naturally different by team. Dominant teams like Juventus, Napoli, Roma,
and Inter Milan all have win percentages over 50%. This class imbalance
has consequences for the models built. However, for the example below,
we’ll focus on Sampdoria which has a relatively balanced distribution of
outcomes for seasons 2015-16 - 2018-19: 34.8% Win, 23.6%, Loss 41.4%.

``` r
load(file="00 Data/Team_split_Data.rdata")

outcomes <- data.frame(team=NA,D=NA,L=NA,W=NA)

for(i in 1:length(final_data)){
    tmp <- final_data[[i]] %>% filter(!season=="2019-20")

    if (nrow(tmp)==0) {
      next
      }

    tmp_name <- tmp$Team %>% unique() %>% as.character()

    outcomes[i,1] <- tmp_name

    out<-as.vector(table(tmp$outcome)/nrow(tmp))

    #outcomes[i,2] <- out[1]
    #outcomes[i,3] <- out[2]
    #outcomes[i,4] <- out[3]
}

outcome_viz <- outcomes %>% gather("Outcome","Prop",2:4)


# Order of teams
ordered_by_W <- outcome_viz %>% filter(Outcome=="W") %>% arrange(-Prop) %>% select(team) %>% as.vector()

outcome_viz$team <- factor(outcome_viz$team,levels = ordered_by_W$team)
outcome_viz$Outcome <- factor(outcome_viz$Outcome,levels = c("W","D","L"))

p <- ggplot(outcome_viz, aes(x=Outcome, y=Prop, fill=Outcome)) +
  geom_bar(stat="identity", width=1,colour="grey") +
  facet_wrap(facets = "team",nrow = 8) +
  theme_minimal() + # remove background, grid, numeric labels
  scale_fill_manual(values=c("#008c45","#f4f5f0", "#cd212a"),labels=c("Win","Draw","Loss")) +
  xlab("") + ylab("") + ggtitle("Match Outcome Distribution by Team") +
  theme(legend.position="bottom",plot.title = element_text(hjust = 0.5)) +
  labs(caption = "Outcomes for seasons 2015-16 - 2018-19")

p
```

![](/rblogging/2020/05/05/outcome_viz-1.png)

### 4. Illustrative Example with U.C Sampdoria

``` r
Samp <- final_data[[17]]
```

#### Multinomial Regression

#### SVM

#### Random Forest

#### Naive-Bayes

#### Ensemble

#### Results

------------------------------------------------------------------------

### 5. Overall Results

### 6. Conclusion and Next Steps

-   Include xG data

    -   Find a way to account for class imbalance

    -   Add player-level data

    -   Try a generalizable model
