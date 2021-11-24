---
title: "Covid Death Forecasting"
author: Malcolm Smith Fraser
date: 2021-11-22T19:36:36Z
tags:
series:
draft: true
---

In this post I will discuss the overall scope, the teams approach, and the results of a
final project I completed for the Statistical Program for Big Data course at Duke. 

### Project Scope
The goal of this project was to determine if a univariate autoregressive
model or a multivariate autoregressive model was better at forecasting COVID-19 
deaths in the Durham area (DHPC). The data used to perform this analysis came the
AWS COVID-19 data lake, and from the North Carolina Department of Heath and Human
Services's COVID-19 dashboard.

### Results
We determined that a multivariate model build with features that (in our dataset) 
had relatively correlations with deaths (Ventilators Available, ICU Empty Staffed Beds,
Inpatient Beds In Use, Inpatient Empty Staffed Beds, Confirmed Cases) was the more
effective than a univariate model, and multivariate models that both included all 12 of
the features in the original dataset or just the features highly correlated with deaths. 
We arrived at these findings by comparing the average RMSE of the forecasted values
over a five week period at the end of our dataset. 
                    | Model                       | RMSE        |
                    | --------------------------- | ----------- |
                    | Univariate                  | 2.183       |
                    | Multivariate (all features) | 8.994       |
                    | Multivariate (high corr)    | 3.380       |
                    | **Multivariate (low corr)** | **1.577**   |
                    
![Example image](/images/univariate_forcasting.png)     
![Example image](/images/multivariate_forcasting.png)


### Our approach
After cleaning and merging our data, we were left 76 weeks of data for number of
COVID-19 related Hospitalizations, Adult ICU COVID-19 Patients, Confirmed Patient
Admitted in the Last 24 Hours, Hospitalized and Ventilated COVID Inpatient Count, 
Ventilators In Use, Ventilators Available, ICU Beds In Use, ICU Empty Staffed Beds,
Inpatient Beds In Use, Inpatient Empty Staffed Beds, Confirmed Cases, and Deaths.
For each approach to forcasting, the model was trained on the first 71 weeks, with
the last five weeks held out to evaluate the efficacy of the forcasting. 

The dataset was made stationary with the use of first order or second order differencing, 
and a threshold of .5 was used to determine high vs. low correlation. 

A notebook with our the project source code can be found on our github repo.
https://github.com/jenniesun/covid_forcasting.
