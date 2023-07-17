## bellabeat fitness tracker case study for Google Data Annyltics Course Capstone by: Adam Pukalo
**Date: July 13, 2023** 
#
_This case study will follow the six step data analysis procee:_
###  1. [Ask](#step-1-ask)
###  2. [Prepare](#step-2-prepare)
###  3. [Process](#step-3-process)
###  4. [Analyze](#step-4-analyze)
###  5. [Share](#step-5-share)
###  6. [Act](#step-6-act)

![download](https://github.com/pukalo19/bellabeat_case_study_Google_Capstone_R/assets/131198211/9e72c0f6-3bf2-49b1-86a3-0bf6412ce524)

## Introduction 
bellabea is a high-tech company that manufactures health-focused smart products. Collecting data on activity, sleep, stress, and reproductive health that has allowed Bellabeat to empower
women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. Founders Urška Sršen and Sando Mur believe that with proper data analysis, bellabeat can make intelligent decisions to push the company to new heights of success.

## Step 1: Ask

## Business Task
Analyze the data of an established competitor, identify growth opportunities, and make recommendations for the bellabeat marketing strategy.

**Stakeholders:** 

* Urška Sršen: Cheif Creative Officer of bellabeat and cofounder
* Sando Mur: Cofounder of bellabeat and mathematician

**Guiding questions for analysis**
1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?

## Step 2: Prepare
The data used in this anlysis can be found here:[FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) CC0: Public Domain, dataset made available through [Mobius](https://www.kaggle.com/arashnic)
The data was uploaded and stored into R Studio Cloud. 
This dataset was generated by respondents to a distributed survey via Amazon Mechanical Turk between 03.12.2016-05.12.2016. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring.

ROCCC Data Review 
* **Reliability - LOW:** The data comes from only 30 fitbit users who consented to the submission of personal tracker data.
* **Original - LOW:** Amazon Mechanical Turk a third party was used to collect the data.
* **Comprehensive - MED:** The dataset contains six different fields
* **Current - LOW:** The dataset is from 2016, quite old now, and users habits could have changed over this time period.
* **Cited - LOW:** Unkown as the data was collected from a third party.

## Loading necessary packages into R
```
library(tidyverse)
library(lubridate) 
library(dplyr)
library(ggplot2)
library(tidyr)
library(janitor)
```
## Step 3: Process 
[Back to top](#introduction)

### Importing the data
```
#Reading the data
activity <- read.csv("dailyActivity_merged.csv",TRUE,",")
calories <- read.csv("dailyCalories_merged.csv",TRUE,",")
intensities <- read.csv("hourlyIntensities_merged.csv", TRUE, ",")
sleep <- read.csv("sleepDay_merged.csv",TRUE, ",")
weight <- read.csv("weightLogInfo_merged.csv",TRUE, ",")
```
Now that all the data we will be using is uploaded we can start the process of cleaning the data. Lets take a look at the data and 
```
          Id ActivityDate TotalSteps TotalDistance TrackerDistance
1 1503960366    4/12/2016      13162          8.50            8.50
2 1503960366    4/13/2016      10735          6.97            6.97
3 1503960366    4/14/2016      10460          6.74            6.74
4 1503960366    4/15/2016       9762          6.28            6.28
5 1503960366    4/16/2016      12669          8.16            8.16
6 1503960366    4/17/2016       9705          6.48            6.48
  LoggedActivitiesDistance VeryActiveDistance ModeratelyActiveDistance
1                        0               1.88                     0.55
2                        0               1.57                     0.69
3                        0               2.44                     0.40
4                        0               2.14                     1.26
5                        0               2.71                     0.41
6                        0               3.19                     0.78
  LightActiveDistance SedentaryActiveDistance VeryActiveMinutes
1                6.06                       0                25
2                4.71                       0                21
3                3.91                       0                30
4                2.83                       0                29
5                5.04                       0                36
6                2.51                       0                38
  FairlyActiveMinutes LightlyActiveMinutes SedentaryMinutes Calories
1                  13                  328              728     1985
2                  19                  217              776     1797
3                  11                  181             1218     1776
4                  34                  209              726     1745
5                  10                  221              773     1863
6                  20                  164              539     1728
```
Checking the rest of the data 
```
head(calories)
head(intensities)
head(sleep)
head(weight)
head(weight)
```
After looking at all the data, I notice inconsistencies in the recording of the timestamp data. I will convert this to a date time format to fix this. 
```
activity$ActivityDate=as.POSIXct(activity$ActivityDate, format="%m/%d/%Y", tz=Sys.timezone())
activity$date <- format(activity$ActivityDate, format = "%m/%d/%y")

intensities$ActivityHour=as.POSIXct(intensities$ActivityHour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
intensities$time <- format(intensities$ActivityHour, format = "%H:%M:%S")
intensities$date <- format(intensities$ActivityHour, format = "%m/%d/%y")

sleep$SleepDay=as.POSIXct(sleep$SleepDay, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
sleep$date <- format(sleep$SleepDay, format = "%m/%d/%y")
```
Now that we have looked over the data and corrected any inconsistencies/errors we can begin to analyze the data.

## Step 4: Analyze 
[Back to top](#introduction)

We will begin with fiding out how many particitpants there were in each category of the study. 
```
> n_distinct(activity$Id)  
[1] 33
> n_distinct(calories$Id)   
[1] 33
> 
> n_distinct(intensities$Id)
[1] 33
> 
> n_distinct(sleep$Id)
[1] 24
> 
> n_distinct(weight$Id)
[1] 8
```
