---
layout: post
title: Variable Importance Analysis in Python
categories: python
description: "Filtering information from noise is hard. In this article, we demonstrate how to determine variable importance by a simple method."
comments: true
tags: [python, machine learning, feature selection, variable importance]
image:
feature:
credit: 
creditlink: 
date: 2016-2-28T00:00:00+00:00
author: ryanchao
---

### **Variable Importance Analysis in Python**

When dealing with machine learning problems, sometimes one has to face a huge dataset with hundreds or thousands of features. Machine learning relies on these data to build models for prediction, more information the features contain, more easier to train a good model. However, these variables also contain noise, and most of them might be anonymous or formatted by some kind of hash process due to privacy issue or confidential reasons. So it's hard to figure out the physical meaning and explain the correlation between these variables. Even we could know all variables' meanings, it's still difficult to determine which are more essential than others. Variable importance evaluation helps us extract features which contributes the most to the model, and discard the redundant which are considered as noise.

In python's packages such as *scikit-learn*, some methods for feature selection are already available[^1][^2]. But in this article, I want to introduce a model-independent approach to rank the variable importance. The idea is simple: we expect a strong feature that its distributions over labels(or regression target) should be very different, however if all distributions are the same, this feature can be considered as containing no information but noise. Taking the binary classification for example, the figure below shows a group of normalized distributions of a variable, as you can see, there is a obvious gap between distributions of positive and negative samples, the yellow and purple areas quantify the differences w.r.t. overall distribution, we can define the importance by calculating these areas.
<div style="text-align:center" markdown="1">
![normalized distribution](/img/blog/ryan/distribution_diff.png)
</div>

In order to demonstrate this method, first we build a pseudo dataset for our ranking experiment. The function below implement the work. 

```python

def build_ps_df(feature, nsample = 10000):
	nsample = int(nsample / 2)
	num = len(feature)
	print "Total feature number:" + str(num)
	iShuffle = np.random.permutation(num)
	tnum = num / 2
	lnum = num - tnum
	print "\nRandomly pick " + str(tnum) + " features to assign distinguishable distributions, " \
		+ "\nand the expected features in top rankings are: "
	for i in range(tnum):
		print feature[iShuffle[i]],

	delta_mu = 1 + 1 * np.random.random(tnum)
	delta_mu.sort()
	delta_mu = delta_mu[::-1]

	delta_sigma = 0.5 + 0.5 * np.random.random(tnum)
	delta_sigma.sort()

	mu = np.zeros(tnum)
	sigma = np.ones(tnum)
	df = pd.DataFrame()
	for i in range(tnum):
		d = np.zeros(4)
		if np.random.random() >= 0.5:
			d[0] = mu[i]
			d[1] = mu[i] + delta_mu[i]
		else:
			d[0] = mu[i] + delta_mu[i]
			d[1] = mu[i]
		if np.random.random() >= 0.5:
			d[2] = sigma[i]
			d[3] = sigma[i] + delta_sigma[i]
		else:
			d[2] = sigma[i] + delta_sigma[i]
			d[3] = sigma[i]
		sp = pd.Series(np.random.randn(nsample) * d[2] + d[0])
		sn = pd.Series(np.random.randn(nsample) * d[3] + d[1])
		df[feature[iShuffle[i]]] = sp.append(sn)

	print "\nIn contrast, the following features are generated with same distributions"
	for i in range(lnum):
		print feature[iShuffle[tnum + i]],
		df[feature[iShuffle[tnum + i]]] = np.random.randn(2 * nsample) * 50 * np.random.random() + 5 * np.random.random()

	sp = pd.Series(np.ones(nsample))
	sn = pd.Series(np.zeros(nsample))
	df['label'] = sp.append(sn)
	return df
```

This function generates a dataset for binary classification, half of variables are created as noise, their distributions are almost the same to their labels, the other half are assigned to have little gaps in their distributions between labels. Just feed a feature list into this function, then we get the dataset. According to the output message below, we could expect the variables: v11, v24, v26, v9, ..., and v10 should be at the top positions of ranking list.

```xml
In [1]:features = ['v1','v2','v3','v4','v5','v6','v7','v8','v9','v10', \
	'v11','v12','v13','v14','v15','v16','v17','v18','v19','v20', \
	'v21','v22','v23','v24','v25','v26','v27','v28','v29','v30']
In [2]:train = build_ps_df(features)

Total feature number:30
Randomly pick 15 features to assign distinguishable distributions, 
and the expected features in top rankings are: 
v11 v24 v26 v9 v30 v3 v18 v27 v8 v4 v20 v1 v17 v13 v10 
In contrast, the following features are generated with same distributions
v2 v14 v5 v15 v22 v6 v19 v21 v25 v29 v16 v7 v12 v28 v23 
```

