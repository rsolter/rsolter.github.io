---
title: pandas Overview
categories: [python, pandas, reference]
date: 2020-05-12
excerpt: "An overview of pandas key functions"
---


```python
## Importing csv for summary of functions
import pandas as pd
p4k = pd.read_csv("p4kreviews.csv",encoding='latin1',index_col=0)
```

## Course Summary


Summary notes from pandas course using Pitchfork review data downloaded from Kaggle

**Sections:**

    1. Series
    2. DataFrames
    3. Strings
    4. Multi-Index
    5. Group By
    6. Merging, Joining, Concatenating
    7. Dates and Times

**Some useful links:**

- [Official Pandas Documentation](https://pandas.pydata.org/)

- [Comparison with R/R libraries](https://pandas.pydata.org/pandas-docs/stable/getting_started/comparison/comparison_with_r.html?highlight=arrange)

- [On Method Chaining in pandas](https://towardsdatascience.com/the-unreasonable-effectiveness-of-method-chaining-in-pandas-15c2109e3c69)


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
