# Bellabeat---Data-Analysis-Case-Study

## Overview
This project is a case study for the final stage of the Google Data Analysis Course on Coursera. The objective is to analyze data from smart device users to help the high-tech company Bellabeat unlock new growth opportunities. Bellabeat has invested in traditional advertising media such as radio, TV, print, and out-of-home billboards, as well as digital channels like Google Search, Instagram, Facebook, and Twitter.

## Company Background
Founded in 2013, [Bellabeat](https://bellabeat.com/) is a high-tech company that develops wellness tracking devices specifically for women. By 2016, Bellabeat had launched multiple products and expanded its business globally. These products became available on their own e-commerce platform, as well as through various online retailers. Bellabeat places a strong emphasis on digital marketing, utilizing Google Search, video advertisements, and consumer engagement on social media platforms.

Bellabeat has introduced five products:

**Bellabeat app**: This app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits, helping them better understand their current habits and make healthier decisions.

**Leaf**: Bellabeat's classic wellness tracker can be worn as a bracelet, necklace, or clip. It connects to the Bellabeat app to monitor activity, sleep, and stress levels.

**Time**: This wellness watch combines the timeless look of a classic timepiece with smart technology to track user activity, sleep, and stress. It syncs with the Bellabeat app to provide insights into daily wellness.

**Spring**: A smart water bottle that tracks daily water intake to ensure proper hydration throughout the day. It connects to the Bellabeat app to monitor hydration levels.

**Bellabeat membership**: A subscription-based program that offers users 24/7 access to personalized guidance on nutrition, activity, sleep, health and beauty, and mindfulness based on their lifestyle and goals.



## Project Goals
The company aims to gain insights into how people are using their smart devices. Using this information, we will provide high-level recommendations to inform Bellabeat's marketing strategy.

### 1. Ask 

**1.1	What is the problem we are trying to solve?**
	The problem we are trying to solve is understanding how users interact with their smart devices to identify patterns and trends that can help Bellabeat optimize its marketing strategy and unlock new growth opportunities.

**1.2	How can we drive business decisions?**
	By analyzing smart device usage data, we can identify key insights and trends that inform Bellabeat on user behavior. These insights can guide decisions on targeted advertising, product development, and marketing campaigns across various channels such as radio, TV, print, billboards, and social media platforms like Google Search, Instagram, Facebook, and Twitter.


### 2. Prepare and Clean 

**2.1 Data:**
Urška Sršen, Bellabeat’s cofounder and Chief Creative Officer, encourages the use of public data that explores smart device users' habits. She points to a specific dataset: [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit), which is made available through Mobius on Kaggle and updated annually.
•	Data Source: FitBit Fitness Tracker Data (CC0: Public Domain, dataset available through Mobius).
•	Data Type: CSV files.
•	Data Description: The dataset consists of logs of customer activities, sleep, and other health metrics. Some files are merged, while others (e.g., calorie or sleep data per minute or second) are very large.

•	We have 33 participants and 3 Types of data collected over 31 days in 2016: 
1-	Physical: Activity, Intensity, and Steps.
2-	Physiology: Heart rate, and calories.
3-	Monitoring: Weight, and Sleep.

•	Limited Descriptive: Age, Sex, Career or Life Style.

• Each file of data was explored to know to work with data, columns, data types, names .. etc

**2.2 Loading Packages:**

```
install.packages("tidyverse")
install.packages("here")
install.packages("skimr")
install.packages("janitor")
install.packages("lubridate")
install.packages("readr")
install.packages("ggpubr")
```

```
library(tidyverse)
library(here)
library(skimr)
library(janitor)
library(lubridate)
library(readr)
library(ggpubr)
```

**2.3 Importing data and with names:**
```
dailyActivity <- read.csv("/cloud/project/Bellabeat/dailyActivity_merged.csv", header = TRUE)
heartrate <- read.csv("/cloud/project/Bellabeat/heartrate_seconds_merged.csv", header = TRUE)
sleepDay <- read.csv("/cloud/project/Bellabeat/sleepDay_merged.csv", header = TRUE)
hourlyCalories <- read.csv("/cloud/project/Bellabeat/hourlyCalories_merged.csv", header = TRUE)
minuteSleep <- read.csv("/cloud/project/Bellabeat/minuteSleep_merged.csv", header = TRUE)
sleepDay <- read.csv("/cloud/project/Bellabeat/sleepDay_merged.csv", header = TRUE)
weight <- read.csv("/cloud/project/Bellabeat/weightLogInfo_merged.csv", header = TRUE)
```

**2.4 Take a look again on data and its structure:**

```
head(dailyActivity)
head(heartrate)
head(sleepDay)
head(hourlyCalories)
head(minuteSleep)
head(weight)
```

```
str(dailyActivity)
str(heartrate)
str(sleepDay)
str(hourlyCalories)
str(minuteSleep)
str(weight)
```

**2.5 Lets check the number of participants in each file:***
```
n_unique(dailyActivity$Id)
n_unique(heartrate$Id)
n_unique(sleepDay$Id)
n_unique(hourlyCalories$Id)
n_unique(minuteSleep$Id)
n_unique(weight$Id)
```

Output:
```
[1] 33
[1] 7
[1] 24
[1] 33
[1] 24
[1] 8
```
All datasets have 33 participants each, except for the heartrate and weight datasets, which have 7 and 8 participants, respectively. I will drop the heartrate and weight datasets because a sample size of 7 and 8 participants is too small to draw conclusions and make recommendations.

**2.6 Duplicates :**

```
sum(duplicated(dailyActivity))
sum(duplicated(sleepDay))
sum(duplicated(hourlyCalories))
sum(duplicated(minuteSleep))
sum(duplicated(sleepDay))
```

Output:
```
[1] 0
[1] 3
[1] 0
[1] 543
[1] 3
```

Drop the duplicated row:
```
dailyActivity <- dailyActivity %>%
  distinct() %>%
  drop_na()
sleepDay <- sleepDay %>%
  distinct() %>%
  drop_na()
hourlyCalories <- hourlyCalories %>%
  distinct() %>%
  drop_na()
minuteSleep <- minuteSleep %>%
  distinct() %>%
  drop_na()

sleepDay <- sleepDay %>%
  distinct() %>%
  drop_na()

```

### 3. Process 

**3.1 Make Time and Date Same Format:**
```
dailyActivity <- dailyActivity %>%
  rename(date = ActivityDate) %>%
  mutate(date = as_date(date, format = "%m/%d/%Y"))

sleepDay <- sleepDay %>%
  rename(date = SleepDay) %>%
  mutate(date = as_date(date, format ="%m/%d/%Y %I:%M:%S %p", tz = Sys.timezone()))

hourlyCalories <- hourlyCalories %>% 
  rename(date_time = ActivityHour) %>% 
  mutate(date_time = as.POSIXct(date_time, format ="%m/%d/%Y %I:%M:%S %p" , tz=Sys.timezone()))

minuteSleep <- minuteSleep %>% 
  rename(date_time = date) %>% 
  mutate(date_time = as.POSIXct(date_time, format ="%m/%d/%Y %H:%M", tz=Sys.timezone()))

sleepDay <- sleepDay %>% 
  rename(date = date) %>%
  mutate(date = as_date(date, format = "%m/%d/%Y"))
```

**3.2 Lets merge the data of sleeping (per day) to the daily activity file (like JOIN):**
```
daily_activity_sleep <- merge(dailyActivity, sleepDay, by= c("Id", "date"))
```

**3.3 Calculate Time in Bed without Sleeping:**
I will subtract the TotalMinutesAsleep from TotalTimeInBed to determine the pure sleeping time and to see how long people stay in bed without sleeping. I will call this new column 'InBedWithoutSleeping'.

```
daily_activity_sleep$InBedWithoutSleeping <- daily_activity_sleep$TotalTimeInBed - daily_activity_sleep$TotalMinutesAsleep
```
**3.4 Creating new DataFrame that contains the average daily values of data for each participant:**

```{r}
daily_average <- daily_activity_sleep %>%
  group_by(Id) %>%
  summarise (mean_daily_steps = mean(TotalSteps),   mean_daily_calories = mean(Calories), mean_daily_sleep = mean(TotalMinutesAsleep), mean_time_inBed_without_sleep=mean(InBedWithoutSleeping))
```




### 4. Analysis & Share
### 5. Act and Recomendation 



