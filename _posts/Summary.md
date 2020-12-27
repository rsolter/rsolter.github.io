```python
## Importing csv for summary of functions
import pandas as pd
p4k = pd.read_csv("p4kreviews.csv",encoding='latin1',index_col=0)
```

## pandas Overview


Making the move from R to python, I feel out of place without my familiar tidyverse of packages for data manipulation and visualization. As such, I've been spending a lot of time learning [pandas](https://pandas.pydata.org/), the most popular data analysis and manipulation tool in python.

This post is meant to serve as an overview of pandas functionality as well as serve as a personal reference. To demonstrate pandas, I've chosen to use a Kaggle [dataset](https://www.kaggle.com/nolanbconaway/pitchfork-data) that compiles over 18k music reviews from the Pitchfork website.

A copy of this .ipynb can be found on [here](https://github.com/rsolter/Udemy-Courses/blob/master/Udemy%20-%20Data%20Analysis%20with%20Pandas%20and%20Python/Summary.ipynb) my git repository for the Udemy pandas course





### Sections

1. [Inspecting a dataframe](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
2. [Selecting Columns or Rows from dataframe](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
3. [Adding or Deleting Columns and Rows](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
4. [Filtering](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
5. [Ranking & Sorting](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
6. [Working with NAs](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
7. [Unique and Duplicate values](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
8. [Working with Indexes](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
9. [Renaming Labels and Columns](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
10. [Sampling](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
11. [Grouping](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
12. [Dates and Times](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
13. [Text Functions](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)
14. [Merging, Joining, and Concatenating](https://rsolter.github.io/python/pandas/reference/Pandas-Overview/)

**Some useful links:**

- [Official Pandas Documentation](https://pandas.pydata.org/)

- [Comparison with R/R libraries](https://pandas.pydata.org/pandas-docs/stable/getting_started/comparison/comparison_with_r.html?highlight=arrange)

- [On Method Chaining in pandas](https://towardsdatascience.com/the-unreasonable-effectiveness-of-method-chaining-in-pandas-15c2109e3c69)

### Inspecting a dataframe

Below is a preview of the dataset which includes each album's score on a 10 point scale, artist name, album name, genre, review date, and text of the review. The best column refers to whether or not the album was designated a 'best new music' label.

key methods: .head(), .describe(), .info(), .shape, .dtypes, .columns(), .value_counts()


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
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>December 5 2017</td>
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
     3   date    19555 non-null  object
     4   genre   19555 non-null  object
     5   review  19554 non-null  object
     6   score   19555 non-null  float64
    dtypes: float64(1), int64(1), object(5)
    memory usage: 1.2+ MB



```python
p4k.shape ## Returns a tuple with dimensions of a dataset
```




    (19555, 7)




```python
p4k.dtypes ## Returns the data types for each column
```




    album      object
    artist     object
    best        int64
    date       object
    genre      object
    review     object
    score     float64
    dtype: object




```python
p4k.columns ## Returns a list with the column names
```




    Index(['album', 'artist', 'best', 'date', 'genre', 'review', 'score'], dtype='object')



### Selecting columns or rows from a dataframe

**Selecting columns** by name is done by passing the column(s) quoted name into brackets.


```python
p4k['album']
```




    1                   A.M./Being There
    2                           No Shame
    3                   Material Control
    4              Weighing of the Heart
    5                        The Visitor
                        ...             
    19551                           1999
    19552                 Let Us Replay!
    19553    Singles Breaking Up, Vol. 1
    19554                    Out of Tune
    19555      Left for Dead in Malaysia
    Name: album, Length: 19555, dtype: object




```python
p4k[['album','artist']]
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



A range of columns can also be selected using the colon (:)


```python
p4k.loc[:,'artist':]
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
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Glassjaw</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>December 5 2017</td>
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
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>Well, it's been two weeks now, and I guess it'...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>19552</th>
      <td>Coldcut</td>
      <td>0</td>
      <td>January 26 1999</td>
      <td>Electronic</td>
      <td>The marketing guys of yer average modern megac...</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>19553</th>
      <td>Don Caballero</td>
      <td>0</td>
      <td>January 12 1999</td>
      <td>Experimental</td>
      <td>Well, kids, I just went back and re-read my re...</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>19554</th>
      <td>Mojave 3</td>
      <td>0</td>
      <td>January 12 1999</td>
      <td>Rock</td>
      <td>Out of Tune is a Steve Martin album. Yes, I'll...</td>
      <td>6.3</td>
    </tr>
    <tr>
      <th>19555</th>
      <td>Neil Hamburger</td>
      <td>0</td>
      <td>January 5 1999</td>
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
# Returning the first row (as a pandas series)
p4k.iloc[0]
```




    album                                      A.M./Being There
    artist                                                Wilco
    best                                                      1
    date                                        December 6 2017
    genre                                                  Rock
    review    Best new reissue 1 / 2 Albums Newly reissued a...
    score                                                     7
    Name: 1, dtype: object




```python
# Returning the first 10 rows (as a pandas dataframe)
p4k.iloc[0:9]
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
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1</td>
      <td>December 5 2017</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Everyday Is Christmas</td>
      <td>Sia</td>
      <td>0</td>
      <td>December 5 2017</td>
      <td>Pop/R&amp;B</td>
      <td>Sias shiny Christmas album feels inconsistent...</td>
      <td>5.8</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Zaytown Sorority Class of 2017</td>
      <td>Zaytoven</td>
      <td>0</td>
      <td>December 5 2017</td>
      <td>Rap</td>
      <td>The prolific Atlanta producer enlists 17 women...</td>
      <td>6.2</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Songs of Experience</td>
      <td>U2</td>
      <td>0</td>
      <td>December 4 2017</td>
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




    Rock            0.355817
    Electronic      0.205574
    None            0.118844
    Experimental    0.086883
    Rap             0.075735
    Pop/R&B         0.059166
    Metal           0.039939
    Folk/Country    0.035796
    Jazz            0.013142
    Global          0.009103
    Name: genre, dtype: float64



### Adding or Deleting Columns and Rows

Adding a new column with a universal value:


```python
p4k["Review Language"] = 'ENG'
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
      <td>December 6 2017</td>
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
      <td>December 6 2017</td>
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
      <td>December 6 2017</td>
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
      <td>December 6 2017</td>
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
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
      <td>ENG</td>
    </tr>
  </tbody>
</table>
</div>



Second method for adding column with loc


```python
p4k.insert(loc=0,column="Parent Company", value="Vox")
p4k.head()
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-99-9e9ac6a54cee> in <module>
    ----> 1 p4k.insert(loc=0,column="Parent Company", value="Vox")
          2 p4k.head()


    ~/anaconda3/lib/python3.7/site-packages/pandas/core/frame.py in insert(self, loc, column, value, allow_duplicates)
       3494         self._ensure_valid_index(value)
       3495         value = self._sanitize_column(column, value, broadcast=False)
    -> 3496         self._data.insert(loc, column, value, allow_duplicates=allow_duplicates)
       3497
       3498     def assign(self, **kwargs) -> "DataFrame":


    ~/anaconda3/lib/python3.7/site-packages/pandas/core/internals/managers.py in insert(self, loc, item, value, allow_duplicates)
       1171         if not allow_duplicates and item in self.items:
       1172             # Should this be a different kind of error??
    -> 1173             raise ValueError(f"cannot insert {item}, already exists")
       1174
       1175         if not isinstance(loc, int):


    ValueError: cannot insert Parent Company, already exists


Adding a row with **append()**


```python
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
      <td>December 6 2017</td>
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
      <td>December 6 2017</td>
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
      <td>December 6 2017</td>
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
      <td>December 6 2017</td>
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
      <th>19552</th>
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
      <th>19553</th>
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
      <th>19554</th>
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




```python
reviews = p4k['review']


```


```python

```


```python
ReviewWordCount = []

for i in range(1,len(reviews)):
    WordCount = len(reviews[i].split())
    #print(WordCount)
    ReviewWordCount.append(WordCount)
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-40-0ece44ccfecb> in <module>
          2
          3 for i in range(1,len(reviews)):
    ----> 4     WordCount = len(reviews[i].split())
          5     #print(WordCount)
          6     ReviewWordCount.append(WordCount)


    AttributeError: 'float' object has no attribute 'split'


### Filtering

Filtering can be achieved with boolean conditions as well as the following methods: .isin(), .between(), .where(), .query().

**Boolean filtering**


```python
p4k[p4k["artist"]=="Prince"]
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
      <th>1599</th>
      <td>One Nite Alone, The Aftershow: It Ain't Over!</td>
      <td>Prince</td>
      <td>1</td>
      <td>September 1 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue Originally released as part o...</td>
      <td>8.6</td>
    </tr>
    <tr>
      <th>2042</th>
      <td>Sign "O" the Times</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 30 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Choosing a single high point from Prince's glo...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>2043</th>
      <td>1999</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 30 2016</td>
      <td>Pop/R&amp;B</td>
      <td>1999 is the greatest album ever made about par...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>2047</th>
      <td>Dirty Mind</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 29 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Princes first fully actualized album is an un...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>2048</th>
      <td>Controversy</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 29 2016</td>
      <td>Pop/R&amp;B</td>
      <td>Controversy emerged in 1981 at a pivotal time ...</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>2444</th>
      <td>HITNRUN Phase Two</td>
      <td>Prince</td>
      <td>0</td>
      <td>January 8 2016</td>
      <td>Pop/R&amp;B</td>
      <td>The second of Prince's HITNRUN series is anoth...</td>
      <td>4.7</td>
    </tr>
    <tr>
      <th>2779</th>
      <td>HITNRUN Phase One</td>
      <td>Prince</td>
      <td>0</td>
      <td>September 10 2015</td>
      <td>Pop/R&amp;B</td>
      <td>Prince's new effort, exclusive to Jay Z's Tida...</td>
      <td>4.5</td>
    </tr>
    <tr>
      <th>12334</th>
      <td>Planet Earth</td>
      <td>Prince</td>
      <td>0</td>
      <td>July 23 2007</td>
      <td>Pop/R&amp;B</td>
      <td>So far this year Prince has wowed at the Super...</td>
      <td>4.8</td>
    </tr>
    <tr>
      <th>13400</th>
      <td>Ultimate Prince</td>
      <td>Prince</td>
      <td>0</td>
      <td>September 5 2006</td>
      <td>Pop/R&amp;B</td>
      <td>This Prince best of covers the Warner Brothers...</td>
      <td>8.6</td>
    </tr>
    <tr>
      <th>13946</th>
      <td>3121</td>
      <td>Prince</td>
      <td>0</td>
      <td>March 20 2006</td>
      <td>Pop/R&amp;B</td>
      <td>On his latest release, the rock legend betters...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>16164</th>
      <td>Musicology</td>
      <td>Prince</td>
      <td>0</td>
      <td>April 28 2004</td>
      <td>Pop/R&amp;B</td>
      <td>One of the first things a rock critic learns i...</td>
      <td>5.8</td>
    </tr>
  </tbody>
</table>
</div>




```python
p4k[p4k["genre"] == "Global"]
#p4k[p4k["genre"] != "Metal"]
```


      File "<ipython-input-56-1d41fa158feb>", line 1
        p4k[p4k["genre"] !== "Global"]
                           ^
    SyntaxError: invalid syntax




```python
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
      <th>14</th>
      <td>Master of Puppets</td>
      <td>Metallica</td>
      <td>1</td>
      <td>December 2 2017</td>
      <td>Metal</td>
      <td>Best new reissue In 1986, Metallica released i...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>128</th>
      <td>I Can Hear the Heart Beating as One</td>
      <td>Yo La Tengo</td>
      <td>0</td>
      <td>October 29 2017</td>
      <td>Rock</td>
      <td>Twenty years on from its original release, Yo ...</td>
      <td>9.7</td>
    </tr>
    <tr>
      <th>154</th>
      <td>The Queen Is Dead</td>
      <td>The Smiths</td>
      <td>0</td>
      <td>October 22 2017</td>
      <td>Rock</td>
      <td>Newly reissued as a boxed set, the Smiths 198...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>506</th>
      <td>Appetite for Destruction</td>
      <td>Guns N' Roses</td>
      <td>0</td>
      <td>July 16 2017</td>
      <td>Rock</td>
      <td>The debut from Guns N' Roses was a watershed m...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>566</th>
      <td>Purple Rain Deluxe  Expanded Edition</td>
      <td>Prince / The Revolution</td>
      <td>1</td>
      <td>June 26 2017</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue In 1984, Purple Rain turned P...</td>
      <td>10.0</td>
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
      <th>19064</th>
      <td>Kid A</td>
      <td>Radiohead</td>
      <td>0</td>
      <td>October 2 2000</td>
      <td>Rock</td>
      <td>I had never even seen a shooting star before. ...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>19172</th>
      <td>The Moon &amp; Antarctica</td>
      <td>Modest Mouse</td>
      <td>0</td>
      <td>June 13 2000</td>
      <td>Rock</td>
      <td>It's not very exciting behind the scenes at Pi...</td>
      <td>9.8</td>
    </tr>
    <tr>
      <th>19236</th>
      <td>Animals</td>
      <td>Pink Floyd</td>
      <td>0</td>
      <td>April 25 2000</td>
      <td>Rock</td>
      <td>It begins somewhere for everyone. There's the ...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>19383</th>
      <td>Emergency &amp; I</td>
      <td>The Dismemberment Plan</td>
      <td>0</td>
      <td>September 30 1999</td>
      <td>Rock</td>
      <td>The Short Review:\r\nIf you consider yourself ...</td>
      <td>9.6</td>
    </tr>
    <tr>
      <th>19384</th>
      <td>I See a Darkness</td>
      <td>Bonnie Prince Billy</td>
      <td>0</td>
      <td>September 30 1999</td>
      <td>Folk/Country</td>
      <td>Music is a wounded, corrupted, vile, halfbreed...</td>
      <td>10.0</td>
    </tr>
  </tbody>
</table>
<p>127 rows × 7 columns</p>
</div>




```python
condition1 = p4k["score"]>9.3
condition2 = p4k["genre"] == "Global"

p4k[condition1 & condition2] # filtering on both conditions
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
      <th>1568</th>
      <td>Caetano Veloso</td>
      <td>Caetano Veloso</td>
      <td>0</td>
      <td>September 11 2016</td>
      <td>Global</td>
      <td>In 1968, the Brazilian pop singer began a Trop...</td>
      <td>9.4</td>
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
      <th>14</th>
      <td>Master of Puppets</td>
      <td>Metallica</td>
      <td>1</td>
      <td>December 2 2017</td>
      <td>Metal</td>
      <td>Best new reissue In 1986, Metallica released i...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>119</th>
      <td>Walk Among Us</td>
      <td>Misfits</td>
      <td>0</td>
      <td>October 31 2017</td>
      <td>Metal</td>
      <td>They were outliers when they started, but by t...</td>
      <td>9.4</td>
    </tr>
    <tr>
      <th>128</th>
      <td>I Can Hear the Heart Beating as One</td>
      <td>Yo La Tengo</td>
      <td>0</td>
      <td>October 29 2017</td>
      <td>Rock</td>
      <td>Twenty years on from its original release, Yo ...</td>
      <td>9.7</td>
    </tr>
    <tr>
      <th>154</th>
      <td>The Queen Is Dead</td>
      <td>The Smiths</td>
      <td>0</td>
      <td>October 22 2017</td>
      <td>Rock</td>
      <td>Newly reissued as a boxed set, the Smiths 198...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>218</th>
      <td>Ash</td>
      <td>Ibeyi</td>
      <td>1</td>
      <td>October 4 2017</td>
      <td>Global</td>
      <td>Best new music On their second album, the Fren...</td>
      <td>8.3</td>
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
      <th>19473</th>
      <td>Agaetis Byrjun</td>
      <td>Sigur Rós</td>
      <td>0</td>
      <td>June 1 1999</td>
      <td>Rock</td>
      <td>Icelandic lore tells of the Hidden People who ...</td>
      <td>9.4</td>
    </tr>
    <tr>
      <th>19475</th>
      <td>Livro</td>
      <td>Caetano Veloso</td>
      <td>0</td>
      <td>June 1 1999</td>
      <td>Global</td>
      <td>I heard somewhere that a person's tastes chang...</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>19490</th>
      <td>Mule Variations</td>
      <td>Tom Waits</td>
      <td>0</td>
      <td>April 27 1999</td>
      <td>Rock</td>
      <td>I once took a poetry workshop taught by a guy ...</td>
      <td>9.5</td>
    </tr>
    <tr>
      <th>19520</th>
      <td>Brand New Secondhand</td>
      <td>Roots Manuva</td>
      <td>0</td>
      <td>March 23 1999</td>
      <td>Electronic</td>
      <td>For politcially unaware, socially unconscious,...</td>
      <td>9.5</td>
    </tr>
    <tr>
      <th>19535</th>
      <td>Summerteeth</td>
      <td>Wilco</td>
      <td>0</td>
      <td>February 28 1999</td>
      <td>Rock</td>
      <td>After parting ways with Uncle Tupelo partner J...</td>
      <td>9.4</td>
    </tr>
  </tbody>
</table>
<p>403 rows × 7 columns</p>
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
      <th>28</th>
      <td>4444</td>
      <td>Sam Gendel</td>
      <td>0</td>
      <td>November 29 2017</td>
      <td>Jazz</td>
      <td>Sam Gendels smoothly psychedelic debut is dom...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Tauhid/Jewels of Thought/Deaf Dumb Blind (Summ...</td>
      <td>Pharoah Sanders</td>
      <td>1</td>
      <td>November 10 2017</td>
      <td>Jazz</td>
      <td>Best new reissue Best new reissue 1 / 3 Albums...</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>157</th>
      <td>The Centennial Trilogy</td>
      <td>Christian Scott aTunde Adjuah</td>
      <td>0</td>
      <td>October 21 2017</td>
      <td>Jazz</td>
      <td>1 / 3 Albums On the three albums that compose ...</td>
      <td>7.6</td>
    </tr>
    <tr>
      <th>178</th>
      <td>The Magic City / My Brother the Wind Vol. 1</td>
      <td>Sun Ra and His Arkestra</td>
      <td>0</td>
      <td>October 16 2017</td>
      <td>Jazz</td>
      <td>1 / 2 Albums Sun Ra manifested an ecstatic vis...</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>208</th>
      <td>Dreams and Daggers</td>
      <td>Cécile McLorin Salvant</td>
      <td>0</td>
      <td>October 7 2017</td>
      <td>Jazz</td>
      <td>The young jazz singers live double album show...</td>
      <td>7.6</td>
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
      <th>19261</th>
      <td>Permutation</td>
      <td>Bill Laswell</td>
      <td>0</td>
      <td>March 31 2000</td>
      <td>Global</td>
      <td>If there's one thing Bill Laswell knows, it's ...</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>19276</th>
      <td>Expensive Shit/He Miss Road</td>
      <td>Fela Kuti</td>
      <td>0</td>
      <td>March 21 2000</td>
      <td>Global</td>
      <td>Afro-beat pioneer Fela Kuti was never more pis...</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>19280</th>
      <td>Treader</td>
      <td>Spring Heel Jack</td>
      <td>0</td>
      <td>March 21 2000</td>
      <td>Jazz</td>
      <td>Isn't it kind of alarming that the once cuttin...</td>
      <td>5.4</td>
    </tr>
    <tr>
      <th>19467</th>
      <td>Interstellar Space Revisited: The Music of Joh...</td>
      <td>Gregg Bendian / Nels Cline</td>
      <td>0</td>
      <td>June 8 1999</td>
      <td>Jazz</td>
      <td>A friend of mine once remarked about the later...</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>19475</th>
      <td>Livro</td>
      <td>Caetano Veloso</td>
      <td>0</td>
      <td>June 1 1999</td>
      <td>Global</td>
      <td>I heard somewhere that a person's tastes chang...</td>
      <td>9.0</td>
    </tr>
  </tbody>
</table>
<p>435 rows × 7 columns</p>
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
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1200</th>
      <td>December 99th</td>
      <td>Yasiin Bey</td>
      <td>0</td>
      <td>January 2 2017</td>
      <td>Rap</td>
      <td>On December 99th, Yasiin Bey (fka Mos Def) sou...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1348</th>
      <td>Collage</td>
      <td>The Chainsmokers</td>
      <td>0</td>
      <td>November 9 2016</td>
      <td>Electronic</td>
      <td>The massive lite-EDM duo the Chainsmokers port...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1775</th>
      <td>New Introductory Lectures on the System of Tra...</td>
      <td>Kel Valhaal</td>
      <td>0</td>
      <td>July 15 2016</td>
      <td>Experimental</td>
      <td>Liturgy frontman Hunter Hunt-Hendrix's latest ...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1808</th>
      <td>Primary Colours</td>
      <td>Magic!</td>
      <td>0</td>
      <td>July 6 2016</td>
      <td>Pop/R&amp;B</td>
      <td>After scoring a #1 hit with 2014's "Rude,"  th...</td>
      <td>3.5</td>
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
      <th>18797</th>
      <td>Bleed American</td>
      <td>Jimmy Eat World</td>
      <td>0</td>
      <td>August 21 2001</td>
      <td>Rock</td>
      <td>Are you a 15-year-old TRL addict looking for a...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>19072</th>
      <td>Dream Signals in Full Circles</td>
      <td>Tristeza</td>
      <td>0</td>
      <td>September 26 2000</td>
      <td>Rock</td>
      <td>In the beginning, there was Nothing. And it wa...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>19336</th>
      <td>The Past Was Faster</td>
      <td>Kelley Stoltz</td>
      <td>0</td>
      <td>December 14 1999</td>
      <td>Rock</td>
      <td>There's an upside and a downside to the perpet...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>19394</th>
      <td>Cobra and Phases Group Play Voltage in the Mil...</td>
      <td>Stereolab</td>
      <td>0</td>
      <td>September 21 1999</td>
      <td>Experimental</td>
      <td>"Okay, Brent, this is getting really old." "Wh...</td>
      <td>3.4</td>
    </tr>
    <tr>
      <th>19454</th>
      <td>The Seduction of Claude Debussy</td>
      <td>Art of Noise</td>
      <td>0</td>
      <td>June 29 1999</td>
      <td>Electronic</td>
      <td>Tonite during Prime Time: Channel 02: "The Art...</td>
      <td>3.6</td>
    </tr>
  </tbody>
</table>
<p>91 rows × 7 columns</p>
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
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0.0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
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
    </tr>
  </tbody>
</table>
<p>19555 rows × 7 columns</p>
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
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Perfect Angel</td>
      <td>Minnie Riperton</td>
      <td>1</td>
      <td>December 5 2017</td>
      <td>Pop/R&amp;B</td>
      <td>Best new reissue A deluxe reissue of Minnie Ri...</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Master of Puppets</td>
      <td>Metallica</td>
      <td>1</td>
      <td>December 2 2017</td>
      <td>Metal</td>
      <td>Best new reissue In 1986, Metallica released i...</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Kick</td>
      <td>INXS</td>
      <td>1</td>
      <td>December 1 2017</td>
      <td>Rock</td>
      <td>Best new reissue The 30th-anniversary edition ...</td>
      <td>8.4</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Utopia</td>
      <td>Björk</td>
      <td>1</td>
      <td>November 27 2017</td>
      <td>Electronic</td>
      <td>Best new music Filled with flute and birdsong,...</td>
      <td>8.4</td>
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
      <th>17501</th>
      <td>Do You Party?</td>
      <td>The Soft Pink Truth</td>
      <td>1</td>
      <td>February 4 2003</td>
      <td>Electronic</td>
      <td>Best new music There will be big fun in town t...</td>
      <td>8.4</td>
    </tr>
    <tr>
      <th>17511</th>
      <td>You Forgot It in People</td>
      <td>Broken Social Scene</td>
      <td>1</td>
      <td>February 2 2003</td>
      <td>Rock</td>
      <td>Best new music It's a bit late to be talking a...</td>
      <td>9.2</td>
    </tr>
    <tr>
      <th>17551</th>
      <td>Everything Is Good Here/Please Come Home</td>
      <td>The Angels of Light</td>
      <td>1</td>
      <td>January 20 2003</td>
      <td>None</td>
      <td>Best new music To a certain extent, most of us...</td>
      <td>8.6</td>
    </tr>
    <tr>
      <th>17554</th>
      <td>Mount Eerie</td>
      <td>The Microphones</td>
      <td>1</td>
      <td>January 20 2003</td>
      <td>Experimental</td>
      <td>Best new music Growing up in the shadow of Mt....</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>17562</th>
      <td>S.T.R.E.E.T. D.A.D.</td>
      <td>Out Hud</td>
      <td>1</td>
      <td>January 15 2003</td>
      <td>Electronic</td>
      <td>Best new music Language is for suckers, but du...</td>
      <td>9.0</td>
    </tr>
  </tbody>
</table>
<p>1040 rows × 7 columns</p>
</div>



### Ranking & Sorting


### Working with NAs


### Unique and Duplicate values


### Working with Indexes


### Renaming Labels and Columns


### Sampling


### Grouping


### Data and Times


### Text Functions


### Merging, Joining, and Concatenating


### 1.Series

_A note on attributes and methods:_
An attribute is something that bound to an object, while a method is a procedure or action. Also, attributes have no parantheses, attributes require them

#### Series attributes and methods, explanations where necessary:

   - series.head
   - series.tail
   - len(series) - _Return length of series including NA/null observations_
   - sorted(series) - _Sorts values_
   - list(series) - _Converts series to a list_
   - dict(series) - _Turns the series into a dictionary object where the the existing index becomes the dictionary key_
   - min(series) -_For strings, will return first value sorted alphabetically_
   - max(series) - _For strings, will return last value sorted alphabetically _
   - series.values - _values attribute_
   - series.index - _values attribute_
   - series.dtype - _data type_
   - series.is_unique - _Returns unique values_
   - series.shape - _dimensions of series/dataframe_
   - series.size - _number of elements (rows*columns)_
   - series.count() - _Returns number of non-NA/null observations_
   - series.name - _name of the series_
   - series.sort_values(inplace=T) - _sorts values, inplace=T replaces original values with sorted ones_
   - series.sort_index(inplace=T) - _sorts index, inplace=T replaces original values with sorted ones_
   - "Value" in series
   - series['n'] - _returns nth element by index_
   - series['index label'] - _returns element by index value name_
   - series.sum()
   - series.mean()
   - series.std()
   - series.min()
   - series.max()
   - series.median()
   - series.mode()
   - series.describle() - _Similar to summary() in R, returns key descriptive stats_
   - series.idmax() - _Return the row label of the maximum value._
   - series.idmin() - _Return the row label of the minimum value._
   - series.value_counts() - _Similar to table() in base R_

#### Apply method - invokes a function on a series of values

   - [Documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.apply.html)
   - series.apply(FUNCTION, args(,.. additional arguments)


#### lambda
   - [Explanation](https://stackabuse.com/lambda-functions-in-python/)
   - In Python, the lambda keyword declares an anonymous (no name) function, which are referred to as "lambda functions". Although syntactically they look different, lambda functions behave in the same way as regular functions that are declared using the def keyword.


#### .map()
    - .map()


```python
range(12)
```




    range(0, 12)




```python
artists = p4k["artist"]
scores = p4k["score"]
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>A.M./Being There</td>
      <td>Wilco</td>
      <td>1</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>Best new reissue 1 / 2 Albums Newly reissued a...</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>No Shame</td>
      <td>Hopsin</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rap</td>
      <td>On his corrosive fifth album, the rapper takes...</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Material Control</td>
      <td>Glassjaw</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Rock</td>
      <td>On their first album in 15 years, the Long Isl...</td>
      <td>6.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Weighing of the Heart</td>
      <td>Nabihah Iqbal</td>
      <td>0</td>
      <td>December 6 2017</td>
      <td>Pop/R&amp;B</td>
      <td>On her debut LP, British producer Nabihah Iqba...</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Visitor</td>
      <td>Neil Young / Promise of the Real</td>
      <td>0</td>
      <td>December 5 2017</td>
      <td>Rock</td>
      <td>While still pointedly political, Neil Youngs ...</td>
      <td>6.7</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Methods and Attributes

artists.count()
artists.value_counts()
artists.head
artists.tail
len(artists)
sorted(artists)  
list(artists)
dict(artists)
min(artists)  
max(artists)
artists.values
artists.index
artists.dtype
artists.is_unique
artists.shape
artists.size
artists.name
artists.sort_values()  
artists.sort_index()
"David Bowie" in artists # in operator
artists[100]
#artists['David Bowie']
scores.sum()
scores.mean()
scores.std()
scores.min()
scores.max()
scores.median()
scores.mode()
scores.describe()
scores.idxmax("Score")
scores.idxmin("Score")
```




    12223




```python
## Apply Method - invokes a function on a series of values

# returns nth character of each artist name, with index starting at 0
def n_char(string,n):
    if len(string)<n+1:
        return ''
    else:
        return(string[n])


## Returning character from artist string at positiong 3:
artists.apply(n_char, args=(3,))


```




    1        c
    2        s
    3        s
    4        i
    5        l
            ..
    19551    s
    19552    d
    19553     
    19554    a
    19555    l
    Name: artist, Length: 19555, dtype: object




```python
## Lambda -
artists.apply(lambda x: x[0])
```




    1        W
    2        H
    3        G
    4        N
    5        N
            ..
    19551    C
    19552    C
    19553    D
    19554    M
    19555    N
    Name: artist, Length: 19555, dtype: object



### 2. Data Frames

#### Basic Information
   - df.shape
   - df.dtypes
   - df.columns
   - df.axes
   - df.info
   - df.sum( , axes={1,0})

#### Selecting column(s)

   - df["c1"] or df.c1, df[["c1","c2"]]

#### Adding a new column

   - df["newCol"] = {value}

#### Broadcasting Operations
   - df[value].add(5) or df[value] + 5 (accounts for NAs)
   - df[value].mul(3)

#### Dropping Rows with Null Values  

   - df.dropna() - _drops any observations with an NA values. Similar to R's complete.observations_
   - df.dropna(how="all") - _only drops rows with all NA values_
   - df.dropna(axis=1) - _drops columns with any NA values_

   - df.fillna(value=0) - _fills all values in the dataframe_
   - df["column1"].fillna(0,inplace=True) - _column by column approach_


#### Converting Types using as.type() method
   - df["Float_Score"].astype("int") - _converts FLOAT to INT. Note that there is not inplace arg_
   - as.type("category") - _can be used to convert a string to a R factor-like variable. Saves space._  

#### Sorting/Ranking Values
   - df.sort_values([Co1],[Col2], ascending=[True,False])
   - df.rank() - _provides rankings as integers_

#### Filtering based upon a condition
   - df["Col1"]=="Value" or df["Col1"]<=22 will return a boolean
   - df[df["Col1"]=="Value"] will return a filtered dataset
   - Alternatively, filter1 = df["Col1"]=="Value", df[filter1]
   - Conditions can be strung together with AND (&), OR (|)

#### .isin() Method

   - df["Col1].isin(["Value1","Value2"]) can be used to filter/extract rows in a dataframe

#### .isnull(), .notnull() Methods
   - df["Col1"].isnull() - _produces a boolean series where Col1 value is null_
   - df["Col1"].notnull() - _produces a boolean series where Col1 value is NOT null_

#### .between() Method
   - df["Col1"].between(200,300) - _returns a boolean series of observations falling between 200 and 300, inclusive. Works on times, dates, and numerics_

#### .duplicated() Method
   - df["Col1"].duplicate(keep="first") - _Idenifies duplicates and removes them, by default keeps the first observation. keep=False will return all observations that have duplicates_

#### .drop_duplicates() Method
   - df.drop_duplicate() - _Applies to a df across all columns, where as the .duplicated() method above applies to a series._
   - df.drop_duplicates(subset=["Col1"], keep = "first") - Can be applied to specific columns_

#### .unique() Method
   - df["Col1"].unique() - counts unique values for one column

#### .nunique() Method
   - df.nunique() - counts unique values across columns

#### .set_index() Method
   - df.set_index("Col1") - _replaces existing index with values from a column_

#### .reset_index() Method
   - df.reset_index(drop=True) - _resets index and drops values_
   - df.reset_index(drop=True) - _getting back to original_

#### Retrieving Rows by Index Label with .loc()
   - .loc uses brackets, parantheses
   - df.loc["indexLabel"] - Retrieves row with specific index label

#### Retrieving Rows by Index Position with .loc()
   - df.iloc[100] - _retrieves row with specific index number(s)_
   - df.iloc[60:120] - _retrieving a range_
   - df.iloc[12,1:3] - _retrieving a certain row, multiple columns_

#### Identifying Individual cells, setting new values
   - df.iloc[0, 1] == "New Value"
   - df.iloc[2, 0:] == "New row value"

#### Renaming Index Labels or Columns in a Dataframe
   - df.rename(columns = {"Col1" : "NewCol1", "Col2" : "NewCol2"}, inplace=T) - _renaming of columns are done with a dictionary


#### Deleting Rows or Columns from a Dataframe
   - dr.drop["Row1", axis=0] - _drops a row by name_
   - df.drop("Col1", axis=1) - _drop a column_
   - del df["Col1"] - _alternative method_

#### Random Samples with .sample() Method
   - df.sample(n) - _sample n random rows_
   - df.sample(frac=0.25) - _samples a random 25%_
   - df.sample(axis=) - _can sample rows or cols with axis_

#### The .nsmallest() and .nlargest() Methods
   - df.nsmallest(n=3, columns="Col1 ) - _returns 3 smallest values for Col1_
   - df.nlargest(n=3, columns="Col1 ) - _returns 3 largest values for Col1_


#### Filtering with the .where() Method()
   - df.where(df[Col1]=="Value") - _returns the original data frame with NAS in rows that don't meet the filtering criteria_

#### The .query() Method
   - df.query('Col'=="Value") - _Similar to filter, only returns matching rows_


#### .copy() Method
   - df.copy() - _create a copy of the object's indices and data_



### 3. Strings

#### Common methods   
   - string.lower()
   - string.upper()
   - string.title() - _Capitalizes first letter of each word_
   - len(string)
   - string.strip() - _Strips white space_
   - string.lstrip() - _Strips white space on the left_
   - string.rstrip() - _Strips white space on the right_


#### .str.replace() method
   - "Hello world".replace("l","!") - _Two arguments: pattern, substitute_


#### Filtering with string methods   
   - df["Col1"].str.lower().str.contains("water") - _Searches Col1 for strings that contain 'water'_
   - Other alternate searches: str.startswith(), str.endswith()

#### Splitting strings by characters
   - "Hello my name is Ravi".split(" ") # single arg is the delimiter/sep
   - **Expand parameter**: df[["First Name", "Last Name"]] = df["Name"].str.split(",", expand=True) - _Breaks apart Name into first and last name columns_
   - **n parameter** - _n equals the maximum number of splits_



### 4. Multi Index

#### Creating a multi-index with set_index()
   - df.set_index(keys=["Col1","Col2"], inplace=True) - _Creates multi-level index_

#### The .get_level_values() Method
   - df.index.get_level_values() - _returns index values_

#### The .set_names() Method
   - df.index_set_names(["Name1","Name2"]) - _renames index levels_

#### The sort_index() Method
   - df.sort_index(ascending=[True,False]) - _sorts indexes_

#### The .transpose() method and MultiIndex on Column Level
   - dfT = df.transpose() - _transposes data.frame, including indexes_

#### The .swaplevel() Method
   - df.swaplevel() - _swamps levels of multi-index_

#### The .stack() Method
   - Similar to R's tidy::gather()
   - [Official documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.stack.html)

#### The .unstack() Method
   - Similar to R's tidyr::spread()
   -

#### The .pivot() Method
   - df.pivot(index="Col1", columns"Col2", values="Col3") - _Returns reshaped DataFrame organized by given index/column names_
   - [Official documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.pivot.html)

#### The .pivot_table() Method
   - df.pivot_table(index="Col1", columns"Col2", values="Col3", aggfunc="mean") - _Create a spreadsheet-style pivot table as a DataFrame_
   - [Official documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.pivot_table.html)

#### The pd.melt() Method
   - Essentially the inverse of pivot_table. Converting into a longer table
   - [Official documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.melt.html)


### 5. Group by

#### The pd.melt() Method
   -
   -

### 6. Merging, Joining, Concatenating
   -
   -
   -
   -
   -

### 7. Merging, Joining, Concatenating  
   -
   -
   -
   -
   -


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```
