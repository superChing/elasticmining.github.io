---
layout: post
title: The First Class of Tableau - part 1
categories: data visualization
description: Learning Tableau step by step.
comments: true
tags: [tableau, data visualization]
image:
feature:
credit:
creditlink:
date: 2016-1-18T00:00:00+00:00
author: bryanyang
---
# The First Class of Tableau-part 1



Tableau ([http://www.tableau.com](http://www.tableau.com/)) is a famous visualization tool nowadays. Gartner (the world’s leading information technology research and advisory company) conducts a survey for business interagency and Analysis platform-Magic Quadrant for Business Intelligence and Analytics Platforms([link here](http://www.gartner.com/technology/reprints.do?id=1-2ACLP1P&ct=150220)), and Tableau stands in the leaders area in the report. 
![garner](/img/blog/bryan/001.png)

In this article, I will assist you to walk into the world of Tableau step by step.

### Download and Install
* You can download the free trial version of Tableau Desktop on the http://www.tableau.com/products/desktop. 
* Please download the comprehensive version according to your OS version(win-32/ win64/ Mac)
* Double click the file you download, and install it through the install wizard.
* **Open it!**
* The first time you open Tableau, you need to enter the register code or choose free trail.
* You should see the screen like this:

![002](/img/blog/bryan/002.png)

### Connect to the Data source
* Tableu provides a easy way to connect to your data source including csv, .excel file in your local system and mysql, postgresql through ODBC, even the Hive and Apache Spark! 
![003](/img/blog/bryan/003.png)
* Choose the sheet **“Data Source”** under the main screen
* Choose one of the data source. In this case, I chose the **“Text File“**(The sample file comes from http://catalog.data.gov/dataset/consumer-complaint-database).
* If you read text file successfully, you could see this:
![004](/img/blog/bryan/004.png)
* Tableau will use the first row as the column name automatically and You can click the **“Update Now”** button for looking over the content of the data.

### Plot the First Graph in Tableau
* Now you have read the data, and please move on the worksheet (**sheet 1 **is next to the sheet of **Data Source**). 
![005](/img/blog/bryan/005.png)
* The left side is the list of the features from the data (I’ll explain the difference between Dimensions and Measures later), and the right side is the plot area. There is a **“Show me”** window for controlling the plot in the rightmost. 
* **Dimensions** include the features which are the nominal scale, categorical information, date, and geographic data; **Measures** include the features containing numeric (quantitative) information as a measure and Number of Records.
* Now, drag the **Product** to the **Columns** in the right side, then you can see all values of **Product** list on the right side.
![006](/img/blog/bryan/006.png)
* If we want to know how many records in each Product, drag the Number of Records to the **Text** button and drop down.
![007](/img/blog/bryan/007.png)
![008](/img/blog/bryan/008.png)
* Now you have already done the basic and the most actions of Tableau-**drag and drop**. 
* When you drag and drop the features to the graph area, you can see the the "Show me" window become colorful. You can try to click each button with color and see what will happen.
* I choose **stacked bars** for example.
![009](/img/blog/bryan/009.png)
* And you have done a horizon plot! 
![010](/img/blog/bryan/010.png)

In this step by step example, I demonstrated how to draw a plot in tableau through drag and drop. You can visualize a data in a few simple step in tableau and that's why Tableau was a leader of Bussiness Intelligence  platform in Gartner report.

## Reference

* [Magic Quadrant for Business Intelligence and Analytics Platforms, 2015](http://www.gartner.com/technology/reprints.do?id=1-2ACLP1P&ct=150220)

* [Tableu Offical Site](http://www.tableau.com)

