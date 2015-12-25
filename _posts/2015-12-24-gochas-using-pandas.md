---
layout: post
title: Gotchas of using Pandas
categories: dataframe
description: "Pandas is a tool of Python for data processing. This post will give you some important points that may help you cross the pitfalls when you're learning the pandas."
comments: true
tags: [python, dataframe, ETL]
image:
feature:
credit:
creditlink:
date: 2015-12-24T00:00:00+00:00
author: superching
---
<head>
<style>
table, th, td {
   border: 1px solid black;
   padding: 3px;
}
</style>
</head>

# Gotchas of using Pandas
Pandas is a tool of Python for data processing, which provides a dataframe structure that you can handle complex structured data. When you have data on hand and you know how to use Python, Pandas is a must-have package that facilitates your *data wrangling*. In data science, we first wrangle the data, then torture data in the hope that they will confess. If you are new to Pandas, you probably would be confused at some point. Here are 4 gotchas that may help you.

### gotcha 1
Say you have a series of values [1,2], you wanna append it to new column of existing dataframe `df`.

```python
import pandas as pd

series=pd.Series([1,2])
df=pd.DataFrame(index=[3,4])
df['column1']=series
df
```
you would instead get `NaN` in your new column:

<table border="1">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column1</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>3</th>
      <td>NaN</td>
    </tr>
    <tr style="text-align: center;">
      <th>4</th>
      <td>NaN</td>
    </tr>
  </tbody>
</table>

<br>If you were Numpy user or Spark dataframe user, it may surprise you. Because **the operations in Pandas are index-aware**:  
![index-aware](/img/blog/wayne/pandas_index_aware.png)  
Operations, e.g. assign, add, compare, to name a few aware indices of operands. That occasionally would make you mad if what you want is Numpy style operation. The example is somewhat contrived since I make the index different first, but in reality it's common that after data being wrangled the index is disordered.

If you want to ignore index the right way to go is to turn it into a Numpy ndarray by `.values`:

