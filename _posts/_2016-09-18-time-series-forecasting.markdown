---
layout:     post
title:      Magic quadrant for startups & people
date:       2016-09-14 15:31:19
summary:    
categories: time_series, research
---

#### First glance on the data

Input data for forecasting is daily delinquency bucket volumes since 2013 - all in all 14 time series which are correlated with the neighbor with some lag. Usually this lag is around 30 days, but not always.
On the right table you can see the example of one of the time series. Rows are days, columns are months. White squares are weekends/holidays or were excluded as outliers (outliers are covered in the next part). You can also see that the time series have dual seasonality (weekly and monthly) and trend.
Each time series has its unique trend, seasonality and variance. As you can see on the chart below, the variance can be really high
(show B1 normalized)
The task is to develop an algorithm which will predict Y for the next month for each time series with Mean Absolute Percentage Error (MAPE) < 3%.
