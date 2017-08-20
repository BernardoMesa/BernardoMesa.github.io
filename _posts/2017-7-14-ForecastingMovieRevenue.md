---
layout: post
date: 2017-07-14 -0500
title: Forecasting Movie Total Gross Revenue
categories: project
---

Movie studios, just as any other commercial enterprise, have limited resources and it is in their interest to be able to accurately estimate the revenue a movie project will generate.


Better gross revenue estimates and an accurate confidence interval around this estimate would assist movie studios in raising funds, creating accurate budgets and optimizing their movie pipeline.


## The Data - Thanks Box Office Mojo!


Using scrapy, I scraped information from Box Office Mojo. I focused on collecting the cumulative weekly total gross revenue for the the 100 top grossing movies each year from 1995 to 2015. I also colected each movie's weekly ranking, genre, duration, rating, budget, and original release data. In all, I downloaded information for almost 1300 movies.


Since the data was collected in json format, I created a script to transform it into a pandas dataframe and a separate one to clean the data and get it ready for analysis.


## Examining the Dataset


From looking at the weekly box office for several movies it seemed apparent that most of the revenue for each movie was made in the first few weeks the movie was shown. This led me to speculate that the number of movies premiered in the same week had an impact on the total gross revenue made at the box office, and accordingly I created a new feature to investigate this theory.


Prior to 1999 the dataset has very few movies per year. I decided to only use the movies that started between 1998 and 2016, and whose showing ended by 06/29/2017. That way I am trying to ensure not to include movies that were still being displayed at theaters.


![alt text](/images/releasePerYear.png "Movies Released Each Year")


### Characteristics of the movies in the Dataset

Using the describe() method in pandas I found out some interesting facts about the movies in the dataset. The average movie duration is aproximately 1 hour 50 minutes with 75% of movies lasting less than 2 hours. The average movie is out in theaters for 15 weeks and 75% of movies are out in theaters 19 weeks. The movie which lasted the longest in theaters was the documentary 'Deep Sea 3D (IMAX)' displayed for 580 weeks. 


|      | **Duration (HH:MM)** | **Weeks in Theaters** | **Gross Box Office (MM)** |
|------|----------------------|-----------------------|---------------------------|
| 25%  | 1:30                 | 11                    | 40                        |
| Mean | 1:50                 | 15                    | 94                        |
| 75%  | 2:00                 | 19                    | 116                       |


### Distribution of our dependent variable


A pair plot revealed the distributions of 'Box Office Gross' ('gtd' in the plot) and 'Budget' to be a bit log normal because of which we used their log values in the regression analysis.



![alt text](/images/pairPlot.png "Pair Plot")


###Correlations


In our analysis 'Gross Box Office' (GBO) is our dependent variable. Examining the correlation of 'Gross Box Office' (GBO) with the other variables we can notice a mild positive (+ve) correlation with movie 'Duration' (0.26), stronger +ve correlation to 'Budget' and 'Weeks in Theaters' (0.6 and 0.45 respectively), and a mild negative (-ve) correlation to number of movies premiering in the same week (-0.12). I decided to drop 'Weeks in Theaters' from the analysis, since its seems likely that 'Weeks in Theaters' is not know ahead of time.


Also, the correlation between our dependent variables did not seem strong enough to rise significant concerns of multicollinearity being present.










