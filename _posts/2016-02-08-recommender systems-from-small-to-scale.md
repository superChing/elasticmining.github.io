---
layout: post
title: "Recommender Systems: From Small to Scale"
categories: data_science
description: "How do you choose your recommender system? Most solutions you will get is for giants like Amazon, Netflix or Spotify. We want to break it down, from small to scale."
comments: true
tags: ["data science", "machine learning", "production"]
image:
  feature:
  credit: 
  creditlink: 
date: 2016-02-08T00:00:00+00:00
author: chuyuhsu
---

# Recommender Systems: From Small to Scale

> “The Age of Search has come to an end. Long live the recommendation!”
> 
> Jeffrey M. O’Brien, CNN Money



Since our clients constantly need a recommender system for E-commerce, music, news, food etc. We want to make a brief introduction to recommender systems from small to large scale.

For a recommender system, its job is to filter those information users do not need to know. Yes, it sounds a little bit like what search engines do, in the opposite way. Exactly. It is known as an information filtering system, whiles search engines are usually known as information retrieval systems. [^1]

So how do you choose your recommender system? Just google it with something like "free recommendation engine", then use whatever first popped? Yeah, that will work. However, most solutions you will get is for giants like Amazon, Netflix or Spotify. I want to break it down, from small to scale.



## What is Your Scale?

I mean, what is the scale of users and items you want to recommend? Different scale of users and items can make a totally different choice of your recommendation system. How much is your daily active logged-in user? How does the number of recommended items increase? The most crucial part is how much money you can burn. 

Yes, recommender system can be very expensive. Many recommendation system rely on data mining; that is, attempting to discover useful patterns in large data sets. For instance, the collaborative filtering algorithms are computation intensive, because they have to exhaustively compare users and items. 

While this kind of recommender system is the most effective when you have large amount of data, the maintenance of such a data pipeline is painful and costly. Yeah, to serve the result as soon as possible, for effectively user perference capturing, usually you will need other tricks to make up for the time consuming model training process.



## For Small

Small business usually doesn't have many users, and the number of items increases gently. If you are in this case, content-based recommender will be suitable for you. Old fashion still works. Other than the user activities you logged, you will need new data to put it work. It is exactly how we did back a decade ago, the **context** and **relevance**. 

Content-based recommender can easily give related results user might be interested at by your existing search engine like Solr or ElasticSearch. Then some other tricks can be added, like novelty, blacklists or event lists. You still can get reasonable recommendations without all those burdens. In fact, the academic literature is still working on small scale recommendation solutions as well.[^2] 



## To Scale

From the medium to the large scale business, collaborative filtering (CF) recommender systems can really make a profit from your user data. Every type of logs will be valuable. Every move user made on your website may be the key to understand what he/she wants. These data are huge. CF algorithms are born for this. 

Although you can find a lot of open sourced machine learning libraries coming with a bunch of CF algorithms, it does not mean you can stop there. Don't forget, users want personalized recommendations, that is to say, users need to know what they want. But user preferences are dynamic. For example, the same user might want beers, but an hour later his last login, diapers might be what he wants. Capturing the vibes of users plays a big role. However, CF algorithms need time to be trained. It is hard to reflect the users interests immediately, because of the training latency. Those libraries have only completed half of the game.

You need more weapons to capture all those vibes. Analysing user activities, event boosting, and item relevance will be your bullets. Content-based recommender still plays in this game. Getting familiar with your users and the understanding of items and interactions between them are the keys to boost your business to the next level.



## Conclusion

Different recommender systems will be needed because the scale of the business is different. A website with text-rich items but less user activities is suitable for content-based recommenders, and it really works. For a larger website, data mining based recommenders from open sourced libraries might have played only a part of the game. Capturing the user interests immediately is the key to boost your business to the next level.

----

#### References:

[^1]: Recommender system, https://en.wikipedia.org/wiki/Recommender_system
[^2]: Recommendation Systems in-the-Small, lhttp://www.linozemtseva.com/research/2014/rsse_book/rits_chapter.pdf

