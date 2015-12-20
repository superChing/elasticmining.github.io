---
layout: post
title: Gotchas of using Pandas
categories: dataframe
description: "Pandas is a tool of Python for data processing, which provide a 'dataframe' structure that you can leverage to handle complex structured data. This post will give you some important points that may help you cross the pitfalls when you're learning the pandas. "
comments: true
tags: [python, dataframe, ETL, preprocessing]
image:
  feature:
  credit:
  creditlink:
date: 2015-12-20T00:00:00+00:00
author: weichinglin
---

# Gotchas of using Pandas
Pandas is a tool of Python for data processing, which provide a 'dataframe' structure that you can leverage to handle complex structured data. When you face the data and you know Python, Pandas is a must-have tool that facilitate your "data wrangling”. In data science, we first wrangle the data, then torture data in the hope that they will confess.
If you are new to Pandas, you probably would confuse at some point, let me list a few for you.

#### gotcha 1:
Say you have a series `series` of [1,2,3], you wanna append it to new column of existing dataframe `df`.

```python
series = pd.Series([1,2])
df = pd.DataFrame(index=[3,4])
df['a'] = series
df['a']
```
You would instead get `NaN` in your new column.
> 3   NaN  
> 4   NaN  
> Name: a, dtype: float64

If you were Numpy user or Spark dataframe user , it may surprise you -- **operations in Pandas are index-aware**, e.g. assign, add , compare, to name a few. That occasionally would make you mad if what you intend is Numpy style operation .   
The example is somewhat contrived since I make the index different first, but in reality it's common that after data being wrangled the index is disordered.
If you want to ignore index the right way to go is to turn it into a Numpy ndarray :

```python
df['a']=series.values
```

Pandas called this index-aware behavior **Automatic Data Alignment**; I see it just an implicit auto index join. Pandas put much effort into *index*, there's even *multi-index* dataframe for advanced users. When you're coding pandas, you must bear this in minds -- *pandas is index-aware*.

#### gotcha 2:
What would you do if you wanna detect missing values?

```python
series=pd.Series([1,np.nan])
series==np.nan
```
you get
> 0    False
> 1    False
> dtype: bool

If you comes from SQL, you could have assumed that the result is `NaN`, because that's what SQL's `NULL` would do. In fact, missing value comparison in Pandas, i.e. comparing anything to `NaN`, would result in a `False`.
The right way to compare NaN is `isnull()` method.

Another gotcha about missing value here, Consider you get a binary valued column, you convert it to boolean type  to better convey the meaning of values.

```python
df=pd.DataFrame([1,np.nan])
df=df.astype(bool)
df
```
you get
> 0    True  
> 1    True  
> dtype: bool   

What unexpected is that the `NaN` is converted into `True`. The information of missing value is lost during the conversion.  
The gotcha is -- **only float type has `NaN`**. Other types will translate `NaN` to their type, the consequence is it's hard or impossible to tell is it a missing value. So the better way is **try as possible to use float type**.

#### gotcha 3
This one is notorious. We know that Pandas dataframe is mutable structure. That impress us we can drill and slicing down to a very fine-grained dataframe and update them. But in fact the indexing or filtering operation in pandas *often* results in a copy. There's no copy-on-write, or a way to force it being a view. By *view* I mean it in terms of Numpy. Where you subset an array, and you are able to update the original array from that subset array:

```python
a= np.array([1,2,3,4])
b=a[:2]
b[0]=1000
assert a[0]==1000 #True  
```

However in Pandas, things got more intricate.

```python
df = pd.DataFrame([[1,2],['1','2']],columns=['a','b'])
df[0:1]['a']=100
df['a']
```
Here you get :
> 0    1  
> 1    2  
> Name: a, dtype: object  

The result is not updated to 100 which is not what the coder intend apparently. The explanation is: The indexing happens with 2 calls, or a *chained indexing*, corresponding to two brackets. The crux is it's possible that either call would return a copy of the data. Thus **when updating, you are actually updating a copy of original dataframe.** In other words, the above code is equivalent to :
```python
df[0:1].copy()['a']=100
```

Telling whether Pandas is doing copy or view is not easy, and the rule is not explicitly defined in the document, but you can find useful information [here](http://stackoverflow.com/questions/23296282/what-rules-does-pandas-use-to-generate-a-view-vs-a-copy) at StackOverflow.

The right way is full indexing
```python
df.loc[0:1,'a']=100
```
You have to get used to `iloc`, `loc` and `ix` which are the more formal ways doing indexing in Pandas and also keep you from write-on-copy pitfalls.

#### gotcha 4
Group operation in Pandas is powerful. For example, you can just treat the `groupby` result just as an `iterator` and use `for`-loop.

```python
df=pd.DataFrame({'animal':['dog','cat','dog','cat'],
                 'weight':[1,2,1,3],
                 'like':['apple','fish','apple','fish'],
                 'color':['white','black','black','black']})
gb=df.groupby('animal')
for k,v in gb['weight']:
    print('weight of all {}s = {}'.format(k,v.sum()))
```
you get:
> weight of all cats = 4  
> weight of all dogs = 2  

Moreover, you can apply a user-defined-groupby-function that return anything. If you returning `dataframe` or `series`, Pandas will automatically combined them back into a big `dataframe`.

```python
gb.apply(lambda x: x[['color','like']].describe())
```
you get:     
![df](/img/blog/wayne/dataframe_20151220.png)


---
There're more gotchas you can find, I only touched a few that is most important in my opinion. I recommend this document [Caveats and Gotchas](http://pandas.pydata.org/pandas-docs/stable/gotchas.html), hoping it will help you along the road learning Pandas.