```python
df['column1']=series.values
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column1</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>3</th>
      <td>1</td>
    </tr>
    <tr style="text-align: center;">
      <th>4</th>
      <td>2</td>
    </tr>
  </tbody>
</table><br>

Pandas called this index-aware behavior *Automatic Data Alignment*; I see it just an implicit auto index join. Pandas put much effort in index, there's even *multi-index* dataframe for advanced users. When you're coding pandas, you must bear this in minds — *pandas is index-aware*.

### gotcha 2
What would you do if you wanna detect missing values?

```python
df=pd.DataFrame({'column1':[1,np.nan]})
df==np.nan
```
you get:
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column1</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>0</th>
      <td>False</td>
    </tr>
    <tr style="text-align: center;">
      <th>1</th>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div><br>

If you come from SQL, you could have assumed that the result is NaN, because that's what SQL's NULL would do (return NULL). In fact, compare missing value to anything would result in a False. And the right way to compare NaN is `.isnull()` method:  

```python  
df=pd.DataFrame({'column1':[1,np.nan]})   
df.isnull()
```  
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column1</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>0</th>
      <td>False</td>
    </tr>
    <tr style="text-align: center;">
      <th>1</th>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div><br>

Another gotcha about missing value here. Consider you get a binary valued column in `df` and you convert it to boolean type to better convey the meaning of the values.

```python
df=pd.DataFrame({'column1':[1,0,np.nan]})  # 0,1 valued column with missing value
df=df.astype(bool) # convert to boolean
df
```
you get unexpected results:
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column1</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>0</th>
      <td>True</td>
    </tr>
    <tr style="text-align: center;">
      <th>1</th>
      <td>False</td>
    </tr>
    <tr style="text-align: center;">
      <th>2</th>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div><br>What unexpected is that the `NaN` is converted into `True`. **The information of missing value is lost during the conversion.**
The gotcha is — **only float type has NaN**. Other types will translate NaN to their type, the consequence is it's hard or impossible to tell is it a missing value. So the better way is **try as possible to use float type**.

### gotcha 3
This one is notorious. We know that Pandas dataframe is mutable structure. That leads us to think that we can drill and slicing down to a very fine-grained dataframe and update them. But in fact the **indexing operations in pandas often results in a copy**. There's no copy-on-write, or a way to force it being a view. By *view* I mean it in terms of Numpy. Where you subset an array, and you are able to update the original array from that subset array :

```python
a=np.array([1,2,3,4])
b=a[:2]           # numpy subset indexing
b[0]=1000         # update
assert a[0]==1000 # True
```

However in Pandas, things got more intricate:

```python
df = pd.DataFrame([[1,2],['1','2']],columns=['col1','col2'])
df[0:1]['col1']=100  # update some cells to 100
df
```
here you get the original values:
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>0</th>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr style="text-align: center;">
      <th>1</th>
      <td>1</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div><br>The result is not updated to `100`. The indexing happens with 2 method calls, or the *chained indexing*, that corresponding to two brackets `df[][]`. The crux is it's possible that either call would return a copy of the data. Thus **when updating, you are actually updating a copy of original dataframe**. In other words, the above second line of code for updating is equivalent to :

```python
df[0:1].copy()['col1']=100
```

Telling whether Pandas indexing is doing copy or view is not easy, and the rule is not explicitly defined in the Pandas document, but you can find useful information [here](http://stackoverflow.com/questions/23296282/what-rules-does-pandas-use-to-generate-a-view-vs-a-copy) at StackOverflow.

The right way to this case is using full indexing operator `.loc` to update values:

```python
df.loc[0:1,'col1']=100
```
<table border="1"  class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th>0</th>
      <td>100</td>
      <td>2</td>
    </tr>
    <tr style="text-align: center;">
      <th>1</th>
      <td>100</td>
      <td>2</td>
    </tr>
  </tbody>
</table><br>You have to get used to `iloc`, `loc` and `ix`. They are the more formal ways doing indexing in Pandas and also keep you from write-on-copy pitfalls.

### gotcha 4
Group operation in Pandas is powerful. For example, you can just treat the `groupby` result just as an iterator and use for-loop.

```python
df=pd.DataFrame({'animal':['dog','cat','dog','cat'],
               'weight':[1,2,1,3],
               'color':['white','black','black','black']})
gb=df.groupby('animal')

for k,v in gb['weight']:
    print('weight of all {}s = {}'.format(k,v.sum()))
```
you get:

> weight of all cats = 5
> weight of all dogs = 2

Moreover, you can apply a user-defined-groupby-function that return *anything*. If you return dataframe or series, Pandas will automatically combined them back into a big dataframe. For example, we summarize the basic statistics of every group using method `describe()`, then pandas will combine them back into a single big dataframe:

```python
gb.apply(lambda x: x[['color']].describe())
```
you get:
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>color</th>
    </tr>
    <tr>
      <th>animal</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr style="text-align: center;">
      <th rowspan="4" valign="top">cat</th>
      <th>count</th>
      <td>2</td>
    </tr>
    <tr style="text-align: center;">
      <th>unique</th>
      <td>1</td>
    </tr>
    <tr style="text-align: center;">
      <th>top</th>
      <td>black</td>
    </tr>
    <tr style="text-align: center;">
      <th>freq</th>
      <td>2</td>
    </tr>
    <tr style="text-align: center;">
      <th rowspan="4" valign="top">dog</th>
      <th>count</th>
      <td>2</td>
    </tr>
    <tr style="text-align: center;">
      <th>unique</th>
      <td>2</td>
    </tr>
    <tr style="text-align: center;">
      <th>top</th>
      <td>white</td>
    </tr>
    <tr style="text-align: center;">
      <th>freq</th>
      <td>1</td>
    </tr>
  </tbody>
</table>

---
There're more gotchas you will run into, I only touched a few that are most important in my opinion. I recommend this document [Caveats and Gotchas](http://pandas.pydata.org/pandas-docs/stable/gotchas.html), hoping it will help you along the road while learning Pandas.
