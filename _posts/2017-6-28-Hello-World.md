---
layout: post
date: 2017-06-28 -0500
title: New York City MTA Subway Traffic
categories: project
---

For our first project at Metis Data Science Bootcamp, we used MTA subway traffic data to analyze ridership volumes across stations in the subway system.

# The Problem

A fictitious company (WTWY Inc. - Women Tech Women Yes) approached our team with a problem they needed help solving. WTWY's mission was to increase the participation of Women in the Tech industry and STEM subjects and was hosting a fundraising gala early in the summer. 

WTWY wanted to advertise the event targeting wealthy individuals and increase awareness of itself among people who were enthusiastic and passionate about its mission. To this end, WTWY wanted to optimize the location of their canvassing team across the subway stations during its marketing campaign. 

Our deadline to analyze the data and make a recommendation was a few days away, and after a conversation with WTWY we determined the following information which helped guide our analysis:

1. The gala was to occur the first week of June.
2. The canvassing team was comprised of three groups.
3. Canvassing groups were mobile and could change locations during the day.
4. Canvassing would occur for one week in the spring.
5. WTWY's marketing team determined the target demographic to be individuals already in Tech and in particular: Women already in Tech.

# Our Solution

Since canvassing would occur during one week in the spring, we analyzed data for the months of March, April, and May 2017.

Our initial thought was to determine traffic volume for the following target audiences: women in tech on their way to work, individuals in tech during work hours, and women in tech returning home from work.

Given our target audience, it seemed best to concentrate on the subway stations in locations where wealthy individuals working in Tech live, and where there is a high density of Tech companies and learning Institutions.

We thought to use census gender and income information in the NYC area to determine those zip codes with a higher proportion of wealthy women but decided against using this data because this information would not tell us if these individuals or women worked in Tech. Therefore we decided to concentrate on subway stations in areas of high density of Tech companies and learning Institutions.

To capture tech individuals on their way to work, we would analyze turnstile exit counts from subway stations in tech-heavy areas, assuming that people exiting these stations in the morning were working nearby. Similarly, we would examine turnstile entry counts in the evening thinking that people entering these stations worked nearby and were coming out of work. During work hours we would analyze turnstile entry and exit counts information.








![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.