Next step we define the main function to calculate the distribution differences and generate the ranking.

```python
def ranking(train, features):
	target = train.label
	col_num = len(features)
	print "\n\nRescaling the dataset into [0, 1]"
	for i in range(col_num):
		s = train[features[i]]
		r = s.max()
		l = s.min()
		s = (s - l) / (r - l)
		s[s < 0] = 0
		train[features[i]] = s

	bin_num = 20
	step = 1.0 / bin_num
	print("Getting global distribution...")
	globalCount = []
	for i in range(col_num):
		t = train[features[i]].dropna()
		l = float(len(t))
		count = np.zeros(bin_num)
		acc = 0.
		for j in range(bin_num):
			count[j] = float(len(t[t < step * (j + 1)])) - acc
			acc = acc + count[j]
		count = count / l
		globalCount.append(count)


	print("Calculating the distribution differences...")
	numeric_distr_diff = np.zeros((col_num, 2))

	for i in range(2):
		df = train[target == i]
		diff = np.zeros(col_num)
		for j in range(col_num):
			t = df[features[j]].dropna()
			l = float(len(t))
			count = np.zeros(bin_num)
			acc = 0.
			for k in range(bin_num):
				count[k] = float(len(t[t < step * (k + 1)])) - acc
				acc = acc + count[k]
			count = count / l
			arr = np.asarray([globalCount[j], count])
			diff[j] = abs(arr[0, :] - arr[1, :]).sum()
		numeric_distr_diff[:, i] = diff

	numeric_idx_sort = numeric_distr_diff.sum(axis = 1).argsort()
	numeric_idx_sort = numeric_idx_sort[::-1]
	numeric_variance_sort = numeric_distr_diff[numeric_idx_sort][:]
	print "\nRankings of feature importance"
	for i in range(col_num):
		print features[numeric_idx_sort[i]],
	return numeric_idx_sort

```

As you can see below, the ranking results basically match our expectation though some positions are slightly different due to randomness, and with no surprise, all noise variables fall into the bottom of the ranking list.

```xml
In [3]:feature_idx_rank = ranking(train, features)

Rescaling the dataset into [0, 1]
Getting global distribution...
Calculating the distribution differences...

Rankings of feature importance
v11 v26 v24 v9 v30 v27 v3 v18 v8 v1 v4 v17 v20 v13 v10 
v21 v19 v25 v6 v23 v2 v7 v15 v28 v16 v5 v29 v22 v14 v12
```

As mentioned before, our purpose is to extract features which are most valuable to the model, in order to evaluate how well this method work, we utilize xgboost[^3] to train this pseudo dataset as a binary classification problem, with random training parameters and cross-validation process, we compare the log-loss values(lower is better) under different conditions of feature selection schemes. 
<div style="text-align:center" markdown="1">
![training result](/img/blog/ryan/exp_result.png)
</div>

The thick blue line highlights our performance benchmark, this line shows the training results as all raw variables feed into the model including all noise. the topN lines shows the results from which we only pick N features at top positions of ranking list. While N equals 10, the performance is little worse due to lack of information, and as N gets larger than 15, we get almost the same results as the benchmark, which also implies that the rest of variables are redundant. On the purple line, we pick 20 features from the bottom positions of ranking, the difference is obvious, since most of the variables are noise, it get the worse result.

Indeed, this importance analysis method shows that it can filter informative features from noise, however there is no guarantee it can improve the training results without feature engineering. In this brief demo, we assume all variables are numerical type, and have simple normal distribution. In fact, this method can also be applied on categorical variables with same logic, and should adapt to different distribution type. So if your principal component analysis[^4] failed again, you could give it a try.



#### **REFERENCE**

[^1]: [Feature importances with forests of trees](http://scikit-learn.org/stable/auto_examples/ensemble/plot_forest_importances.html#example-ensemble-plot-forest-importances-py)

[^2]: [Feature selection](http://scikit-learn.org/stable/modules/feature_selection.html)

[^3]: [XGBoost Documentation](http://xgboost.readthedocs.org/en/latest/index.html)

[^4]: [Principal Component Analysis](https://en.wikipedia.org/wiki/Principal_component_analysis)
