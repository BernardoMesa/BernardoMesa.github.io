---
layout: post
date: 2017-07-14 -0500
title: Forecasting Movie Box Office Gross Revenue
categories: project
---

Movie studios, just as any other commercial enterprise, have limited resources and it is in their interest to be able to accurately estimate the revenue a movie project will generate.


Better gross revenue estimates and an accurate confidence interval around this estimate would assist movie studios in raising funds, creating accurate budgets and optimizing their movie pipeline.


## The Data - Thanks Box Office Mojo!


Using scrapy, I scraped information from Box Office Mojo. I focused on collecting the cumulative weekly total gross revenue for the 100 top grossing movies each year from 1983 to 2017. I also colected each movie's weekly ranking, genre, duration, rating, budget, and original release data. In all, I downloaded information for almost 1300 movies.


Since the data was collected in json format, I created a script to transform it into a pandas dataframe and a separate one to clean the data and get it ready for analysis.


## Examining the Dataset


From looking at the weekly box office for several movies it seemed apparent that most of the revenue for each movie was made in the first few weeks the movie was shown. This led me to speculate that the number of movies premiered in the same week had an impact on the total gross revenue made at the box office, and accordingly I created a new feature to investigate this theory.


Prior to 1999 the dataset has very few movies per year. I decided to only use the movies that started between 1998 and 2016, and whose showing ended by 06/29/2017. That way I am trying to ensure not to include movies that were still being displayed at theaters.


![alt text](/images/2017-7-14_post/releasePerYear.svg "Movies Released Each Year")


### Characteristics of the movies in the Dataset

Using the describe() method in pandas I found out some interesting facts about the movies in the dataset. The average movie duration is aproximately 1 hour 50 minutes with 75% of movies lasting less than 2 hours. The average movie is out in theaters for 15 weeks and 75% of movies are out in theaters 19 weeks. The movie which lasted the longest in theaters was the documentary 'Deep Sea 3D (IMAX)' displayed for 580 weeks. 

<style>
.tablelines table, .tablelines td, .tablelines th {
        border: 1px solid black;
        padding: 10px;
        }
</style>

|      | **Duration (HH:MM)** | **Weeks in Theaters** | **Gross Box Office (MM)** |
|------|----------------------|-----------------------|---------------------------|
| 25%  | 1:30                 | 11                    | 40                        |
| Mean | 1:50                 | 15                    | 94                        |
| 75%  | 2:00                 | 19                    | 116                       |
{: .tablelines}


### Distribution of Box Office Gross and Correlations


Inspecting a pair plot revealed the distributions of 'Box Office Gross' ('gtd' in the plot) and 'Budget' to be a bit Log Normal. This led me to used their log values in the regression analysis in order to make their distributions more Normal.


![alt text](/images/2017-7-14_post/pairPlot.svg "Pair Plot")


A pair plot of the log of 'Box Office Gross' and budget show slightly more normal distributions for these variables.


![alt text](/images/2017-7-14_post/pairLogPlot.svg "Pair Plot")


In our analysis 'Box Office Gross' (BGO) is our dependent variable. Examining the correlation of BGO with our other variables we can notice a mild positive (+ve) correlation with movie 'Duration' (0.26), stronger +ve correlation to 'Budget' and 'Weeks in Theaters' (0.6 and 0.45 respectively), and a weak negative (-ve) correlation to number of movies premiering in the same week (-0.12). I decided to drop 'Weeks in Theaters' from the analysis, since its seems likely that 'Weeks in Theaters' is not known prior to movie release.


Also, budget and movie duration appear mildly +ve correlated (0.33), which alerts me to watch out for evidence of multicollinearity between these variables in our subsequent analysis.


### Box Office Gross (BGO) and Budget per year

The mean movie budget seems to have stayed relatively stable at around 50 million, while its distribution seems to be becoming more right skewed with time with the top quartile budgets gradually increasing with time.


![alt text](/images/2017-7-14_post/budgetVsYear.svg "Budget Vs. Year")


BGO distributions are right skewed and the mean budget seems to stay in the 50 to 90 million dollars range through out time.


![alt text](/images/2017-7-14_post/gtdVsYear.svg "Box Office Gross Vs. Year")


### Movie Premiere condition


Box Office distribution grouped by number of movies released in a week seem to shift lower as more movies are launched the same week. The mean of the distributions seems to remain in the 10^(7.75) to 10^8 range independently of the number of movies launched in the week.


![alt text](/images/2017-7-14_post/gtdLogVsNumReleases.svg "Log Box Office Vs. Same Week Number of Movies Releases")


A release of another movie of the same genre seems to negatively affect Box Office outcomes and shifts its distribution lower.


![alt text](/images/2017-7-14_post/gtdLogVsSameGenreNumReleases.svg "Log Box Office Vs. Same Week Same Genre Number of Movies Releases")


There does not seem to be a discernable pattern between Box Office Outcomes and day of the week a movie is released, except that the widest range of outcomes seems to occur when movies are released on Fridays.


















