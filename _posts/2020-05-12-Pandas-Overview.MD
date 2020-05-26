---
title: pandas Overview
categories: [python, pandas, reference]
date: 2020-05-12
excerpt: "An overview of pandas key functions"
toc: true
toc_label: "Content"
toc_sticky: true
---

### pandas Overview

![pandas](/assets/images/pandas.jpg)

Making the move from R to python, I feel out of place without my familiar tidyverse of packages for data maniuplation and visualization. As such, I've been spending a lot of time learning [pandas](https://pandas.pydata.org/), the most popular data analysis and manipulation tool in python.

This post is meant to serve as an overview of pandas functionality as well as serve as a personal reference. To demonstrate pandas, I've chosen to use a Kaggle [dataset](https://www.kaggle.com/nolanbconaway/pitchfork-data) that compiles over 18k music reviews from the Pitchfork website.

A copy of this .ipynb can be found on [here](https://github.com/rsolter/Udemy-Courses/blob/master/Udemy%20-%20Data%20Analysis%20with%20Pandas%20and%20Python/Summary.ipynb) my git repository for the Udemy pandas course


**Some useful links:**

- [Official Pandas Documentation](https://pandas.pydata.org/)

- [Comparison with R/R libraries](https://pandas.pydata.org/pandas-docs/stable/getting_started/comparison/comparison_with_r.html?highlight=arrange)

- [On Method Chaining in pandas](https://towardsdatascience.com/the-unreasonable-effectiveness-of-method-chaining-in-pandas-15c2109e3c69)


**Importing data**


```python
import pandas as pd
import numpy as np
import datetime as dt

## Importing csv for summary of functions
import pandas as pd
p4k = pd.read_csv("p4kreviews.csv",
                  encoding='latin1',
                  index_col=0, # no index set, can specify id column if it exists
                  parse_dates=["date"]) # pandas is very good at recognizing dates upon import
```

### 1. Inspecting a dataframe

_key methods: .head(), .describe(), .info(), .shape, .dtypes, .columns(), .value_counts()_

Below is a preview of the dataset which includes each album's score on a 10 point scale, artist name, album name, genre, review date, and text of the review. The best column refers to whether or not the album was designated a 'best new music' label.




```python
p4k.head() ## shows first five rows
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
  </tbody>
</table>
</div>




```python
p4k.describe().transpose() ## Provides a summary of quantitative columns, extra transpose() method chained along
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>best</th>
      <td>19555.0</td>
      <td>0.053183</td>
      <td>0.224405</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>score</th>
      <td>19555.0</td>
      <td>7.027446</td>
      <td>1.277544</td>
      <td>0.0</td>
      <td>6.5</td>
      <td>7.3</td>
      <td>7.8</td>
      <td>10.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
p4k.info() ## Provides data type and non-null counts for each column
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 19555 entries, 1 to 19555
    Data columns (total 7 columns):
     #   Column  Non-Null Count  Dtype         
    ---  ------  --------------  -----         
     0   album   19550 non-null  object        
     1   artist  19555 non-null  object        
     2   best    19555 non-null  int64         
     3   date    19555 non-null  datetime64[ns]
     4   genre   19555 non-null  object        
     5   review  19554 non-null  object        
     6   score   19555 non-null  float64       
    dtypes: datetime64[ns](1), float64(1), int64(1), object(4)
    memory usage: 1.2+ MB



```python
p4k.columns ## Returns a list with the column names
p4k.dtypes ## Returns the data types for each column
p4k.shape ## Returns a tuple with dimensions of a dataset
```




    (19555, 7)



### 2. Selecting columns or rows from a dataframe

_key methods: .loc(), .iloc(), .value_counts()_

**Selecting columns** by name is done by passing the column(s) quoted name into brackets.


```python
p4k['album'] # selecting a single column
p4k[['album','artist']] # selecting multiple columns
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>1999</td>
      <td>Cassius</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 2 columns</p>
</div>




```python
p4k.loc[:,'artist':] # A range of columns can also be selected using the colon (:)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 6 columns</p>
</div>



**Selecting rows** can be done with the .iloc() method which can be sliced with a colon (:)



```python
p4k.iloc[0] # Returning the first row (as a pandas series)
p4k.iloc[0:9] # Returning the first 10 rows (as a pandas dataframe)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1</td>
      <td>2017-12-05</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Everyday Is Christmas</td>
      <td>Sia</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Pop/R&amp;B</td>
      <td>Sias shiny Christmas album feels inconsistent...</td>
      <td>5.8</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Zaytown Sorority Class of 2017</td>
      <td>Zaytoven</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rap</td>
      <td>The prolific Atlanta producer enlists 17 women...</td>
      <td>6.2</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Songs of Experience</td>
      <td>U2</td>
      <td>0</td>
      <td>2017-12-04</td>
      <td>Rock</td>
      <td>Years in the making, U2s 14th studio album fi...</td>
      <td>5.3</td>
    </tr>
  </tbody>
</table>
</div>



Counting non-numeric data with **.value_counts()**


```python
p4k['genre'].value_counts()
# p4k['genre'].value_counts()/p4k.shape[0] -- getting proportions instead of counts
```




    Rock            6958
    Electronic      4020
    None            2324
    Experimental    1699
    Rap             1481
    Pop/R&B         1157
    Metal            781
    Folk/Country     700
    Jazz             257
    Global           178
    Name: genre, dtype: int64



### 3. Adding or Deleting Columns and Rows

_key methods: .insert(), .append(), .drop()_


```python
p4k["Review Language"] = 'ENG' # Adding a new column with a universal value:
p4k.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
</div>




```python
p4k.insert(loc=0,column="Parent Company", value="Vox") # Second method for adding column with loc
p4k.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
</div>



Adding a row with **append()**


```python
# creating a new, fake review
new_review = {'Parent Company':'Vox','album':'Yandhi','artist':'Kanye West',
              'best':'1','date':'December 31 2050','genre':'Rap','review':'BEST.ALBUM.EVER',
              'score':10.0,'Review Language':'ENG'}
```


```python
p4k.append(new_review,ignore_index=True) # ignore_index allows the new row(s) to be inserted seemlessly
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06 00:00:00</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06 00:00:00</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06 00:00:00</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06 00:00:00</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05 00:00:00</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26 00:00:00</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12 00:00:00</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12 00:00:00</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05 00:00:00</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Vox</td>
      <td>Yandhi</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>December 31 2050</td>
      <td>Rap</td>
      <td>BEST.ALBUM.EVER</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 9 columns</p>
</div>



Dropping rows and columns with **.drop()**


```python
p4k.drop(1) # returns a dataframe w/out the observation with index label '1'
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Vox</td>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1</td>
      <td>2017-12-05</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>19554 rows × 9 columns</p>
</div>




```python
p4k.drop("Parent Company", axis=1)
# or
p4k.drop("Parent Company", axis="columns")

# multiple columns
# p4k.drop(["Parent Company","date"], axis="columns")
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 8 columns</p>
</div>



### 4. Filtering

_key methods: .isn(), .between(), .where(), .query()_



```python
# Boolean filtering
p4k[p4k["artist"]=="Prince"]
p4k[p4k["genre"] == "Global"]
#p4k[p4k["genre"] != "Metal"] # Filter negated with '!='
p4k[p4k["score"]>9.5]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1599</th>
      <td>Vox</td>
      <td>One Nite Alone, The Aftershow: It Ain't Over!</td>
      <td>Prince</td>
      <td>1</td>
      <td>September 1 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue Originally released as part o...</td>
      <td>8.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2042</th>
      <td>Vox</td>
      <td>Sign "O" the Times</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 30 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Choosing a single high point from Prince's glo...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2043</th>
      <td>Vox</td>
      <td>1999</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 30 2016</td>
      <td>Pop/R&amp;B</td>
      <td>1999 is the greatest album ever made about par...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2047</th>
      <td>Vox</td>
      <td>Dirty Mind</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 29 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Princes first fully actualized album is an un...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2048</th>
      <td>Vox</td>
      <td>Controversy</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 29 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Controversy emerged in 1981 at a pivotal time ...</td>
      <td>9.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2444</th>
      <td>Vox</td>
      <td>HITNRUN Phase Two</td>
      <td>Prince</td>
      <td>0</td>
      <td>January 8 2016</td>
      <td>Pop/R&amp;B</td>
      <td>The second of Prince's HITNRUN series is anoth...</td>
      <td>4.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2779</th>
      <td>Vox</td>
      <td>HITNRUN Phase One</td>
      <td>Prince</td>
      <td>0</td>
      <td>September 10 2015</td>
      <td>Pop/R&amp;B</td>
      <td>Prince's new effort, exclusive to Jay Z's Tida...</td>
      <td>4.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>12334</th>
      <td>Vox</td>
      <td>Planet Earth</td>
      <td>Prince</td>
      <td>0</td>
      <td>July 23 2007</td>
      <td>Pop/R&amp;B</td>
      <td>So far this year Prince has wowed at the Super...</td>
      <td>4.8</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>13400</th>
      <td>Vox</td>
      <td>Ultimate Prince</td>
      <td>Prince</td>
      <td>0</td>
      <td>September 5 2006</td>
      <td>Pop/R&amp;B</td>
      <td>This Prince best of covers the Warner Brothers...</td>
      <td>8.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>13946</th>
      <td>Vox</td>
      <td>3121</td>
      <td>Prince</td>
      <td>0</td>
      <td>March 20 2006</td>
      <td>Pop/R&amp;B</td>
      <td>On his latest release, the rock legend betters...</td>
      <td>6.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>16164</th>
      <td>Vox</td>
      <td>Musicology</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 28 2004</td>
      <td>Pop/R&amp;B</td>
      <td>One of the first things a rock critic learns i...</td>
      <td>5.8</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Multiple conditions
condition1 = p4k["score"]>9.3
condition2 = p4k["genre"] == "Global"
p4k[condition1 & condition2] # filtering on both
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1568</th>
      <td>Vox</td>
      <td>Caetano Veloso</td>
      <td>Caetano Veloso</td>
      <td>0</td>
      <td>September 11 2016</td>
      <td>Global</td>
      <td>In 1968, the Brazilian pop singer began a Trop...</td>
      <td>9.4</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
</div>




```python
p4k[condition1 | condition2] # filtering on either conditions
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>14</th>
      <td>Vox</td>
      <td>Master of Puppets</td>
      <td>Metallica</td>
      <td>1</td>
      <td>December 2 2017</td>
      <td>Metal</td>
      <td>Best new reissue In 1986, Metallica released i...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>119</th>
      <td>Vox</td>
      <td>Walk Among Us</td>
      <td>Misfits</td>
      <td>0</td>
      <td>October 31 2017</td>
      <td>Metal</td>
      <td>They were outliers when they started, but by t...</td>
      <td>9.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>128</th>
      <td>Vox</td>
      <td>I Can Hear the Heart Beating as One</td>
      <td>Yo La Tengo</td>
      <td>0</td>
      <td>October 29 2017</td>
      <td>Rock</td>
      <td>Twenty years on from its original release, Yo ...</td>
      <td>9.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>154</th>
      <td>Vox</td>
      <td>The Queen Is Dead</td>
      <td>The Smiths</td>
      <td>0</td>
      <td>October 22 2017</td>
      <td>Rock</td>
      <td>Newly reissued as a boxed set, the Smiths 198...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>218</th>
      <td>Vox</td>
      <td>Ash</td>
      <td>Ibeyi</td>
      <td>1</td>
      <td>October 4 2017</td>
      <td>Global</td>
      <td>Best new music On their second album, the Fren...</td>
      <td>8.3</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19473</th>
      <td>Vox</td>
      <td>Agaetis Byrjun</td>
      <td>Sigur Rós</td>
      <td>0</td>
      <td>June 1 1999</td>
      <td>Rock</td>
      <td>Icelandic lore tells of the Hidden People who ...</td>
      <td>9.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19475</th>
      <td>Vox</td>
      <td>Livro</td>
      <td>Caetano Veloso</td>
      <td>0</td>
      <td>June 1 1999</td>
      <td>Global</td>
      <td>I heard somewhere that a person's tastes chang...</td>
      <td>9.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19490</th>
      <td>Vox</td>
      <td>Mule Variations</td>
      <td>Tom Waits</td>
      <td>0</td>
      <td>April 27 1999</td>
      <td>Rock</td>
      <td>I once took a poetry workshop taught by a guy ...</td>
      <td>9.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19520</th>
      <td>Vox</td>
      <td>Brand New Secondhand</td>
      <td>Roots Manuva</td>
      <td>0</td>
      <td>March 23 1999</td>
      <td>Electronic</td>
      <td>For politcially unaware, socially unconscious,...</td>
      <td>9.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19535</th>
      <td>Vox</td>
      <td>Summerteeth</td>
      <td>Wilco</td>
      <td>0</td>
      <td>February 28 1999</td>
      <td>Rock</td>
      <td>After parting ways with Uncle Tupelo partner J...</td>
      <td>9.4</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>403 rows × 9 columns</p>
</div>



The **.isin()** method


```python
p4k[p4k["genre"].isin(["Global","Jazz"])]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>28</th>
      <td>Vox</td>
      <td>4444</td>
      <td>Sam Gendel</td>
      <td>0</td>
      <td>November 29 2017</td>
      <td>Jazz</td>
      <td>Sam Gendels smoothly psychedelic debut is dom...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Vox</td>
      <td>Tauhid/Jewels of Thought/Deaf Dumb Blind (Summ...</td>
      <td>Pharoah Sanders</td>
      <td>1</td>
      <td>November 10 2017</td>
      <td>Jazz</td>
      <td>Best new reissue Best new reissue 1 / 3 Albums...</td>
      <td>8.2</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>157</th>
      <td>Vox</td>
      <td>The Centennial Trilogy</td>
      <td>Christian Scott aTunde Adjuah</td>
      <td>0</td>
      <td>October 21 2017</td>
      <td>Jazz</td>
      <td>1 / 3 Albums On the three albums that compose ...</td>
      <td>7.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>178</th>
      <td>Vox</td>
      <td>The Magic City / My Brother the Wind Vol. 1</td>
      <td>Sun Ra and His Arkestra</td>
      <td>0</td>
      <td>October 16 2017</td>
      <td>Jazz</td>
      <td>1 / 2 Albums Sun Ra manifested an ecstatic vis...</td>
      <td>8.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>208</th>
      <td>Vox</td>
      <td>Dreams and Daggers</td>
      <td>Cécile McLorin Salvant</td>
      <td>0</td>
      <td>October 7 2017</td>
      <td>Jazz</td>
      <td>The young jazz singers live double album show...</td>
      <td>7.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19261</th>
      <td>Vox</td>
      <td>Permutation</td>
      <td>Bill Laswell</td>
      <td>0</td>
      <td>March 31 2000</td>
      <td>Global</td>
      <td>If there's one thing Bill Laswell knows, it's ...</td>
      <td>6.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19276</th>
      <td>Vox</td>
      <td>Expensive Shit/He Miss Road</td>
      <td>Fela Kuti</td>
      <td>0</td>
      <td>March 21 2000</td>
      <td>Global</td>
      <td>Afro-beat pioneer Fela Kuti was never more pis...</td>
      <td>8.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19280</th>
      <td>Vox</td>
      <td>Treader</td>
      <td>Spring Heel Jack</td>
      <td>0</td>
      <td>March 21 2000</td>
      <td>Jazz</td>
      <td>Isn't it kind of alarming that the once cuttin...</td>
      <td>5.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19467</th>
      <td>Vox</td>
      <td>Interstellar Space Revisited: The Music of Joh...</td>
      <td>Gregg Bendian / Nels Cline</td>
      <td>0</td>
      <td>June 8 1999</td>
      <td>Jazz</td>
      <td>A friend of mine once remarked about the later...</td>
      <td>7.9</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19475</th>
      <td>Vox</td>
      <td>Livro</td>
      <td>Caetano Veloso</td>
      <td>0</td>
      <td>June 1 1999</td>
      <td>Global</td>
      <td>I heard somewhere that a person's tastes chang...</td>
      <td>9.0</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>435 rows × 9 columns</p>
</div>



The **.between()** method


```python
p4k[p4k["score"].between(3.4,3.6)]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>1200</th>
      <td>Vox</td>
      <td>December 99th</td>
      <td>Yasiin Bey</td>
      <td>0</td>
      <td>January 2 2017</td>
      <td>Rap</td>
      <td>On December 99th, Yasiin Bey (fka Mos Def) sou...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>1348</th>
      <td>Vox</td>
      <td>Collage</td>
      <td>The Chainsmokers</td>
      <td>0</td>
      <td>November 9 2016</td>
      <td>Electronic</td>
      <td>The massive lite-EDM duo the Chainsmokers port...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>1775</th>
      <td>Vox</td>
      <td>New Introductory Lectures on the System of Tra...</td>
      <td>Kel Valhaal</td>
      <td>0</td>
      <td>July 15 2016</td>
      <td>Experimental</td>
      <td>Liturgy frontman Hunter Hunt-Hendrix's latest ...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>1808</th>
      <td>Vox</td>
      <td>Primary Colours</td>
      <td>Magic!</td>
      <td>0</td>
      <td>July 6 2016</td>
      <td>Pop/R&amp;B</td>
      <td>After scoring a #1 hit with 2014's "Rude,"  th...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>18797</th>
      <td>Vox</td>
      <td>Bleed American</td>
      <td>Jimmy Eat World</td>
      <td>0</td>
      <td>August 21 2001</td>
      <td>Rock</td>
      <td>Are you a 15-year-old TRL addict looking for a...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19072</th>
      <td>Vox</td>
      <td>Dream Signals in Full Circles</td>
      <td>Tristeza</td>
      <td>0</td>
      <td>September 26 2000</td>
      <td>Rock</td>
      <td>In the beginning, there was Nothing. And it wa...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19336</th>
      <td>Vox</td>
      <td>The Past Was Faster</td>
      <td>Kelley Stoltz</td>
      <td>0</td>
      <td>December 14 1999</td>
      <td>Rock</td>
      <td>There's an upside and a downside to the perpet...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19394</th>
      <td>Vox</td>
      <td>Cobra and Phases Group Play Voltage in the Mil...</td>
      <td>Stereolab</td>
      <td>0</td>
      <td>September 21 1999</td>
      <td>Experimental</td>
      <td>"Okay, Brent, this is getting really old." "Wh...</td>
      <td>3.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19454</th>
      <td>Vox</td>
      <td>The Seduction of Claude Debussy</td>
      <td>Art of Noise</td>
      <td>0</td>
      <td>June 29 1999</td>
      <td>Electronic</td>
      <td>Tonite during Prime Time: Channel 02: "The Art...</td>
      <td>3.6</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>91 rows × 9 columns</p>
</div>



The **.where()** method which returns the original dataframe with NAs in rows that don't meet the criteria


```python
p4k.where(p4k["genre"]=="Rap")
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 9 columns</p>
</div>



The **.query()** method is very readable, but since the conditions are wrapped in single quotes, it will not work if there are spaces in the column names


```python
p4k.query('best==1.0')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Vox</td>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1</td>
      <td>December 5 2017</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Vox</td>
      <td>Master of Puppets</td>
      <td>Metallica</td>
      <td>1</td>
      <td>December 2 2017</td>
      <td>Metal</td>
      <td>Best new reissue In 1986, Metallica released i...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Vox</td>
      <td>Kick</td>
      <td>INXS</td>
      <td>1</td>
      <td>December 1 2017</td>
      <td>Rock</td>
      <td>Best new reissue The 30th-anniversary edition ...</td>
      <td>8.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Vox</td>
      <td>Utopia</td>
      <td>Björk</td>
      <td>1</td>
      <td>November 27 2017</td>
      <td>Electronic</td>
      <td>Best new music Filled with flute and birdsong,...</td>
      <td>8.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>17501</th>
      <td>Vox</td>
      <td>Do You Party?</td>
      <td>The Soft Pink Truth</td>
      <td>1</td>
      <td>February 4 2003</td>
      <td>Electronic</td>
      <td>Best new music There will be big fun in town t...</td>
      <td>8.4</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>17511</th>
      <td>Vox</td>
      <td>You Forgot It in People</td>
      <td>Broken Social Scene</td>
      <td>1</td>
      <td>February 2 2003</td>
      <td>Rock</td>
      <td>Best new music It's a bit late to be talking a...</td>
      <td>9.2</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>17551</th>
      <td>Vox</td>
      <td>Everything Is Good Here/Please Come Home</td>
      <td>The Angels of Light</td>
      <td>1</td>
      <td>January 20 2003</td>
      <td>None</td>
      <td>Best new music To a certain extent, most of us...</td>
      <td>8.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>17554</th>
      <td>Vox</td>
      <td>Mount Eerie</td>
      <td>The Microphones</td>
      <td>1</td>
      <td>January 20 2003</td>
      <td>Experimental</td>
      <td>Best new music Growing up in the shadow of Mt....</td>
      <td>8.9</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>17562</th>
      <td>Vox</td>
      <td>S.T.R.E.E.T. D.A.D.</td>
      <td>Out Hud</td>
      <td>1</td>
      <td>January 15 2003</td>
      <td>Electronic</td>
      <td>Best new music Language is for suckers, but du...</td>
      <td>9.0</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>1040 rows × 9 columns</p>
</div>



### 5. Ranking & Sorting

_key methods: .sort_values(), .sort_index(), .rank(), .nsmallest(), .nlargest()_

Sorting of values is done with the **.sort_values()** method or **.sort_index()**


```python
p4k.sort_values("score",
               ascending=False, # default is ascending=True
               na_position="first", # position of na values. options include "first","last"
               inplace=True) # argument to replace 'p4k' dataframe with sorted output
```


```python
p4k.sort_values(['score','artist'],ascending=[False,False]) # multiple parameters to sort by
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1162</th>
      <td>Vox</td>
      <td>Germfree Adolescents</td>
      <td>X-Ray Spex</td>
      <td>0</td>
      <td>January 15 2017</td>
      <td>Rock</td>
      <td>X-Ray Spexs debut album is a brash, vivid mas...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>13782</th>
      <td>Vox</td>
      <td>Pink Flag / Chairs Missing / 154</td>
      <td>Wire</td>
      <td>0</td>
      <td>May 5 2006</td>
      <td>Rock</td>
      <td>1 / 3 Albums Wire were born at the dawn of pun...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>6049</th>
      <td>Vox</td>
      <td>The Disintegration Loops</td>
      <td>William Basinski</td>
      <td>1</td>
      <td>November 19 2012</td>
      <td>Experimental</td>
      <td>Best new reissue The four volumes of William B...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>18222</th>
      <td>Vox</td>
      <td>Yankee Hotel Foxtrot</td>
      <td>Wilco</td>
      <td>0</td>
      <td>April 21 2002</td>
      <td>Rock</td>
      <td>Myth, it has been said, is the buried part of ...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>1004</th>
      <td>Vox</td>
      <td>Weezer (Blue Album)</td>
      <td>Weezer</td>
      <td>0</td>
      <td>February 26 2017</td>
      <td>Rock</td>
      <td>Weezers 1994 debut, filled with geeky humor, ...</td>
      <td>10.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>15690</th>
      <td>Vox</td>
      <td>Travistan</td>
      <td>Travis Morrison</td>
      <td>0</td>
      <td>September 27 2004</td>
      <td>Pop/R&amp;B</td>
      <td>After a prestigious and fruitful career fronti...</td>
      <td>0.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19228</th>
      <td>Vox</td>
      <td>NYC Ghosts &amp; Flowers</td>
      <td>Sonic Youth</td>
      <td>0</td>
      <td>April 30 2000</td>
      <td>Rock</td>
      <td>No, I have not forgotten to put the numbers in...</td>
      <td>0.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>15048</th>
      <td>Vox</td>
      <td>Relaxation of the Asshole</td>
      <td>Robert Pollard</td>
      <td>0</td>
      <td>April 20 2005</td>
      <td>Rock</td>
      <td>If more drunks would learn from Robert Pollard...</td>
      <td>0.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>17050</th>
      <td>Vox</td>
      <td>Liz Phair</td>
      <td>Liz Phair</td>
      <td>0</td>
      <td>June 24 2003</td>
      <td>Rock</td>
      <td>It could be said that Liz Phair's greatest ass...</td>
      <td>0.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>13301</th>
      <td>Vox</td>
      <td>Shine On</td>
      <td>Jet</td>
      <td>0</td>
      <td>October 2 2006</td>
      <td>Rock</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 9 columns</p>
</div>




```python
p4k.sort_index(inplace=True) # reverting to original dataframe by sorting by index
p4k
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 9 columns</p>
</div>



Ranking is done with **.rank()** method, while individual extreme records can be returned with **.nlargest()** and **.nsmallest()**


```python
p4k['score'].rank()
```




    1         7976.0
    2          402.0
    3         5494.0
    4        13486.5
    5         5932.0
              ...   
    19551     1133.0
    19552    18931.0
    19553     9370.0
    19554     4286.0
    19555     5069.5
    Name: score, Length: 19555, dtype: float64



By default, **.rank()** assign average rankings to values that are equivalent. Different ranking methods can be applied by using the method parameter:


```python
p4k['score'].rank(method='min',ascending=False) # Min assigns lowest ranking for observations with the same values
```




    1        11104.0
    2        19131.0
    3        13879.0
    4         5691.0
    5        13370.0
              ...   
    19551    18368.0
    19552      587.0
    19553     9807.0
    19554    15110.0
    19555    14246.0
    Name: score, Length: 19555, dtype: float64




```python
# Adding score_rank to the dataframe as a new column
p4k['score_rank']=p4k['score'].rank(method='min',ascending=False)
p4k.sort_values("score_rank", ascending=True, na_position="first")  
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9787</th>
      <td>Vox</td>
      <td>Abbey Road</td>
      <td>The Beatles</td>
      <td>0</td>
      <td>September 10 2009</td>
      <td>Rock</td>
      <td>The perfect ending to a recording career, this...</td>
      <td>10.0</td>
      <td>ENG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2280</th>
      <td>Vox</td>
      <td>Off the Wall</td>
      <td>Michael Jackson</td>
      <td>1</td>
      <td>February 24 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue Off the Wall is the sound of ...</td>
      <td>10.0</td>
      <td>ENG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>7272</th>
      <td>Vox</td>
      <td>The Smile Sessions</td>
      <td>The Beach Boys</td>
      <td>1</td>
      <td>November 2 2011</td>
      <td>Rock</td>
      <td>Best new reissue Conceived, recorded, and ulti...</td>
      <td>10.0</td>
      <td>ENG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>9784</th>
      <td>Vox</td>
      <td>The Stone Roses</td>
      <td>The Stone Roses</td>
      <td>1</td>
      <td>September 11 2009</td>
      <td>Rock</td>
      <td>Best new reissue A badly needed remaster of th...</td>
      <td>10.0</td>
      <td>ENG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>9261</th>
      <td>Vox</td>
      <td>Ladies and Gentlemen We are Floating in Space ...</td>
      <td>Spiritualized</td>
      <td>1</td>
      <td>March 2 2010</td>
      <td>Experimental</td>
      <td>Best new reissue This new deluxe, limited prod...</td>
      <td>10.0</td>
      <td>ENG</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>13301</th>
      <td>Vox</td>
      <td>Shine On</td>
      <td>Jet</td>
      <td>0</td>
      <td>October 2 2006</td>
      <td>Rock</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>19228</th>
      <td>Vox</td>
      <td>NYC Ghosts &amp; Flowers</td>
      <td>Sonic Youth</td>
      <td>0</td>
      <td>April 30 2000</td>
      <td>Rock</td>
      <td>No, I have not forgotten to put the numbers in...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>12223</th>
      <td>Vox</td>
      <td>This Is Next</td>
      <td>Various Artists</td>
      <td>0</td>
      <td>August 22 2007</td>
      <td>None</td>
      <td>Basically an ADA sampler with a promotional st...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>17050</th>
      <td>Vox</td>
      <td>Liz Phair</td>
      <td>Liz Phair</td>
      <td>0</td>
      <td>June 24 2003</td>
      <td>Rock</td>
      <td>It could be said that Liz Phair's greatest ass...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>15048</th>
      <td>Vox</td>
      <td>Relaxation of the Asshole</td>
      <td>Robert Pollard</td>
      <td>0</td>
      <td>April 20 2005</td>
      <td>Rock</td>
      <td>If more drunks would learn from Robert Pollard...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 10 columns</p>
</div>




```python
p4k.nsmallest(n=4, columns="score")
# Alternative method which returns the records' index labels
#p4k["score"].nsmallest(n=4)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12223</th>
      <td>Vox</td>
      <td>This Is Next</td>
      <td>Various Artists</td>
      <td>0</td>
      <td>August 22 2007</td>
      <td>None</td>
      <td>Basically an ADA sampler with a promotional st...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>13301</th>
      <td>Vox</td>
      <td>Shine On</td>
      <td>Jet</td>
      <td>0</td>
      <td>October 2 2006</td>
      <td>Rock</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>15048</th>
      <td>Vox</td>
      <td>Relaxation of the Asshole</td>
      <td>Robert Pollard</td>
      <td>0</td>
      <td>April 20 2005</td>
      <td>Rock</td>
      <td>If more drunks would learn from Robert Pollard...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
    <tr>
      <th>15690</th>
      <td>Vox</td>
      <td>Travistan</td>
      <td>Travis Morrison</td>
      <td>0</td>
      <td>September 27 2004</td>
      <td>Pop/R&amp;B</td>
      <td>After a prestigious and fruitful career fronti...</td>
      <td>0.0</td>
      <td>ENG</td>
      <td>19550.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#p4k.nlargest(n=4, columns="score")
# Alternative method which returns the records' index labels
p4k["score"].nlargest(n=4)
```




    14     10.0
    154    10.0
    506    10.0
    566    10.0
    Name: score, dtype: float64



### 6. Working with NAs

_key methods: .isnull(), .notnull(), .dropna(), .fillna()_

There are a variety of methods to identify, remove, and replace NA values


```python
# adding a NA row to the data.frame
import numpy as np
NA_Row = {'album':np.nan,'artist':np.nan,'best':np.nan,'genre':np.nan,'review':np.nan,'score':np.nan}
NA_Row
```




    {'album': nan,
     'artist': nan,
     'best': nan,
     'genre': nan,
     'review': nan,
     'score': nan}




```python
p4k = p4k.append(NA_Row,ignore_index=True)
p4k
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
      <td>13879.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0.0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
      <td>587.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0.0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
      <td>14246.0</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 10 columns</p>
</div>



Null values can be identifed using **.isnull()**, while **.notnull()** can be used for the opposite. Both return boolean values.


```python
p4k.isnull()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 10 columns</p>
</div>




```python
p4k.notnull()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 10 columns</p>
</div>



The **.dropna()** method allows for multiple ways to drop NaN values:


```python
# drops any value with a NA
p4k.dropna()

#only removes rows with NAs in all values
p4k.dropna(how="all", inplace=True)

# Remove columns with any na values -- in this case, all columns
p4k.dropna(axis=1)

# Only dropping observations where there are nulls in "Salary" column
p4k.dropna(subset=["album"])

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
      <td>13879.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0.0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19550</th>
      <td>Vox</td>
      <td>1999</td>
      <td>Cassius</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>ENG</td>
      <td>18368.0</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
      <td>587.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0.0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
      <td>14246.0</td>
    </tr>
  </tbody>
</table>
<p>19549 rows × 10 columns</p>
</div>



The **.fillna()** method allows for automaic filling of NA values


```python
 # will replace all NAs within the datafram with 0 (not ideal)
p4k.fillna(value=0)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
      <td>13879.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0.0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
      <td>587.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0.0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
      <td>14246.0</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 10 columns</p>
</div>



### 7. Unique and Duplicate values

_key methods: .unique(), .nunique(), .duplicated(), .drop_duplicates()_

Two methods for identifying unique values are **.unique()** which returns an array of unique values, while **.nunique()** counts those unique values


```python
p4k['genre'].unique()
```




    array(['Rock', 'Rap', 'Pop/R&B', 'Metal', 'None', 'Electronic',
           'Experimental', 'Folk/Country', 'Jazz', 'Global', nan],
          dtype=object)




```python
# dropna parameter will not include nan's in the count
p4k['genre'].nunique(dropna=True)
```




    10



**.duplicated()** and **.drop_duplicates** are methods for identifying and removing duplicate rows in a dataframe.


```python
.duplicated()
df["First Name"].duplicated(keep="first") # by default, only the first observation of a value is not seen as a duplicate
df["First Name"].duplicated(keep="last") # running from the bottom up
df["First Name"].duplicated(keep= False) # identifys all values that are duplciated
df[df["First Name"].duplicated(keep= False)] # keeping any values that are duplicated
df[~df["First Name"].duplicated(keep=False)] # '~' negates to get all values which have no duplicates


```


```python
# returns boolean
p4k['genre'].duplicated()

# by default, only the first observation of a value is not seen as a duplicate
p4k['genre'].duplicated(keep="first")

# running from the bottom up
p4k['genre'].duplicated(keep="last")

```




    0         True
    1         True
    2         True
    3         True
    4         True
             ...  
    19551    False
    19552    False
    19553    False
    19554    False
    19555    False
    Name: genre, Length: 19556, dtype: bool




```python
# identifys all values that are duplciated
p4k['genre'].duplicated(keep= False)

# keeping any values that are duplicated (all rows except NA row)
p4k[p4k['genre'].duplicated(keep= False)]

# '~' negates to get all values which have no duplicates (only NA row)
p4k[~p4k['genre'].duplicated(keep=False)]

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python

# only dropping duplicates in the score column, keeping first obs
p4k.drop_duplicates(subset=["score"], keep = "first")

# dropping all duplicated artists, only returning artists w/one review
p4k.drop_duplicates(subset=["artist"], keep = False)

# Can drop duplicates across multiple columns
p4k.drop_duplicates(subset = ["genre", "date"])

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0.0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Vox</td>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1.0</td>
      <td>December 5 2017</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
      <td>ENG</td>
      <td>425.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19550</th>
      <td>Vox</td>
      <td>1999</td>
      <td>Cassius</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>ENG</td>
      <td>18368.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0.0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
      <td>14246.0</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>13777 rows × 10 columns</p>
</div>



### 8. Working with Indexes

_key methods: .sort_index(), .set_index(), .reset_index(), .get_level_values(), .swap_levels(), .stack(), .unstack()_

Indexes in pandas store axis labels for all pandas objects. Indexes can take the form of id values, categorical indexes, multiple indexes, datetime indexes among others.


```python
p4k.index
```




    Int64Index([    1,     2,     3,     4,     5,     6,     7,     8,     9,
                   10,
                ...
                19546, 19547, 19548, 19549, 19550, 19551, 19552, 19553, 19554,
                19555],
               dtype='int64', length=19555)




```python
# returning an array of representing the data in the index
p4k.values
```




    array([['A.M./Being There', 'Wilco', 1, ..., 'Rock',
            'Best new reissue 1 / 2 Albums Newly reissued and remastered, the group\x92s first two albums find Jeff Tweedy and his Chicago band transforming themselves from alt-country also-rans into a formidable rock\x91n\x92roll outfit. The nuclear detonation of Uncle Tupelo launched an alt-country arms race, with the band\x92s two chief singer-songwriters mutating from old friends into bitter enemies trying to outdo each other with their follow-up records. Jay Farrar started Son Volt with Tupelo\x92s drummer, Mike Heidorn, and released Trace, which yielded the radio hit \x93Drown\x94 and found him greeted as a visionary. Jeff Tweedy, on the other hand, rushed into the studio to record a set of demos with his new band, Wilco, barely a couple months after his old band had played its final show. Nearly a year later they released their first album, A.M., which was greeted with a big shrug from critics and fans alike. Tweedy had managed to retain almost every member of Uncle Tupelo\x92s expanded line-up: multi-instrumentalist Max Johnston, guitarist John Stirratt, drummer Ken Coomer, even guitarist Brian Henneman, of the Bottle Rockets. In fact, the group briefly flirted with the idea of keeping the Uncle Tupelo name, although wiser heads prevailed and they rechristened themselves after trucker lingo for \x93will comply.\x94 Still, despite their familiarity and camaraderie, Wilco were still trying to figure out who they were when they wrote and recorded A.M.\x97a necessary step for any band, but especially for one whose de facto frontman had spent the last decade-plus in the shadow of the more forceful Farrar. The original mix of A.M. was, reportedly, much grittier and more akin to Uncle Tupelo\x92s early material than to the cleaner, more polished version that finally made its way into CD stores in March 1995. The final version is fine, although A.M. is the very definition of 1990s alt-country: as conservative as punk rock, with flourishes of pedal steel and a gentle twang to frame Tweedy\x92s hard-luck tales of casino losing streaks, suspended licenses, and custody battles over record collections. \x93I Must Be High\x94 nods toward the sunny pop of Summerteeth and beyond, and \x93Box Full of Letters\x94 and \x93Passenger Side\x94 hint at the melancholy melodicism that would become Tweedy\x92s stock in trade. He wanted Wilco to be a collaborative and democratic unit rather than a glorified solo project, though only one of his bandmates\x92 songs made the final cut: the gently loping \x93It\x92s Just That Simple,\x94 courtesy of Stirratt (who pens the new liner notes for this expanded edition). Otherwise, the MVP of the album is Henneman, who was a fine foil for Tweedy. There\x92s real wit and warmth in his guitar riffs, even if he was merely a rental rather than a full band member. According to Learning How to Die, Greg Kot\x92s 2004 biography of the band, Henneman recorded his part for the raucous throwaway \x93Casino Queen\x94 while nursing a broken heart; he downed two bottles of gin before the tape started rolling, and he plays like he\x92s holding on for dear life. A.M. is an album persistently defined by unfavorable comparisons, first to Son Volt\x92s Trace and later to every other Wilco album. And certainly there\x92s a timidity here that must have been discouraging after Tweedy\x92s strong contributions to Uncle Tupelo\x92s final pair of albums. But there\x92s also a lot of humor and sadness to these songs, which are circumscribed and local in their scope. That\x92s the charm of this modest reissue, which includes only a handful of bonus tracks (including the final Uncle Tupelo recording and a Blue Mountain cover) but treats A.M. as something more than a footnote to Wilco\x92s long career. In 2017, the album plays like a band setting its terms, striving to keep everything perfectly human-size. As much as the band was compared to that other Uncle Tupelo offshoot, Wilco\x92s biggest rivals were themselves. Tweedy, meanwhile, was struggling to grow up. Then in his late 20s, he quit smoking pot, a vice he\x92d adopted after quitting drinking a few years before. He married his girlfriend and prepared to become a father. And he made some painful adjustments to his expectations once A.M. floundered and ticket sales cratered. If he hadn\x92t turned those setbacks into songs, you probably wouldn\x92t be reading a review of any Wilco reissues. Being There is every bit as confident as their debut was shaky: a double album tied together by a loose theme of what it means to be in a rock\x91n\x92roll band. It opens with a big blast of lurching, serrating guitar distortion, sinewy and visceral and violent, which sounds like they\x92re tearing up A.M. so they can start over again. What\x92s amazing is how well it works. After the grinding din that begins and ends opener \x93Misunderstood,\x94 Tweedy\x92s songs have a brand new context. \x93Far, Far Away\x94 and \x93I Got You (At the End of the Century)\x94 aren\x92t actually that different from A.M., but they sound heavier, riskier, the stakes so much greater that it could be a different band altogether. There are some country flourishes, specifically the pedal steel courtesy of Freakwater\x92s Bob Egan, but Wilco had managed to transform itself from an alt-country also-ran into a real rock band: noise rock, classic rock, folk rock, heartland rock, every kind of rock. A conceptually knotty record, Being There contains barely veiled references to a breakup, but most listeners at the time would have understood them as references to Uncle Tupelo rather than a romantic partner. \x93Monday,\x94 with its Stonesy brass stomp, is about a guy in \x93a plenty good band\x94 who can only watch as others get all the glory, and you can guess what might have inspired Tweedy\x92s wry observations about fame and futility.  There are a few pulled punches (\x93I know you\x92re just a mama\x92s boy\x94 doesn\x92t have much sting), but thankfully, these aren\x92t dis tracks or bitter recriminations. Jay Farrar\x92s only a minor concern; these songs are first and foremost about Jeff Tweedy and his misgivings about his own talent. That had been a detriment on A.M.; on Being There it\x92s the band\x92s greatest strength and truest subject. Rock\x91n\x92roll is a sucker\x92s bet on these songs, something you pour your heart and soul and money and labor and dreams into and for what? Creative and financial frustration? \x93We\x92ve got solid-state technology,\x94 Tweedy sings on \x93Red-Eyed and Blue\x94; \x93Tapes on the floor/Some songs we can\x92t afford to play.\x94 So, why were they even doing it? That\x92s what Wilco are trying to figure out on Being There, and they need your help. It\x92s useful to imagine each of these songs being sung from the stage directly and explicitly to the audience who has paid to see Wilco. As Tweedy sings on \x93I Got You (At the End of the Century),\x94 \x93I got you and I still believe that you\x92re all that I will ever need/It\x92s you.\x94 That \x93you\x94 means us. Are we enough for them? Is our applause really that valuable? Being There flirts with cynicism, but really the mood is sober, world-weary, adult. \x93There is no sunken treasure/Rumored to be wrapped inside my ribs,\x94 Tweedy sings on \x93Sunken Treasure,\x94 the slow-burn epic that anchors the second disc of this double album. \x93Music is my savior/I was maimed by rock and roll.\x94 If rock had once been a liberating medium, after Nirvana it had become an albatross around the neck of every dreamer with a guitar. But Tweedy finds his way in the end: Closer \x93Dreamer in My Dreams\x94 may be the most unflinchingly personal song on the record, but it\x92s also the wildest, the wooliest, maybe the most raucous Wilco have ever sounded. \x93Well I know I\x92ve made mistakes,\x94 he sings; \x93I\x92m passing them on.\x94 Just when you think the song is ending, the band kicks it up again, as though reluctant to stop playing. Only when the song ends do they have to face real life again. At the heart of this bruised and tender record is the frazzled relationship between artist and audience, which makes this reissue more powerful and essential than your typical album repackaging. Digital media means we as an audience have less and less to give an artist, and rock\x91n\x92roll may be even more of a fool\x92s errand now than it was in 1996, when people bought CDs by the handful and even a mid-level band like pre-Foxtrot Wilco could make a comfortable living driving around the country in a van. Being There sounds even direr and more desperate than it once did, and the bonus material expands upon its themes. The \x93Party Horn\x94 mix of \x93Monday\x94 actually sounds richer and more excitable than the studio version, especially with its screeching sax solo. Most of the outtakes and alternate takes still have studio chatter attached to them, showing the band goofing around in the studio or half-assing the demos. \x93I think that was good enough,\x94 Tweedy deadpans after a beautifully understated version of \x93Dynamite My Soul.\x94 All of Tweedy\x92s big ideas about his vocation sound even more volatile on the two full live sets included on the 5xCD version, with \x93Sunken Treasure\x94 and \x93Hotel Arizona\x94 making the most of Tweedy\x92s rapport with the audience (\x93This is a true story. Sorta.\x94) and the noise of the crowd. Maybe that\x92s where the album\x92s title comes from. It is, of course, a reference to the 1979 Peter Sellers film, but it\x92s also a twist on the old adage about concerts and concert films: I guess you had to be there. We\x92ve always been a crucial part of Being There, always an unseen force motivating Wilco from one show to the next, but these live cuts make it explicit. It\x92s as though Tweedy played the long game on this sucker\x92s bet, waiting 21 years to make the ultimate version of Being There.',
            7.0],
           ['No Shame', 'Hopsin', 0, ..., 'Rap',
            'On his corrosive fifth album, the rapper takes aim at several targets and whiffs spectacularly. His verbose, tryhard technique is a tired formula he employs without flow, direction, or meaning. Hopsin\x97the California rapper whose constricting flows and humorless turns suck all the fun out of rapping\x97made his reputation appealing to the angstier corners of the internet, inhabited by rap moralists and \x93alternative thinkers\x94\x97those who earnestly think Tech N9ne is Peak Underground Rap. His new album, No Shame, is a bitter, petulant response to recent setbacks and mistakes: the collapse of his Funk Volume label, the souring of personal and professional relationships, and losing custody of his son after pleading guilty to assault. But he refuses to back down. \x93I built this empire off rebellion/Niggas seem to have a problem with what I be sellin\x92,\x94 he raps on \x93Panorama City,\x94 a song using a \x93California Love\x94 sample as a Thug Life signifier. When given the chance to explain himself, he doubles down on the worst aspects of an irritating persona. No Shame is a misfired revenge stunt. He takes aim at several targets and whiffs spectacularly, producing the worst album of his career, filled with tryhard wordplay and defamation that verges on abuse. \x93This album is backlash for somebody else. This album is pretty much just karma for people who have screwed me over or done something wrong to me,\x94 he told Tim Westwood. Those people are his former business partner, manager, and Funk Volume co-founder, Damien Ritter, and his ex-girlfriend, who Hopsin was arrested for assaulting in Australia last year. The barbs he has for Ritter are boring enough, but those reserved for his ex are particularly dark. Hopsin leaves no room for interpretation: He uses No Shame (and the press tour promoting it) to get back at the mother of his child, to damage her any way he can: \x93I fuckin\x92 hate this bitch, her name could sit on a grave/Only reason she ain\x92t dead is \x91cause my kid on the way/The bitch is pregnant and she strippin\x92, dodgin\x92 minimum wage/She done kick me down, locked me up, and spit on my face,\x94 he raps, enraged on \x93All Your Fault (Remix).\x94 This is a rapper using the full weight of his musical machine to discredit a woman he pled guilty to assaulting. And even for all his attempts to disgrace and humiliate her, smearing her with slut-shaming tactics and leveraging the entire force of his fan base against her, he still comes off looking much worse. Hopsin takes pride in his writing, making fun of \x93mumble rappers\x94 and those he perceives to be lesser lyricists, but his ideas are always articulated in the most corrosive ways possible; his set-ups and images are awkward, distasteful, and off-putting. Creepiness is an important part of his aesthetic\x97the colored contact lenses, deranged rambler rhyme mechanics (a la Eminem), and so-sinister-they\x92re-campy performances are all tools he uses to paint himself as a rap iconoclast. But even he pushes the limits of likability on No Shame. Slim Shady is his patron saint, and \x93Rap God\x94 is his scripture. All of his defining principles originate there, only executed without grace or guile. Even his horrible, vitriolic anti-ex tirades are cribbed right from the \x93Kim\x94 playbook.  Rage isn\x92t a substitute for artistry. He\x92ll rap nonsense for the sake of the scheme (\x94Napalm in my dang palm/I\x92m a dark villain like Blade, Spawn, or Akon with a cape on\x94). His scenes are insipid. His phrasings are either basic and unsophisticated or simply unnatural, playing up the phonetics as a distraction. \x93Evil and purgin\x92, I am more deceiving in person/Screamin\x92 and cursin\x92, fuck the world with penis insertions/I\x92ma feel this way until the day I\x92m leavin\x92 this Earth, man,\x94 he raps on \x93Witch Doctor.\x94 You can almost hear him asking himself, \x93what rhymes with this?\x94 making raps purposelessly denser with complete disregard for syntax. The songs that aren\x92t vehicles for his hatred are pointless exercises, each an elaborate convolution. Most Hopsin verses are assembled the same way, with choppy, rapid-fire cadences of multisyllabic filler. Every diphthong and accent ricochets off the next, creating the illusion of mastery when really, it\x92s less complex than letting an algorithm generate raps from scratch. There\x92s no value in craftsmanship alone; there has to be flow, direction, and meaning. \x93Rap monster, Black Mamba/You can\x92t run from the wrath I\x92ve been asked to cast on ya/To all the haters who been keepin\x92 up with my every move/Here\x92s my penis to latch on/I\x92m the only MC in this wack genre,\x94 he raps, following his technicality-first formula. And if there was any doubt that he\x92s detrimentally a creature of habit, he\x92s now nine songs deep in an \x93Ill Mind of Hopsin\x94 series that ran out of ideas four songs ago. Things get even more unpleasant when Hopsin takes detours. On \x93Happy Ending,\x94 he raps about getting off at a massage parlor in a terrible mock accent, mimicking the masseuse for the hook: \x93If you no say nothing, I can give you sucky-sucky.\x94 It would be one thing if the song was just crude or offensive or unlistenable, but it\x92s a trifecta. There aren\x92t any moments of remorse or thoughtfulness on No Shame, but there are a few moments of reflection. \x93I don\x92t like Marcus, I don\x92t like Hopsin/I am ashamed of them both,\x94 he raps on \x93Marcus\x92 Gospel\x94 before concluding, \x93I made my bed, I\x92ma lay in it.\x94 These bars would have you believe Hopsin has learned from his mistakes. But No Shame proves otherwise. The album rebukes any and all responsibility for his current predicament, shifting blame onto others. And in an attempt to weaponize his raps against his adversaries, he exposes his deepest flaws. His album is a reminder that shame is a productive and even necessary thing, keeping us from further making fools of ourselves.',
            3.5],
           ['Material Control', 'Glassjaw', 0, ..., 'Rock',
            'On their first album in 15 years, the Long Island post-hardcore band Glassjaw picks up right where they left off. But their heaviest material yet is, at times, lacking in dynamics and atmosphere. Material Control wouldn\x92t exist if Daryl Palumbo didn\x92t believe it was \x93the most Glassjaw-sounding music we could be making at this point.\x94 There is no doubting the purity of their vision, as lead single \x93New White Extremity\x94 is Glassjaw as hell. Its aggression and abrasive, abstract skronk tie together New York hardcore and no wave, mixed with astringent third-wave emo melody and the CGI\x92d musculature of nu-metal production\x97all with Palumbo doing his ocean-sized roar over it all like Long Island\x92s answer to Perry Farrell. In 2015, the band released \x93New White Extremity\x94 as a one-off, assumed to lead up to this very moment, where it serves as the opener for the first Glassjaw album in 15 years. If there was any doubt whether Glassjaw can pick up where they left off, Material Control answers it as immediately and literally as possible. Whether or not they were doubted, Glassjaw were going to act as if they were. They\x92re a band that\x92s thrived in a state of constant conflict. Fan favorites from their 2000 debut Everything You Ever Wanted to Know About Silence include \x93Pretty Lush\x94 and \x93Ry-Ry\x92s Song,\x94 chief exhibits among the evidence of their scene\x92s ingrained misogyny. These were scorched-earth responses to romantic betrayal performed with such energy that they sound like an endorsement. With their 2002 album Worship and Tribute, Glassjaw were positioned as post-hardcore\x92s Next Big Thing, forged under the pressure of Warner Bros. execs, the Twin Towers of nu-metal (producer Ross Robinson, mixer Chris Lord-Alge), and the band\x92s own outsized ambitions. It didn\x92t have the impact of, say, Relationship of Command, but like At the Drive In, Glassjaw followed their frenzied, fraught breakthrough by taking the next decade off. While creating Material Control, multi-instrumentalist Justin Beck (he and Palumbo constitute the core of Glassjaw) asked a rhetorical question: \x93Yo, if we were 14 again and recording with Martin Bisi and could just be the illest, grimiest band in New York, what would the environment be like?\x94 This is a smart approach, especially in the wake of recent albums from post-hardcore greats like At the Drive In, Refused, and Quicksand. Think of the fan who found those other albums to be hella soft, who wrongly assumed that Palumbo would remain too committed to the pop ambitions of his regrettably-named electronica outfits Head Automatica and Colour Film\x97Material Control is for that person. Material Control certainly can lay claim to being Glassjaw\x92s heaviest or most aggro album. And though most of Palumbo\x92s lyrics are too circumspect to get much across beyond their titles (\x93My Conscience Weighs a Ton,\x94 \x93Bibleland 6\x94), he says it\x92s political in a \x93George Carlin way.\x94 \x93New White Extremity\x94 surveys the demographics of a new Brooklyn, which Beck describes as \x93white people from Kansas.\x94 Despite their multimedia affinity for gruff, hardscrabble NYC cynicism, Material Control lives in the now, one of constant physical and mental confrontation and claustrophobia. Like their hardcore heroes, Glassjaw want you to feel the experience of getting constantly elbowed on the street and the subway, only now it also happens while staring at an iPhone and getting constantly prodded by bad opinions. Nearly every second of Material Control is meant to duplicate this sensory overload. Beck, in particular, puts forth a performance of remarkable endurance and dexterity, neck-snap riffs constantly battling his textural playing in the right channel, intended as field recordings from the concrete jungle\x97circular saws meeting sheet metal, the blare of car horns, jammed traffic. Without Dillinger Escape Plan to use as an outlet anymore, drummer Billy Rymer pushes his new band to their most feral extremes, shuttling from groove-metal, d-beat insanity, free-form dub, and tabla-spiked drone in consecutive tracks. But Glassjaw are so intent on proving they can still Glassjaw that they leave no space to build, to create dynamics or generate atmosphere. They really leave no space for Palumbo, and while there are distinct choruses, there are no hooks. There are more memorable basslines than vocal melodies. If Glassjaw was expected to top their forebears like Orange 9mm and Mind Over Matter as a commercial proposition, though, it was due to Palumbo\x97a \x93Jeff Buckley-in-hell post-hardcore,\x94 an unusually magnetic presence for this grotty realm. But he can\x92t work within these arrangements so much as over or under them. Palumbo elongates vowels while improvising pitch and hoping for the best. Elsewhere, he\x92s almost completely submerged in a mix created without nuance or mercy. If you can withstand the initial onslaught of Material Control, it can be plucked for stems: the cooed harmonies on \x93Shira,\x94 the warm bass countermelodies from \x93Golgotha,\x94 any of the individual elements of \x93Pretty Hell.\x94 The latter serves as the kind of U-turn into amniotic balladry that has sustained the midsections of Deftones albums for the past 20 years. Despite its studied dub production, \x93Pretty Hell\x94 is a mess that wastes the strongest chorus on an arrangement where no instrument appears to be aware of the other\x92s existence. There are points where Glassjaw can work towards a satisfying payoff rather than going all in at once, like the raga experiment \x93Bastille Day\x94 and the title track, which both aspire to the symphonic grandeur of major-label Trail of Dead. To Palumbo\x92s credit, he recognizes the potency of being that angry, impulsive 14-year-old hardcore kid\x97though he\x92s now channeling his adolescent rage towards more worthy targets. On an immediate level, Material Control is a record that simply feels good to have, specifically at the end of 2017\x97a time when everyone\x92s coming together to praise the year\x92s most agreeable rock records, when every day brings further proof that the arc of the moral universe has gone way the fuck off course, when you just want to scream into the void. Think of Material Control as a post-hardcore Long Island tea: it\x92ll take you back to being a teen when you could care less what was in it and how it was mixed together. The only thing that mattered was whether you got knocked on your ass.',
            6.6],
           ...,
           ['Singles Breaking Up, Vol. 1', 'Don Caballero', 0, ...,
            'Experimental',
            'Well, kids, I just went back and re-read my review for these guys\' last album, What Burns never Returns, and what a laugh it was! Sometimes I just love to pretend to be a record reviewer! In my last review, I stated that Don Caballero was "creating cohesive songs from the cacaphony of frenetically beaten drums." Yeah, right! More like creating a goopy mess of my brain between my ears. Well, here I find myself, living a Dilbert- like existence, not even given the dignity of a cubicle, forced to isolate myself the only way I know how: headphones filled with grunchy, mean toonz. Sure, sometimes I listen to porn MPEGs or NPR\'s This American Life, but when I\'m given a particularly enervating task-- something like, say, stapling-- I like to put my head between the hairy thighs of Don Caballero. Sure, it smells a little funky down there, and all\'s I can hear is the pounding of drums upon my skull, but I like it. I see it as an expression against the machine of oppression, man! Yeah, oppression... Don C., in case you\'ve been too lazy to go back and read the earlier review, is intense, indie jam-rok of the highest order. Perhaps you\'ve got friends that like to noodle around with offbeat guitar themes and love to hammer their drum sets without mercy. Perhaps they drink malt liquor and smell like clove cigarettes. Perhaps they stick sharp, barbed objects into their bottoms. Perhaps you have a very vague idea of what we\'re talking about here: jamming. Stinky, sore- bottomed jamming. Inspired, sore- bottomed jamming. Wikked, sharp, tight, sore- bottomed jamming. No lyrics, no mandolin, no techno beats or phat rhymes, just pure, glorious, sore- bottomed jamming. Thank You Jesus! So, Singles Breaking Up (Vol 1) collects the band\'s EP and compilation releases into one, easy- to- use compact disc. Convenient, and perfect for the on- the- go individual, Singles has the texture you\'d expect from such a collection-- lacking the overall continuity that What Burns Never Returns possessed, it serves to chronicle their progress since 1992, before they even released their first album, 1993\'s For Respect. Since this stuff is clearly marketed to instrumento-geeks and weirdos, I would suggest you take a large dose of Ketamine before enjoying. And don\'t forget to stick a sharp, barbed object into your bottom!',
            7.2],
           ['Out of Tune', 'Mojave 3', 0, ..., 'Rock',
            'Out of Tune is a Steve Martin album. Yes, I\'ll explain: \\n\\n\\ Once upon a time, there was ... Out of Tune is a Steve Martin album. Yes, I\'ll explain: Once upon a time, there was Steve Martin, a man with a plan to make us all laugh. His preferred method of inducing laughter was absurdity, doing his "Ramblin\' Man" thang for a while before finally finding a shortcut called "Excuuuuuuse me!" You kids these days may not know it, but if you said those two words just right, the whole wide world of 1976 was on their knees, suffocating with laughter. Continuing his domination by laughter, Martin did a few movies, most notably "The Jerk," one of the finest feature films of all time. Public opinion on "Just how funny is this Steve Martin guy, anyhow?" took a downslide once the \'80s began. His movies remained true to his desire to be Mr. Absurd ("The Man With Two Brains" probably being the apex of the truly hilarious/ nobody cares period), until finally he began to shift gears... crashing head on into a 1986 mess called "The Three Amigos." A horrible, unfunny film, Martin nonetheless recovered just a year later with "Roxanne" and one of his finest works, "Planes Trains and Automobiles." Do you see where I\'m going with this? Oh, wait. I haven\'t even gotten there. Martin went right into the toilet shortly thereafter, trying to first become more sitcom- friendly ("Houseguest"), then semi- dramatic ("Leap of Faith"), until he released a movie that nobody went to see called "A Simple Twist of Fate." Released in 1993, this was a seriously tragic and poorly- done flick about a man and his pet orphan who, if you can imagine, becomes the focus of a nasty custody dispute. When I first heard about this "Fate" bullshit, I thought it was a joke. Catching a few minutes of it a couple of weeks back, I was stunned. Here was a man who used to be in sync with that white noise comedy Robin Williams also excelled at. And, by the way, look at the unfortunate high grosses for "Patch Adams." Williams can at least laugh all the way to the bank with his betrayal of funny. Martin had to flop yet again with "The Out of Towners," but not before he did the also not- too- funny "Sgt. Bilko." Oh, yeah. Mojave 3. Three of these kids used to be in a band called Slowdive. Slowdive was to shoegazing what Steve Martin was to funny circa 1979. Slowdive wasn\'t more complex than their notorious peers, My Bloody Valentine, and they weren\'t as drunk as Ride, but they sure did make some purdy rekkids. Of course, they had to eventually blow off what they were good at to make their own "Simple Twist of Fate," an unfortunate album called Ask Me Tomorrow. And in their desire to be taken seriously, they cast off what made \'em so goddamned good in the first place. Out of Tune isn\'t a step back into Slowdive mode, but it is a much more winsome affair than Tomorrow. I had more hopes just based on the cover alone. (If there are any of those giant Eurpoean subway posters for this floating around, I have a wall in dire need of beauty.) Neil Halstead and Rachel Goswell sing a little differently than back in the day, going aurally naked (sans a zillion effects) and stepping out into the SoCal pot- happy sunshine folk- rock thing they\'re doing now. No, it doesn\'t sound like the Eagles, but a few cuts remind me of that godawful shit Ethan Hawke got to sing. Okay, not really, but somewhere Ethan Hawke is just popping a boner because someone mentioned his name in the press again. So, what do I think overall? It\'s not as meaty as it could be. Whereas Slowdive\'s lyrics were more enigmatic (or maybe appeared to be through a fog of tremelo), Mojave 3 goes for those bold statements that annoy me. Check these songs titles: "Who Do You Love" (no, not that one), "Some Kinda Angel" (where Goswell conjures up some Stevie Nicks mojo), "All Your Tears," "Baby\'s Coming Home..." you get the idea. Lots of acoustic guitars, organ, some pedal steel, and harmonies make this a pleasant enough sort of listen as long as I take a bong hit and stare at the cover. Or, to boil it all down into one sentence, this could very well be the first 4AD album to be fully embraced by both AAA radio stations and your mom. File it somewhere between "Housesitter" and "Father of the Bride." Post Review Note: I just got back from Cellophane Square music store on University Avenue in Seattle, and I guess I should mention that, according to a Mojave 3 display, a clerk named Karrie really digs the songs "Caught Beneath Your Feet" and "This Road I\'m Traveling." I\'m not doing this to hit on her-- I didn\'t ask for Karrie and for all I know she\'s an ugg-o-- but if you\'re a rabid Mojave 3 fan who doesn\'t like what I have to say, take Karrie\'s endorsement to heart.',
            6.3],
           ['Left for Dead in Malaysia', 'Neil Hamburger', 0, ..., 'None',
            'Neil Hamburger\'s third comedy release is a desperate affair, even for a sad sack such as America\'s Funnyman ... Neil Hamburger\'s third comedy release is a desperate affair, even for a sad sack such as America\'s Funnyman. Hamburger-- aka Gregg Turkington-- takes his routine to Kuala Lumpur, Malaysia, warming up the locals before karaoke time. Too bad nobody in the audience speaks or understands English. It\'s a broad setup knocked down with Hamburger\'s usual patheticness. (Sample joke: "How many Spice Girls does it take to screw in a lightbulb? Well, it was five, now it\'s four, and I\'m sure any day now it\'ll be down to three. Uh, huhhhh...") Early on in the show, Hamburger realizes no one in the crowd speaks English and, worse yet, his manager and right- hand man Art Huckman flees the scene in a huff. With no one left to lean on, Neil opens up about his past gigs performing at Amway conventions, his failed marriage, and a little something called "Montezuma\'s Revenge." Is it funny? Not really, but that\'s the point. Left for Dead in Malaysia is forty minutes of flop sweats, so much so that you might feel moist after listening. As usual, the joke is on Hamburger; is there a bigger loser on the planet than this guy? Yes, but the flat routine on Left for Dead may have you thinking otherwise. Hamburger (or Turkington, to be more precise) hits the desperation nail square on the head, but Raw Hamburger and America\'s Funnyman both fared a lot better. I\'m sure that there\'s going to be another Neil Hamburger album-- the stage is easily set for this loser to sink even lower-- and those who enjoyed the previous albums will enjoy Left for Dead more than the folks who don\'t get the joke. But there aren\'t really any jokes on this album, just a series of empty shells and failed observations... classic Hamburger.',
            6.5]], dtype=object)




```python
# sorting on current index
p4k.sort_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 7 columns</p>
</div>




```python
p4k.set_index('album') # set the data frame index using existing columns
# Note: index can be reset to default using reset_index()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
    <tr>
      <th>album</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A.M./Being There</th>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>No Shame</th>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>Material Control</th>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>Weighing of the Heart</th>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>The Visitor</th>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>Let Us Replay!</th>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>Singles Breaking Up, Vol. 1</th>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>Out of Tune</th>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>Left for Dead in Malaysia</th>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 6 columns</p>
</div>




```python
# multi-indexes can be set with set_index()
multindexP4k = p4k.set_index(['genre','date'])
multindexP4k.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>review</th>
      <th>score</th>
    </tr>
    <tr>
      <th>genre</th>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rock</th>
      <th>2017-12-06</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>Rap</th>
      <th>2017-12-06</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>Rock</th>
      <th>2017-12-06</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <th>2017-12-06</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>Rock</th>
      <th>2017-12-05</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
  </tbody>
</table>
</div>




```python
multindexP4k.index
# multindexP4k.index[0] # selecting first row from multi-index
```




    MultiIndex([(        'Rock', '2017-12-06'),
                (         'Rap', '2017-12-06'),
                (        'Rock', '2017-12-06'),
                (     'Pop/R&B', '2017-12-06'),
                (        'Rock', '2017-12-05'),
                (     'Pop/R&B', '2017-12-05'),
                (     'Pop/R&B', '2017-12-05'),
                (         'Rap', '2017-12-05'),
                (        'Rock', '2017-12-04'),
                (       'Metal', '2017-12-04'),
                ...
                (  'Electronic', '1999-02-11'),
                (  'Electronic', '1999-02-09'),
                (  'Electronic', '1999-02-09'),
                ('Folk/Country', '1999-02-09'),
                (  'Electronic', '1999-02-01'),
                (  'Electronic', '1999-01-26'),
                (  'Electronic', '1999-01-26'),
                ('Experimental', '1999-01-12'),
                (        'Rock', '1999-01-12'),
                (        'None', '1999-01-05')],
               names=['genre', 'date'], length=19555)




```python
multindexP4k.index.get_level_values(0)
```




    Index(['Rock', 'Rap', 'Rock', 'Pop/R&B', 'Rock', 'Pop/R&B', 'Pop/R&B', 'Rap',
           'Rock', 'Metal',
           ...
           'Electronic', 'Electronic', 'Electronic', 'Folk/Country', 'Electronic',
           'Electronic', 'Electronic', 'Experimental', 'Rock', 'None'],
          dtype='object', name='genre', length=19555)




```python
multindexP4k.index.get_level_values(1)
```




    DatetimeIndex(['2017-12-06', '2017-12-06', '2017-12-06', '2017-12-06',
                   '2017-12-05', '2017-12-05', '2017-12-05', '2017-12-05',
                   '2017-12-04', '2017-12-04',
                   ...
                   '1999-02-11', '1999-02-09', '1999-02-09', '1999-02-09',
                   '1999-02-01', '1999-01-26', '1999-01-26', '1999-01-12',
                   '1999-01-12', '1999-01-05'],
                  dtype='datetime64[ns]', name='date', length=19555, freq=None)




```python
multindexP4k.swaplevel() # Swapping levels
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>review</th>
      <th>score</th>
    </tr>
    <tr>
      <th>date</th>
      <th>genre</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">2017-12-06</th>
      <th>Rock</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>Rap</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>2017-12-05</th>
      <th>Rock</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">1999-01-26</th>
      <th>Electronic</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>Electronic</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">1999-01-12</th>
      <th>Experimental</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>1999-01-05</th>
      <th>None</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 5 columns</p>
</div>




```python
## Extract Rows from a MultiIndex Dataframe
# multindexP4k.loc[("2017-12-05","Rock")]
```


```python
multindexP4k
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>review</th>
      <th>score</th>
    </tr>
    <tr>
      <th>genre</th>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rock</th>
      <th>2017-12-06</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>Rap</th>
      <th>2017-12-06</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>Rock</th>
      <th>2017-12-06</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <th>2017-12-06</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>Rock</th>
      <th>2017-12-05</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Electronic</th>
      <th>1999-01-26</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>1999-01-26</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>Experimental</th>
      <th>1999-01-12</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>Rock</th>
      <th>1999-01-12</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>None</th>
      <th>1999-01-05</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 5 columns</p>
</div>



When working with multiple indexes, the **.stack()** and **unstack()** methods can help gather or spread the data


```python
multindexP4k.stack() # gathers all the column data into a new index with one columns holding all the values
multindexP4k.stack().to_frame() # Converting to a dataframe
```




    genre  date              
    Rock   2017-12-06  album                                      A.M./Being There
                       artist                                                Wilco
                       best                                                      1
                       review    Best new reissue 1 / 2 Albums Newly reissued a...
                       score                                                     7
                                                       ...                        
    None   1999-01-05  album                             Left for Dead in Malaysia
                       artist                                       Neil Hamburger
                       best                                                      0
                       review    Neil Hamburger's third comedy release is a des...
                       score                                                   6.5
    Length: 97769, dtype: object




```python
multindexP4k.stack().to_frame().index
```




    MultiIndex([('Rock', '2017-12-06',  'album'),
                ('Rock', '2017-12-06', 'artist'),
                ('Rock', '2017-12-06',   'best'),
                ('Rock', '2017-12-06', 'review'),
                ('Rock', '2017-12-06',  'score'),
                ( 'Rap', '2017-12-06',  'album'),
                ( 'Rap', '2017-12-06', 'artist'),
                ( 'Rap', '2017-12-06',   'best'),
                ( 'Rap', '2017-12-06', 'review'),
                ( 'Rap', '2017-12-06',  'score'),
                ...
                ('Rock', '1999-01-12',  'album'),
                ('Rock', '1999-01-12', 'artist'),
                ('Rock', '1999-01-12',   'best'),
                ('Rock', '1999-01-12', 'review'),
                ('Rock', '1999-01-12',  'score'),
                ('None', '1999-01-05',  'album'),
                ('None', '1999-01-05', 'artist'),
                ('None', '1999-01-05',   'best'),
                ('None', '1999-01-05', 'review'),
                ('None', '1999-01-05',  'score')],
               names=['genre', 'date', None], length=97769)




```python
# Unstack does the opposite, spreads the stacked data into multiple columns
s = multindexP4k.stack()
# s.unstack() by default works with the deepest layer. Throws an error on this data as there are duplicates entries
```


    --------------------------------------------------------------------------

    ValueError                               Traceback (most recent call last)

    <ipython-input-119-f95cb0e6bf0e> in <module>
          1 # Unstack does the opposite, spreads the stacked data into multiple columns
          2 s = multindexP4k.stack()
    ----> 3 s.unstack()


    ~/anaconda3/lib/python3.7/site-packages/pandas/core/series.py in unstack(self, level, fill_value)
       3548         from pandas.core.reshape.reshape import unstack
       3549
    -> 3550         return unstack(self, level, fill_value)
       3551
       3552     # ----------------------------------------------------------------------


    ~/anaconda3/lib/python3.7/site-packages/pandas/core/reshape/reshape.py in unstack(obj, level, fill_value)
        417             level=level,
        418             fill_value=fill_value,
    --> 419             constructor=obj._constructor_expanddim,
        420         )
        421         return unstacker.get_result()


    ~/anaconda3/lib/python3.7/site-packages/pandas/core/reshape/reshape.py in __init__(self, values, index, level, value_columns, fill_value, constructor)
        139
        140         self._make_sorted_values_labels()
    --> 141         self._make_selectors()
        142
        143     def _make_sorted_values_labels(self):


    ~/anaconda3/lib/python3.7/site-packages/pandas/core/reshape/reshape.py in _make_selectors(self)
        177
        178         if mask.sum() < len(self.index):
    --> 179             raise ValueError("Index contains duplicate entries, cannot reshape")
        180
        181         self.group_index = comp_index


    ValueError: Index contains duplicate entries, cannot reshape


### 9. Renaming Labels and Columns

_key methods: .rename()_



```python
# Renaming columns with a dictionary. This method can rename all or some columns
p4k.columns
p4k.rename(columns = {"Parent Company" : "parent_company",
                      "album" : "album_title",
                      "artist" : "artist_name",
                      "best" : "best_reviewed",
                      "date" : "review_date",
                      "genre" : "genre_type",
                      "review" : "review_text",
                      "score" : "review_score",
                      "Review Language" : "review_lang",
                      "score_rank" : "ranked_score"}
                       ,inplace=False)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>parent_company</th>
      <th>album_title</th>
      <th>artist_name</th>
      <th>best_reviewed</th>
      <th>review_date</th>
      <th>genre_type</th>
      <th>review_text</th>
      <th>review_score</th>
      <th>review_lang</th>
      <th>ranked_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
      <td>13879.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0.0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
      <td>587.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0.0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
      <td>14246.0</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 10 columns</p>
</div>




```python
# This method requires renaming ALL columns
# p4k.columns = ["c1","c2","c3","c4","c5","c6","c7","c8","c9","c10"]
```


```python
p4k.rename(index = {'1' : 'One',
                    '2' : 'Two'})
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vox</td>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>ENG</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Vox</td>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>ENG</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Vox</td>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>ENG</td>
      <td>13879.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Vox</td>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>ENG</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Vox</td>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0.0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Vox</td>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0.0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>ENG</td>
      <td>587.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Vox</td>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>ENG</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Vox</td>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0.0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>ENG</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Vox</td>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0.0</td>
      <td>January 5 1999</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>ENG</td>
      <td>14246.0</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 10 columns</p>
</div>



### 10. Sampling

_key methods: .sample()_

A random sample of either an integer count or a fraction of the total can be done through **.sample()**


```python
p4k.sample(frac=0.01)
p4k.sample(n=3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Parent Company</th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>Review Language</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18004</th>
      <td>Vox</td>
      <td>Dirty Vegas</td>
      <td>Dirty Vegas</td>
      <td>0.0</td>
      <td>July 8 2002</td>
      <td>Electronic</td>
      <td>Used to be, way back in the twentieth century,...</td>
      <td>4.4</td>
      <td>ENG</td>
      <td>18683.0</td>
    </tr>
    <tr>
      <th>9255</th>
      <td>Vox</td>
      <td>The Creatures in the Garden of Lady Walton / V...</td>
      <td>Clogs</td>
      <td>0.0</td>
      <td>March 3 2010</td>
      <td>Rock</td>
      <td>1 / 2 Albums The National's Bryce Dessner's po...</td>
      <td>8.2</td>
      <td>ENG</td>
      <td>2308.0</td>
    </tr>
    <tr>
      <th>7502</th>
      <td>Vox</td>
      <td>I Am an Attic</td>
      <td>Nick Diamonds</td>
      <td>0.0</td>
      <td>August 26 2011</td>
      <td>Rock</td>
      <td>The busy collaborator and frontman (Mister Hea...</td>
      <td>6.4</td>
      <td>ENG</td>
      <td>14728.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# By default rows are sampled, but columns can be sampled as well
p4k.sample(n=2,axis=1)
p4k.sample(n=2,axis="columns")
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>score_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>11104.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>19131.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>13879.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>5691.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>13370.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>The marketing guys of yer average modern megac...</td>
      <td>587.0</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>9807.0</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>15110.0</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>14246.0</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19556 rows × 2 columns</p>
</div>



### 11. Grouping and Agg

_key methods: .groupby(), .agg()_


```python
p4k.groupby('genre').size()
```




    genre
    Electronic      4020
    Experimental    1699
    Folk/Country     700
    Global           178
    Jazz             257
    Metal            781
    None            2324
    Pop/R&B         1157
    Rap             1481
    Rock            6958
    dtype: int64




```python
p4k.groupby(['genre','best']).size()
```




    genre         best
    Electronic    0       3827
                  1        193
    Experimental  0       1588
                  1        111
    Folk/Country  0        667
                  1         33
    Global        0        169
                  1          9
    Jazz          0        232
                  1         25
    Metal         0        759
                  1         22
    None          0       2269
                  1         55
    Pop/R&B       0       1070
                  1         87
    Rap           0       1399
                  1         82
    Rock          0       6535
                  1        423
    dtype: int64




```python
genre_group = p4k.groupby('genre')
```


```python
genre_group.sum() # by default, will only apply to numeric columns
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>best</th>
      <th>score</th>
    </tr>
    <tr>
      <th>genre</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Electronic</th>
      <td>193</td>
      <td>27904.1</td>
    </tr>
    <tr>
      <th>Experimental</th>
      <td>111</td>
      <td>12503.0</td>
    </tr>
    <tr>
      <th>Folk/Country</th>
      <td>33</td>
      <td>5053.9</td>
    </tr>
    <tr>
      <th>Global</th>
      <td>9</td>
      <td>1323.4</td>
    </tr>
    <tr>
      <th>Jazz</th>
      <td>25</td>
      <td>1945.1</td>
    </tr>
    <tr>
      <th>Metal</th>
      <td>22</td>
      <td>5451.7</td>
    </tr>
    <tr>
      <th>None</th>
      <td>55</td>
      <td>16315.9</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <td>87</td>
      <td>8039.2</td>
    </tr>
    <tr>
      <th>Rap</th>
      <td>82</td>
      <td>10293.4</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>423</td>
      <td>48592.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
genre_group["score"].std() # standard deviation of scores by group
```




    genre
    Electronic      1.286230
    Experimental    1.084305
    Folk/Country    1.039006
    Global          1.020147
    Jazz            1.160742
    Metal           1.382885
    None            1.232897
    Pop/R&B         1.251231
    Rap             1.259559
    Rock            1.336472
    Name: score, dtype: float64




```python
genre_group["score"].describe() # Describe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>genre</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Electronic</th>
      <td>4020.0</td>
      <td>6.941318</td>
      <td>1.286230</td>
      <td>0.2</td>
      <td>6.4</td>
      <td>7.2</td>
      <td>7.8</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Experimental</th>
      <td>1699.0</td>
      <td>7.359035</td>
      <td>1.084305</td>
      <td>0.3</td>
      <td>6.9</td>
      <td>7.5</td>
      <td>8.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Folk/Country</th>
      <td>700.0</td>
      <td>7.219857</td>
      <td>1.039006</td>
      <td>2.3</td>
      <td>6.8</td>
      <td>7.4</td>
      <td>7.9</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Global</th>
      <td>178.0</td>
      <td>7.434831</td>
      <td>1.020147</td>
      <td>2.2</td>
      <td>7.1</td>
      <td>7.7</td>
      <td>8.0</td>
      <td>9.4</td>
    </tr>
    <tr>
      <th>Jazz</th>
      <td>257.0</td>
      <td>7.568482</td>
      <td>1.160742</td>
      <td>1.0</td>
      <td>7.0</td>
      <td>7.7</td>
      <td>8.2</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Metal</th>
      <td>781.0</td>
      <td>6.980410</td>
      <td>1.382885</td>
      <td>0.2</td>
      <td>6.5</td>
      <td>7.4</td>
      <td>7.9</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>None</th>
      <td>2324.0</td>
      <td>7.020611</td>
      <td>1.232897</td>
      <td>0.0</td>
      <td>6.5</td>
      <td>7.2</td>
      <td>7.8</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <td>1157.0</td>
      <td>6.948315</td>
      <td>1.251231</td>
      <td>0.0</td>
      <td>6.3</td>
      <td>7.1</td>
      <td>7.7</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Rap</th>
      <td>1481.0</td>
      <td>6.950304</td>
      <td>1.259559</td>
      <td>1.0</td>
      <td>6.4</td>
      <td>7.2</td>
      <td>7.8</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>6958.0</td>
      <td>6.983616</td>
      <td>1.336472</td>
      <td>0.0</td>
      <td>6.4</td>
      <td>7.2</td>
      <td>7.8</td>
      <td>10.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
best_genre = p4k.groupby(['genre','best'])
```


```python
# returning most recent best=1 reviews by genre
best_genre['date'].max()
```




    genre         best
    Electronic    0      2017-11-30
                  1      2017-11-27
    Experimental  0      2017-11-30
                  1      2017-10-04
    Folk/Country  0      2017-11-29
                  1      2017-08-11
    Global        0      2017-09-29
                  1      2017-10-04
    Jazz          0      2017-11-29
                  1      2017-11-10
    Metal         0      2017-12-04
                  1      2017-12-02
    None          0      2017-12-01
                  1      2017-10-25
    Pop/R&B       0      2017-12-06
                  1      2017-12-05
    Rap           0      2017-12-06
                  1      2017-10-07
    Rock          0      2017-12-06
                  1      2017-12-06
    Name: date, dtype: datetime64[ns]



Aggregate - aggregate data using one or more operations over the specified axis.




```python
genre_group.agg({"best" : "sum",
             "score" : "mean",
             "date": "min"}) # can specify methods by columns using a python dictionary
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>best</th>
      <th>score</th>
      <th>date</th>
    </tr>
    <tr>
      <th>genre</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Electronic</th>
      <td>193</td>
      <td>6.941318</td>
      <td>1999-01-26</td>
    </tr>
    <tr>
      <th>Experimental</th>
      <td>111</td>
      <td>7.359035</td>
      <td>1999-01-12</td>
    </tr>
    <tr>
      <th>Folk/Country</th>
      <td>33</td>
      <td>7.219857</td>
      <td>1999-02-09</td>
    </tr>
    <tr>
      <th>Global</th>
      <td>9</td>
      <td>7.434831</td>
      <td>1999-06-01</td>
    </tr>
    <tr>
      <th>Jazz</th>
      <td>25</td>
      <td>7.568482</td>
      <td>1999-06-08</td>
    </tr>
    <tr>
      <th>Metal</th>
      <td>22</td>
      <td>6.980410</td>
      <td>1999-02-16</td>
    </tr>
    <tr>
      <th>None</th>
      <td>55</td>
      <td>7.020611</td>
      <td>1999-01-05</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <td>87</td>
      <td>6.948315</td>
      <td>1999-02-23</td>
    </tr>
    <tr>
      <th>Rap</th>
      <td>82</td>
      <td>6.950304</td>
      <td>1999-10-12</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>423</td>
      <td>6.983616</td>
      <td>1999-01-12</td>
    </tr>
  </tbody>
</table>
</div>




```python
# giving a list instead for multiple methods to each col
genre_group.agg(['size','sum','mean'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="3" halign="left">best</th>
      <th colspan="3" halign="left">score</th>
    </tr>
    <tr>
      <th></th>
      <th>size</th>
      <th>sum</th>
      <th>mean</th>
      <th>size</th>
      <th>sum</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>genre</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Electronic</th>
      <td>4020</td>
      <td>193</td>
      <td>0.048010</td>
      <td>4020</td>
      <td>27904.1</td>
      <td>6.941318</td>
    </tr>
    <tr>
      <th>Experimental</th>
      <td>1699</td>
      <td>111</td>
      <td>0.065333</td>
      <td>1699</td>
      <td>12503.0</td>
      <td>7.359035</td>
    </tr>
    <tr>
      <th>Folk/Country</th>
      <td>700</td>
      <td>33</td>
      <td>0.047143</td>
      <td>700</td>
      <td>5053.9</td>
      <td>7.219857</td>
    </tr>
    <tr>
      <th>Global</th>
      <td>178</td>
      <td>9</td>
      <td>0.050562</td>
      <td>178</td>
      <td>1323.4</td>
      <td>7.434831</td>
    </tr>
    <tr>
      <th>Jazz</th>
      <td>257</td>
      <td>25</td>
      <td>0.097276</td>
      <td>257</td>
      <td>1945.1</td>
      <td>7.568482</td>
    </tr>
    <tr>
      <th>Metal</th>
      <td>781</td>
      <td>22</td>
      <td>0.028169</td>
      <td>781</td>
      <td>5451.7</td>
      <td>6.980410</td>
    </tr>
    <tr>
      <th>None</th>
      <td>2324</td>
      <td>55</td>
      <td>0.023666</td>
      <td>2324</td>
      <td>16315.9</td>
      <td>7.020611</td>
    </tr>
    <tr>
      <th>Pop/R&amp;B</th>
      <td>1157</td>
      <td>87</td>
      <td>0.075194</td>
      <td>1157</td>
      <td>8039.2</td>
      <td>6.948315</td>
    </tr>
    <tr>
      <th>Rap</th>
      <td>1481</td>
      <td>82</td>
      <td>0.055368</td>
      <td>1481</td>
      <td>10293.4</td>
      <td>6.950304</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>6958</td>
      <td>423</td>
      <td>0.060793</td>
      <td>6958</td>
      <td>48592.0</td>
      <td>6.983616</td>
    </tr>
  </tbody>
</table>
</div>



### 12. Date and Times

_key attributes: .year, .month, .day, .dayofweek, .hour, .minute, .second_

_key methods: .Timestamp(), .to_datetime. .date_range(), .truncate(), .dateoffset(), .Timedelta()_

The following functions for working more closely with dates come both pandas and from the [datetime](https://docs.python.org/3/library/datetime.html) library.


```python
import datetime as dt
```

Creating dates and datetimes


```python
# Creating dates
someday = dt.date(2016,4,12)
someday
```




    datetime.date(2016, 4, 12)




```python
# extracting date values
someday.year
someday.month
someday.day
```




    12




```python
# Creating datetimes
somedatetime = dt.datetime(2016,4,12,8,13,59)
somedatetime
```




    datetime.datetime(2016, 4, 12, 8, 13, 59)




```python
# extracting date and time values
somedatetime.year
somedatetime.month
somedatetime.day
someday.dayofweek
somedatetime.hour
somedatetime.minute
somedatetime.second
```




    59



The pandas **.Timestamp()** and **.to_datetime().** method can work with a variety of input formats


```python
pd.Timestamp('2015-3-31')
pd.Timestamp('2015/3/31')
pd.Timestamp('2015, 3, 31')
```




    Timestamp('2015-03-31 00:00:00')




```python
pd.Timestamp('1/1/2012 08:35:15')
pd.Timestamp('1/1/2012 08:35:15 PM')
pd.Timestamp(dt.datetime(2012,1,1,8,35,5))
```




    Timestamp('2012-01-01 08:35:05')




```python
pd.to_datetime("2001-04-19")
pd.to_datetime(dt.date(2015,1,1))
pd.to_datetime(dt.datetime(2015,2,2,14,35,20))
pd.to_datetime(["2015-01-03","2014/02/08","2016","July 4th, 1996"])
```


```python
times = pd.Series(["2015-01-03","2014/02/08","2016","July 4th, 1996"])
times
```


```python
pd.to_datetime(times)
```

pd.to_datetime can also work with bad data provided the errors are coerced:


```python
bad_dates = pd.Series(["July 14th 1995","Hello","2015-2-31"])
```


```python
pd.to_datetime(bad_dates)
# To handle this set errors="coerce" to fix those it cant. Otherwise will be 'NAT', not a time
```


```python
pd.to_datetime(bad_dates,errors="coerce")
```

The method **pd.date_range()** is used to create a range of dates of different frequnecies


```python
times = pd.date_range(start="2016-01-01",end="2016-01-11",freq="D")
```


```python
pd.date_range(start="2016-01-01",end="2016-01-11",freq="2D") # 2 day incriment
pd.date_range(start="2016-01-01",end="2016-01-11",freq="B") # B -Just weekdays
pd.date_range(start="2016-01-01",end="2016-01-16",freq="W") # Weeks (starting on sunday)
pd.date_range(start="2016-01-01",end="2016-01-16",freq="W-FRI") # W-Fri start on Friday
pd.date_range(start="2016-01-01",end="2016-01-16",freq="H") # H - Hour
pd.date_range(start="2016-01-01",end="2016-10-16",freq="M") # M - Month end

pd.date_range(start="2012-09-09",periods=25,freq="D")  #Periods = # of results
pd.date_range(start="2012-09-09",periods=4,freq="W")  #Periods = # of results
```

Date series or columns can be filtered using **.truncate()**


```python
dp4k = p4k.set_index('date')
dp4k
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-06</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2017-12-06</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>2017-12-06</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>2017-12-06</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>2017-12-05</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1999-01-26</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>1999-01-26</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>1999-01-12</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>1999-01-12</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>1999-01-05</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 6 columns</p>
</div>




```python
dp4k.truncate(before="2010-02-05")
dp4k.truncate(after="2017-12-05")
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-12-06</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2017-12-06</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>2017-12-06</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>2017-12-06</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>2017-12-05</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>2017-12-05</th>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>2017-12-05</th>
      <td>Everyday Is Christmas</td>
      <td>Sia</td>
      <td>0</td>
      <td>Pop/R&amp;B</td>
      <td>Sias shiny Christmas album feels inconsistent...</td>
      <td>5.8</td>
    </tr>
    <tr>
      <th>2017-12-05</th>
      <td>Zaytown Sorority Class of 2017</td>
      <td>Zaytoven</td>
      <td>0</td>
      <td>Rap</td>
      <td>The prolific Atlanta producer enlists 17 women...</td>
      <td>6.2</td>
    </tr>
  </tbody>
</table>
</div>



Timedeltas can be calculated from two date or datetime objects using arithmetic


```python
timeA = pd.Timestamp("2016-03-31 04:35:16 PM")
timeB = pd.Timestamp("2016-03-20")

timeA - timeB
timeB - timeA
```




    Timedelta('-12 days +07:24:44')




```python
pd.Timedelta(days=3) # creating an 'empty' 3 day timedelta
pd.Timedelta(days=3,hours=2,minutes=23) # 3 days + 2 hours + 2 minutes
pd.Timedelta("14 days 6 hours 12 minutes 49 seconds") # can even be done with a string
```




    Timedelta('3 days 00:00:00')




```python
timeB + pd.Timedelta(days=3)
```




    Timestamp('2016-03-23 00:00:00')



An extension of this is the **.dateoffset()** method


```python

from pandas.tseries.offsets import *

# adding two days
p4k.date + pd.DateOffset(days=5)
# subtracting 2 weeks
p4k.date + pd.DateOffset(weeks=-2)
# Adding a year
p4k.date + pd.DateOffset(years=1)
# multiple
p4k.date + pd.DateOffset(years=1,days=-3)
```




    1       2018-12-03
    2       2018-12-03
    3       2018-12-03
    4       2018-12-03
    5       2018-12-02
               ...    
    19551   2000-01-23
    19552   2000-01-23
    19553   2000-01-09
    19554   2000-01-09
    19555   2000-01-02
    Name: date, Length: 19555, dtype: datetime64[ns]




```python
p4k.date + pd.tseries.offsets.MonthEnd() # looks for next month end
p4k.date - pd.tseries.offsets.MonthEnd() # looks for last month end
p4k.date + MonthBegin() # looks next beginning of month
p4k.date + BMonthEnd() # last business day of the month
p4k.date + QuarterEnd() # end of that quarter
p4k.date + YearEnd() # end of that year
```




    1       2017-12-31
    2       2017-12-31
    3       2017-12-31
    4       2017-12-31
    5       2017-12-31
               ...    
    19551   1999-12-31
    19552   1999-12-31
    19553   1999-12-31
    19554   1999-12-31
    19555   1999-12-31
    Name: date, Length: 19555, dtype: datetime64[ns]



### 13. Text Functions

_key attributes: .lower, .upper, .title, .len_

_key methods: .replace(), .contains(), .startswith(), .endswith(), .lstrip(), .rstrip(), .strip(), .title(), .split(), .get()_




```python
testString = "Education is what you get from reading the small print; experience is what you get from not reading it."
```


```python
testString.upper() # all upper case
testString.lower() # all lower case
testString.title() # all first characters capitalized
len(testString) # number of characters
```




    103




```python
testString.replace("reading","slowly reading") # replacing strings
```




    'Education is what you get from slowly reading the small print; experience is what you get from not slowly reading it.'




```python
testBlankString = "   In the middle  "
```


```python
testBlankString.lstrip() # stripping blank text on the left
testBlankString.rstrip() # stripping blank text on the left
testBlankString.strip() # stripping blank text on bot ends
```




    'In the middle'




```python
testBlankString.split() # breaks string apart into individual words
# testBlankString.split(expand=True) #parameter 'expand' will return a df, not list
```




    ['In', 'the', 'middle']



### 14. Merging, Joining, and Concatenating

_key methods: .concat(), .append(), .merge(), .join()_


```python
# .concat() is used to row bind two datasets, assuming they have the same columns

p4k1 = p4k.sample(n=12) # random sample of 12 rows
p4k2 = p4k.sample(n=5) # random sample of 5 rows

pd.concat([p4k1,p4k2], ignore_index=True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>River of Souls</td>
      <td>Magic Trick</td>
      <td>0</td>
      <td>2014-01-09</td>
      <td>None</td>
      <td>Tim Cohen wrote the songs for River of Souls w...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I Predict a Graceful Expulsion</td>
      <td>Cold Specks</td>
      <td>0</td>
      <td>2012-05-01</td>
      <td>None</td>
      <td>The 24-year old Canadian singer's transfixing ...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False Flag</td>
      <td>Rangda</td>
      <td>0</td>
      <td>2010-05-18</td>
      <td>Experimental</td>
      <td>This out-rock supergroup featuring Sir Richard...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Rare Grooves</td>
      <td>Various Artists</td>
      <td>0</td>
      <td>2005-03-17</td>
      <td>None</td>
      <td>Ireland's Bassbin label continues the improbab...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A Tribute to Brother Weldon</td>
      <td>Monk Hughes &amp; The Outer Realm</td>
      <td>0</td>
      <td>2004-07-29</td>
      <td>Jazz</td>
      <td>It's tough not to love a schizophrenic genius....</td>
      <td>5.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Temporary Pleasure</td>
      <td>Simian Mobile Disco</td>
      <td>0</td>
      <td>2009-09-03</td>
      <td>Electronic</td>
      <td>A grab bag of styles, SMD's latest features Ho...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Lauschen</td>
      <td>Qluster</td>
      <td>0</td>
      <td>2013-02-15</td>
      <td>Electronic</td>
      <td>Hans-Joachim Roedelius (Zodiak Free Arts Lab, ...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Hungry for Nothing</td>
      <td>Fight Amp</td>
      <td>0</td>
      <td>2008-03-20</td>
      <td>Metal</td>
      <td>This New Jersey band harkens back to a time wh...</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Chewed Corners</td>
      <td>µ-Ziq</td>
      <td>0</td>
      <td>2013-07-22</td>
      <td>Electronic</td>
      <td>Planet Mu's Mike Paradinas has never reached t...</td>
      <td>7.1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Backlash</td>
      <td>Black Joe Lewis &amp; the Honeybears</td>
      <td>0</td>
      <td>2017-02-07</td>
      <td>Rock</td>
      <td>Black Joe Lewis trawls the familiar intersecti...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Life Ain't No Joke</td>
      <td>Lil Durk</td>
      <td>0</td>
      <td>2012-12-11</td>
      <td>Rap</td>
      <td>The Chicago teen and Chief Keef associate has ...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Melo de Melo EP</td>
      <td>Ricardo Villalobos / Umho</td>
      <td>0</td>
      <td>2016-12-15</td>
      <td>Electronic</td>
      <td>On this two-track EP, Ricardo Villalobos teams...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Effigies of Evil</td>
      <td>Hooded Menace</td>
      <td>0</td>
      <td>2012-09-18</td>
      <td>None</td>
      <td>The Finnish doom metal group has always cited ...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ominosity</td>
      <td>Milemarker</td>
      <td>0</td>
      <td>2005-12-14</td>
      <td>Metal</td>
      <td>Fifth album from the shapeshifting post-hardco...</td>
      <td>6.8</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Topsiders</td>
      <td>Aden</td>
      <td>0</td>
      <td>2002-08-29</td>
      <td>Rock</td>
      <td>Good news, punks! Thanks to your complete lack...</td>
      <td>4.6</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Meds</td>
      <td>Placebo</td>
      <td>0</td>
      <td>2006-05-04</td>
      <td>Rock</td>
      <td>Latest record from the oddly successful glam- ...</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Field Report</td>
      <td>Field Report</td>
      <td>0</td>
      <td>2012-09-13</td>
      <td>None</td>
      <td>Chris Porterfield, the onetime bandmate of Jus...</td>
      <td>6.6</td>
    </tr>
  </tbody>
</table>
</div>




```python
pd.concat([p4k1,p4k2], ignore_index=False)  # setting to false maintains old index
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4741</th>
      <td>River of Souls</td>
      <td>Magic Trick</td>
      <td>0</td>
      <td>2014-01-09</td>
      <td>None</td>
      <td>Tim Cohen wrote the songs for River of Souls w...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>6715</th>
      <td>I Predict a Graceful Expulsion</td>
      <td>Cold Specks</td>
      <td>0</td>
      <td>2012-05-01</td>
      <td>None</td>
      <td>The 24-year old Canadian singer's transfixing ...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>8985</th>
      <td>False Flag</td>
      <td>Rangda</td>
      <td>0</td>
      <td>2010-05-18</td>
      <td>Experimental</td>
      <td>This out-rock supergroup featuring Sir Richard...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>15171</th>
      <td>Rare Grooves</td>
      <td>Various Artists</td>
      <td>0</td>
      <td>2005-03-17</td>
      <td>None</td>
      <td>Ireland's Bassbin label continues the improbab...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>15885</th>
      <td>A Tribute to Brother Weldon</td>
      <td>Monk Hughes &amp; The Outer Realm</td>
      <td>0</td>
      <td>2004-07-29</td>
      <td>Jazz</td>
      <td>It's tough not to love a schizophrenic genius....</td>
      <td>5.5</td>
    </tr>
    <tr>
      <th>9808</th>
      <td>Temporary Pleasure</td>
      <td>Simian Mobile Disco</td>
      <td>0</td>
      <td>2009-09-03</td>
      <td>Electronic</td>
      <td>A grab bag of styles, SMD's latest features Ho...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>5797</th>
      <td>Lauschen</td>
      <td>Qluster</td>
      <td>0</td>
      <td>2013-02-15</td>
      <td>Electronic</td>
      <td>Hans-Joachim Roedelius (Zodiak Free Arts Lab, ...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>11543</th>
      <td>Hungry for Nothing</td>
      <td>Fight Amp</td>
      <td>0</td>
      <td>2008-03-20</td>
      <td>Metal</td>
      <td>This New Jersey band harkens back to a time wh...</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>5263</th>
      <td>Chewed Corners</td>
      <td>µ-Ziq</td>
      <td>0</td>
      <td>2013-07-22</td>
      <td>Electronic</td>
      <td>Planet Mu's Mike Paradinas has never reached t...</td>
      <td>7.1</td>
    </tr>
    <tr>
      <th>1075</th>
      <td>Backlash</td>
      <td>Black Joe Lewis &amp; the Honeybears</td>
      <td>0</td>
      <td>2017-02-07</td>
      <td>Rock</td>
      <td>Black Joe Lewis trawls the familiar intersecti...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>5977</th>
      <td>Life Ain't No Joke</td>
      <td>Lil Durk</td>
      <td>0</td>
      <td>2012-12-11</td>
      <td>Rap</td>
      <td>The Chicago teen and Chief Keef associate has ...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>1234</th>
      <td>Melo de Melo EP</td>
      <td>Ricardo Villalobos / Umho</td>
      <td>0</td>
      <td>2016-12-15</td>
      <td>Electronic</td>
      <td>On this two-track EP, Ricardo Villalobos teams...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>6265</th>
      <td>Effigies of Evil</td>
      <td>Hooded Menace</td>
      <td>0</td>
      <td>2012-09-18</td>
      <td>None</td>
      <td>The Finnish doom metal group has always cited ...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>14232</th>
      <td>Ominosity</td>
      <td>Milemarker</td>
      <td>0</td>
      <td>2005-12-14</td>
      <td>Metal</td>
      <td>Fifth album from the shapeshifting post-hardco...</td>
      <td>6.8</td>
    </tr>
    <tr>
      <th>17861</th>
      <td>Topsiders</td>
      <td>Aden</td>
      <td>0</td>
      <td>2002-08-29</td>
      <td>Rock</td>
      <td>Good news, punks! Thanks to your complete lack...</td>
      <td>4.6</td>
    </tr>
    <tr>
      <th>13783</th>
      <td>Meds</td>
      <td>Placebo</td>
      <td>0</td>
      <td>2006-05-04</td>
      <td>Rock</td>
      <td>Latest record from the oddly successful glam- ...</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>6282</th>
      <td>Field Report</td>
      <td>Field Report</td>
      <td>0</td>
      <td>2012-09-13</td>
      <td>None</td>
      <td>Chris Porterfield, the onetime bandmate of Jus...</td>
      <td>6.6</td>
    </tr>
  </tbody>
</table>
</div>




```python
# append is very similar to concat
p4k1.append(p4k2, ignore_index=True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>River of Souls</td>
      <td>Magic Trick</td>
      <td>0</td>
      <td>2014-01-09</td>
      <td>None</td>
      <td>Tim Cohen wrote the songs for River of Souls w...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>I Predict a Graceful Expulsion</td>
      <td>Cold Specks</td>
      <td>0</td>
      <td>2012-05-01</td>
      <td>None</td>
      <td>The 24-year old Canadian singer's transfixing ...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False Flag</td>
      <td>Rangda</td>
      <td>0</td>
      <td>2010-05-18</td>
      <td>Experimental</td>
      <td>This out-rock supergroup featuring Sir Richard...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Rare Grooves</td>
      <td>Various Artists</td>
      <td>0</td>
      <td>2005-03-17</td>
      <td>None</td>
      <td>Ireland's Bassbin label continues the improbab...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A Tribute to Brother Weldon</td>
      <td>Monk Hughes &amp; The Outer Realm</td>
      <td>0</td>
      <td>2004-07-29</td>
      <td>Jazz</td>
      <td>It's tough not to love a schizophrenic genius....</td>
      <td>5.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Temporary Pleasure</td>
      <td>Simian Mobile Disco</td>
      <td>0</td>
      <td>2009-09-03</td>
      <td>Electronic</td>
      <td>A grab bag of styles, SMD's latest features Ho...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Lauschen</td>
      <td>Qluster</td>
      <td>0</td>
      <td>2013-02-15</td>
      <td>Electronic</td>
      <td>Hans-Joachim Roedelius (Zodiak Free Arts Lab, ...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Hungry for Nothing</td>
      <td>Fight Amp</td>
      <td>0</td>
      <td>2008-03-20</td>
      <td>Metal</td>
      <td>This New Jersey band harkens back to a time wh...</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Chewed Corners</td>
      <td>µ-Ziq</td>
      <td>0</td>
      <td>2013-07-22</td>
      <td>Electronic</td>
      <td>Planet Mu's Mike Paradinas has never reached t...</td>
      <td>7.1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Backlash</td>
      <td>Black Joe Lewis &amp; the Honeybears</td>
      <td>0</td>
      <td>2017-02-07</td>
      <td>Rock</td>
      <td>Black Joe Lewis trawls the familiar intersecti...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Life Ain't No Joke</td>
      <td>Lil Durk</td>
      <td>0</td>
      <td>2012-12-11</td>
      <td>Rap</td>
      <td>The Chicago teen and Chief Keef associate has ...</td>
      <td>6.5</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Melo de Melo EP</td>
      <td>Ricardo Villalobos / Umho</td>
      <td>0</td>
      <td>2016-12-15</td>
      <td>Electronic</td>
      <td>On this two-track EP, Ricardo Villalobos teams...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Effigies of Evil</td>
      <td>Hooded Menace</td>
      <td>0</td>
      <td>2012-09-18</td>
      <td>None</td>
      <td>The Finnish doom metal group has always cited ...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ominosity</td>
      <td>Milemarker</td>
      <td>0</td>
      <td>2005-12-14</td>
      <td>Metal</td>
      <td>Fifth album from the shapeshifting post-hardco...</td>
      <td>6.8</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Topsiders</td>
      <td>Aden</td>
      <td>0</td>
      <td>2002-08-29</td>
      <td>Rock</td>
      <td>Good news, punks! Thanks to your complete lack...</td>
      <td>4.6</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Meds</td>
      <td>Placebo</td>
      <td>0</td>
      <td>2006-05-04</td>
      <td>Rock</td>
      <td>Latest record from the oddly successful glam- ...</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Field Report</td>
      <td>Field Report</td>
      <td>0</td>
      <td>2012-09-13</td>
      <td>None</td>
      <td>Chris Porterfield, the onetime bandmate of Jus...</td>
      <td>6.6</td>
    </tr>
  </tbody>
</table>
</div>



Joining datasets togethers on the basis of their row values can be done with **.merge()** or **.join()**


```python
d = {'artist': ['Prince', 'Kanye West'], 'DOB': ['June 7, 1958', 'June 8, 1977']} # test data
df = pd.DataFrame(data=d)

# Inner join example
p4k.merge(df, how="inner", on = "artist")

# p4k.merge(df, how="inner", on = "artist",suffixes=[" - p4k", " - df"]) # Example with identifying suffixes added
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>DOB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>One Nite Alone, The Aftershow: It Ain't Over!</td>
      <td>Prince</td>
      <td>1</td>
      <td>2016-09-01</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue Originally released as part o...</td>
      <td>8.6</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sign "O" the Times</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-30</td>
      <td>Pop/R&amp;B</td>
      <td>Choosing a single high point from Prince's glo...</td>
      <td>10.0</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1999</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-30</td>
      <td>Pop/R&amp;B</td>
      <td>1999 is the greatest album ever made about par...</td>
      <td>10.0</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Dirty Mind</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-29</td>
      <td>Pop/R&amp;B</td>
      <td>Princes first fully actualized album is an un...</td>
      <td>10.0</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Controversy</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-29</td>
      <td>Pop/R&amp;B</td>
      <td>Controversy emerged in 1981 at a pivotal time ...</td>
      <td>9.0</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>5</th>
      <td>HITNRUN Phase Two</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-01-08</td>
      <td>Pop/R&amp;B</td>
      <td>The second of Prince's HITNRUN series is anoth...</td>
      <td>4.7</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>6</th>
      <td>HITNRUN Phase One</td>
      <td>Prince</td>
      <td>0</td>
      <td>2015-09-10</td>
      <td>Pop/R&amp;B</td>
      <td>Prince's new effort, exclusive to Jay Z's Tida...</td>
      <td>4.5</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Planet Earth</td>
      <td>Prince</td>
      <td>0</td>
      <td>2007-07-23</td>
      <td>Pop/R&amp;B</td>
      <td>So far this year Prince has wowed at the Super...</td>
      <td>4.8</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Ultimate Prince</td>
      <td>Prince</td>
      <td>0</td>
      <td>2006-09-05</td>
      <td>Pop/R&amp;B</td>
      <td>This Prince best of covers the Warner Brothers...</td>
      <td>8.6</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3121</td>
      <td>Prince</td>
      <td>0</td>
      <td>2006-03-20</td>
      <td>Pop/R&amp;B</td>
      <td>On his latest release, the rock legend betters...</td>
      <td>6.0</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Musicology</td>
      <td>Prince</td>
      <td>0</td>
      <td>2004-04-28</td>
      <td>Pop/R&amp;B</td>
      <td>One of the first things a rock critic learns i...</td>
      <td>5.8</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>11</th>
      <td>The Life of Pablo</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2016-02-15</td>
      <td>Rap</td>
      <td>Best new music Finally, after a protracted and...</td>
      <td>9.0</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Yeezus</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2013-06-18</td>
      <td>Rap</td>
      <td>Best new music Marking a blunt break with the ...</td>
      <td>9.5</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>13</th>
      <td>My Beautiful Dark Twisted Fantasy</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2010-11-22</td>
      <td>Rap</td>
      <td>Best new music Kanye's big year culminates in ...</td>
      <td>10.0</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>14</th>
      <td>VH1 Storytellers</td>
      <td>Kanye West</td>
      <td>0</td>
      <td>2010-01-12</td>
      <td>Rap</td>
      <td>This live set finds Kanye inserting rambling m...</td>
      <td>4.9</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>15</th>
      <td>808s and Heartbreak</td>
      <td>Kanye West</td>
      <td>0</td>
      <td>2008-12-02</td>
      <td>Rap</td>
      <td>Poor Kanye West. The guy was already a ball of...</td>
      <td>7.6</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Graduation</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2007-09-11</td>
      <td>Rap</td>
      <td>Best new music Though it doesn't quite match C...</td>
      <td>8.7</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Late Registration</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2005-08-28</td>
      <td>Rap</td>
      <td>Best new music The producer-rapper follows his...</td>
      <td>9.5</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>18</th>
      <td>The College Dropout</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2004-02-20</td>
      <td>Rap</td>
      <td>Best new music College Dropout Mini-Course Syl...</td>
      <td>8.2</td>
      <td>June 8, 1977</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Outer join example - returns all rows found in either source
p4k.merge(df, how="outer", on = "artist") # in this case, same as left join

# Right join example
p4k.merge(df, how="right", on = "artist") # in this case, same as inner

# Left join example
p4k.merge(df, how="left", on = "artist") # leaves plenty of NaN's in DOB column

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>DOB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19550</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 8 columns</p>
</div>




```python
# If columns do not have the same names, use 'left_on' , 'right_on' parameters:
df.rename(columns = {"artist" : "artist_Name", "DOB" : "DOB"}, inplace=True) #  renaming of columns are done with a dictionary
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>artist_Name</th>
      <th>DOB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
  </tbody>
</table>
</div>




```python
# make use of left_on and right_on arguments
p4k.merge(df, how="right", left_on = "artist", right_on = "artist_Name") # leaves plenty of NaN's in DOB column
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>artist_Name</th>
      <th>DOB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>One Nite Alone, The Aftershow: It Ain't Over!</td>
      <td>Prince</td>
      <td>1</td>
      <td>2016-09-01</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue Originally released as part o...</td>
      <td>8.6</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sign "O" the Times</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-30</td>
      <td>Pop/R&amp;B</td>
      <td>Choosing a single high point from Prince's glo...</td>
      <td>10.0</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1999</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-30</td>
      <td>Pop/R&amp;B</td>
      <td>1999 is the greatest album ever made about par...</td>
      <td>10.0</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Dirty Mind</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-29</td>
      <td>Pop/R&amp;B</td>
      <td>Princes first fully actualized album is an un...</td>
      <td>10.0</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Controversy</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-04-29</td>
      <td>Pop/R&amp;B</td>
      <td>Controversy emerged in 1981 at a pivotal time ...</td>
      <td>9.0</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>5</th>
      <td>HITNRUN Phase Two</td>
      <td>Prince</td>
      <td>0</td>
      <td>2016-01-08</td>
      <td>Pop/R&amp;B</td>
      <td>The second of Prince's HITNRUN series is anoth...</td>
      <td>4.7</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>6</th>
      <td>HITNRUN Phase One</td>
      <td>Prince</td>
      <td>0</td>
      <td>2015-09-10</td>
      <td>Pop/R&amp;B</td>
      <td>Prince's new effort, exclusive to Jay Z's Tida...</td>
      <td>4.5</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Planet Earth</td>
      <td>Prince</td>
      <td>0</td>
      <td>2007-07-23</td>
      <td>Pop/R&amp;B</td>
      <td>So far this year Prince has wowed at the Super...</td>
      <td>4.8</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Ultimate Prince</td>
      <td>Prince</td>
      <td>0</td>
      <td>2006-09-05</td>
      <td>Pop/R&amp;B</td>
      <td>This Prince best of covers the Warner Brothers...</td>
      <td>8.6</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3121</td>
      <td>Prince</td>
      <td>0</td>
      <td>2006-03-20</td>
      <td>Pop/R&amp;B</td>
      <td>On his latest release, the rock legend betters...</td>
      <td>6.0</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Musicology</td>
      <td>Prince</td>
      <td>0</td>
      <td>2004-04-28</td>
      <td>Pop/R&amp;B</td>
      <td>One of the first things a rock critic learns i...</td>
      <td>5.8</td>
      <td>Prince</td>
      <td>June 7, 1958</td>
    </tr>
    <tr>
      <th>11</th>
      <td>The Life of Pablo</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2016-02-15</td>
      <td>Rap</td>
      <td>Best new music Finally, after a protracted and...</td>
      <td>9.0</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Yeezus</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2013-06-18</td>
      <td>Rap</td>
      <td>Best new music Marking a blunt break with the ...</td>
      <td>9.5</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>13</th>
      <td>My Beautiful Dark Twisted Fantasy</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2010-11-22</td>
      <td>Rap</td>
      <td>Best new music Kanye's big year culminates in ...</td>
      <td>10.0</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>14</th>
      <td>VH1 Storytellers</td>
      <td>Kanye West</td>
      <td>0</td>
      <td>2010-01-12</td>
      <td>Rap</td>
      <td>This live set finds Kanye inserting rambling m...</td>
      <td>4.9</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>15</th>
      <td>808s and Heartbreak</td>
      <td>Kanye West</td>
      <td>0</td>
      <td>2008-12-02</td>
      <td>Rap</td>
      <td>Poor Kanye West. The guy was already a ball of...</td>
      <td>7.6</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Graduation</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2007-09-11</td>
      <td>Rap</td>
      <td>Best new music Though it doesn't quite match C...</td>
      <td>8.7</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Late Registration</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2005-08-28</td>
      <td>Rap</td>
      <td>Best new music The producer-rapper follows his...</td>
      <td>9.5</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>18</th>
      <td>The College Dropout</td>
      <td>Kanye West</td>
      <td>1</td>
      <td>2004-02-20</td>
      <td>Rap</td>
      <td>Best new music College Dropout Mini-Course Syl...</td>
      <td>8.2</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
  </tbody>
</table>
</div>




```python
# The .join() method requires a lot less code
df.rename(columns = {"artist" : "artist", "DOB" : "DOB"}, inplace=True) #  renaming of columns are done with a dictionary
p4k.join(df)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album</th>
      <th>artist</th>
      <th>best</th>
      <th>date</th>
      <th>genre</th>
      <th>review</th>
      <th>score</th>
      <th>artist_Name</th>
      <th>DOB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
      <td>Kanye West</td>
      <td>June 8, 1977</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>2017-12-06</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>2017-12-05</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>19551</th>
      <td>1999</td>
      <td>Cassius</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Let Us Replay!</td>
      <td>Coldcut</td>
      <td>0</td>
      <td>1999-01-26</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Singles Breaking Up, Vol. 1</td>
      <td>Don Caballero</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Out of Tune</td>
      <td>Mojave 3</td>
      <td>0</td>
      <td>1999-01-12</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Left for Dead in Malaysia</td>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>1999-01-05</td>
      <td>None</td>
      <td>Neil Hamburger's third comedy release is a des...</td>
      <td>6.5</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>19555 rows × 9 columns</p>
</div>



### 15. Reshaping dataframes
_key methods: .pivot(), .pivot_table(), .melt()_



```python
# .pivot() returns a reshaped dataframe as a pivot table. Uses unique values from specified index and columns.
# does not support aggregation

p4ksubset = p4k[p4k['artist'].isin(["Kanye West","Nas","Coldplay"])]
p4ksubset = p4ksubset[['artist','album','best','score']]
p4ksubset
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>artist</th>
      <th>album</th>
      <th>best</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>501</th>
      <td>Coldplay</td>
      <td>Kaleidoscope</td>
      <td>0</td>
      <td>5.8</td>
    </tr>
    <tr>
      <th>2315</th>
      <td>Kanye West</td>
      <td>The Life of Pablo</td>
      <td>1</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>2493</th>
      <td>Coldplay</td>
      <td>A Head Full of Dreams</td>
      <td>0</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>4279</th>
      <td>Coldplay</td>
      <td>Ghost Stories</td>
      <td>0</td>
      <td>4.4</td>
    </tr>
    <tr>
      <th>5369</th>
      <td>Kanye West</td>
      <td>Yeezus</td>
      <td>1</td>
      <td>9.5</td>
    </tr>
    <tr>
      <th>5884</th>
      <td>Nas</td>
      <td>Illmatic</td>
      <td>1</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>6458</th>
      <td>Nas</td>
      <td>Life Is Good</td>
      <td>1</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>7297</th>
      <td>Coldplay</td>
      <td>Mylo Xyloto</td>
      <td>0</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>8370</th>
      <td>Kanye West</td>
      <td>My Beautiful Dark Twisted Fantasy</td>
      <td>1</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>9436</th>
      <td>Kanye West</td>
      <td>VH1 Storytellers</td>
      <td>0</td>
      <td>4.9</td>
    </tr>
    <tr>
      <th>10120</th>
      <td>Coldplay</td>
      <td>LeftRightLeftRightLeft</td>
      <td>0</td>
      <td>6.8</td>
    </tr>
    <tr>
      <th>10670</th>
      <td>Kanye West</td>
      <td>808s and Heartbreak</td>
      <td>0</td>
      <td>7.6</td>
    </tr>
    <tr>
      <th>10685</th>
      <td>Coldplay</td>
      <td>Prospekts March EP</td>
      <td>0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>11140</th>
      <td>Nas</td>
      <td>Untitled</td>
      <td>0</td>
      <td>3.8</td>
    </tr>
    <tr>
      <th>11240</th>
      <td>Coldplay</td>
      <td>Viva la Vida or Death and All His Friends</td>
      <td>0</td>
      <td>6.9</td>
    </tr>
    <tr>
      <th>11939</th>
      <td>Nas</td>
      <td>Greatest Hits</td>
      <td>0</td>
      <td>6.4</td>
    </tr>
    <tr>
      <th>12158</th>
      <td>Kanye West</td>
      <td>Graduation</td>
      <td>1</td>
      <td>8.7</td>
    </tr>
    <tr>
      <th>13045</th>
      <td>Nas</td>
      <td>Hip Hop Is Dead</td>
      <td>0</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>14610</th>
      <td>Kanye West</td>
      <td>Late Registration</td>
      <td>1</td>
      <td>9.5</td>
    </tr>
    <tr>
      <th>14893</th>
      <td>Coldplay</td>
      <td>X&amp;Y</td>
      <td>0</td>
      <td>4.9</td>
    </tr>
    <tr>
      <th>15460</th>
      <td>Nas</td>
      <td>Street's Disciple</td>
      <td>0</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>16370</th>
      <td>Kanye West</td>
      <td>The College Dropout</td>
      <td>1</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>16535</th>
      <td>Coldplay</td>
      <td>Live 2003</td>
      <td>0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>17568</th>
      <td>Nas</td>
      <td>God's Son</td>
      <td>0</td>
      <td>8.6</td>
    </tr>
    <tr>
      <th>17765</th>
      <td>Nas</td>
      <td>The Lost Tapes</td>
      <td>0</td>
      <td>6.9</td>
    </tr>
    <tr>
      <th>17839</th>
      <td>Coldplay</td>
      <td>A Rush of Blood to the Head</td>
      <td>0</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>19027</th>
      <td>Coldplay</td>
      <td>Parachutes</td>
      <td>0</td>
      <td>5.3</td>
    </tr>
  </tbody>
</table>
</div>




```python
p4ksubset.pivot(index='album', columns='artist', values='best')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>artist</th>
      <th>Coldplay</th>
      <th>Kanye West</th>
      <th>Nas</th>
    </tr>
    <tr>
      <th>album</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>808s and Heartbreak</th>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>A Head Full of Dreams</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>A Rush of Blood to the Head</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Ghost Stories</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>God's Son</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Graduation</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Greatest Hits</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Hip Hop Is Dead</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Illmatic</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>Kaleidoscope</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Late Registration</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>LeftRightLeftRightLeft</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Life Is Good</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>Live 2003</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>My Beautiful Dark Twisted Fantasy</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Mylo Xyloto</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Parachutes</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Prospekts March EP</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Street's Disciple</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>The College Dropout</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>The Life of Pablo</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>The Lost Tapes</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Untitled</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>VH1 Storytellers</th>
      <td>NaN</td>
      <td>0.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Viva la Vida or Death and All His Friends</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>X&amp;Y</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Yeezus</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# .pivot_table() - parameters values, index, columns, and agg (aggregate function)
p4ksubset.pivot_table(values='score', index='artist', aggfunc='mean')

# other aggfunc arguments include count, sum, min, max
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>score</th>
    </tr>
    <tr>
      <th>artist</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Coldplay</th>
      <td>5.727273</td>
    </tr>
    <tr>
      <th>Kanye West</th>
      <td>8.425000</td>
    </tr>
    <tr>
      <th>Nas</th>
      <td>7.375000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# pivot table with multiple indexes
```


```python
p4ksubset.pivot_table(values='score', index=['artist','best'], aggfunc='mean')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>score</th>
    </tr>
    <tr>
      <th>artist</th>
      <th>best</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Coldplay</th>
      <th>0</th>
      <td>5.727273</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Kanye West</th>
      <th>0</th>
      <td>6.250000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9.150000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Nas</th>
      <th>0</th>
      <td>6.783333</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9.150000</td>
    </tr>
  </tbody>
</table>
</div>




```python
## .melt is essentially the inverse of pivot_table. Converting to a more tabular version

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>score</th>
    </tr>
    <tr>
      <th>artist</th>
      <th>best</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Coldplay</th>
      <th>0</th>
      <td>5.727273</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Kanye West</th>
      <th>0</th>
      <td>6.250000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9.150000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">Nas</th>
      <th>0</th>
      <td>6.783333</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9.150000</td>
    </tr>
  </tbody>
</table>
</div>
