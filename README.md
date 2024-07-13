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
	<br/>The problem we are trying to solve is understanding how users interact with their smart devices to identify patterns and trends that can help Bellabeat optimize its marketing strategy and unlock new growth opportunities.

**1.2	How can we drive business decisions?**
	<br/>By analyzing smart device usage data, we can identify key insights and trends that inform Bellabeat on user behavior. These insights can guide decisions on targeted advertising, product development, and marketing campaigns across various channels such as radio, TV, print, billboards, and social media platforms like Google Search, Instagram, Facebook, and Twitter.


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

**3.2 Merging Data:**
<br/>Lets merge the data of sleeping (per day) to the daily activity file (like JOIN)
```
daily_activity_sleep <- merge(dailyActivity, sleepDay, by= c("Id", "date"))
```

**3.3 Calculate Time in Bed without Sleeping:**
<br/>I will subtract the TotalMinutesAsleep from TotalTimeInBed to determine the pure sleeping time and to see how long people stay in bed without sleeping. I will call this new column 'InBedWithoutSleeping'.<br/>

```
daily_activity_sleep$InBedWithoutSleeping <- daily_activity_sleep$TotalTimeInBed - daily_activity_sleep$TotalMinutesAsleep
```
**3.4 Average Daily Data:**
<br/>Creating new DataFrame that contains the average daily values of data for each participant

```{r}
daily_average <- daily_activity_sleep %>%
  group_by(Id) %>%
  summarise (mean_daily_steps = mean(TotalSteps),   mean_daily_calories = mean(Calories), mean_daily_sleep = mean(TotalMinutesAsleep), mean_time_inBed_without_sleep=mean(InBedWithoutSleeping))
```

### 4. Analysis & Share

**4.1 User Types Distribution:**

Now, I want to classify the types of users into four groups according to their average total steps:

Sedentary: fewer than 5,000 steps per day
Lightly Active: between 5,000 and 7,499 steps per day
Fairly Active: between 7,500 and 9,999 steps per day
Highly Active: 10,000 steps per day or more

```
classify_activity <- function(steps) {
  if (steps < 5000) {
    return("Sedentary")
  } else if (steps >= 5000 & steps < 7500) {
    return("Lightly_Active")
  } else if (steps >= 7500 & steps < 10000) {
    return("Fairly_Active")
  } else {
    return("Highly_Active")
  }
}

daily_average$user_type <- daily_average %>%
  mutate(user_type = sapply(mean_daily_steps, classify_activity))
```

And Lets Visualize the type of users:

```
freq <- table(daily_average$user_type$user_type)
percentages <- prop.table(freq) * 100

# Create the pie chart with percentages
pie(freq, labels = paste(names(freq), ": ", freq, " (", round(percentages, 1), "%)", sep = ""), main = "Distribution of User Types")
```
![image](https://github.com/user-attachments/assets/702fa22d-2669-48b8-9715-ae59d773f44f)

**4.2 Average Calories per Hour**:
Now, I will create a data frame that shows the average calories burned per hour over a 24-hour period. The file to be used is 'hourlyCalories'.

```
AverageCaloriesPerHour <- hourlyCalories %>%
    mutate(hour = format(date_time, "%H")) %>%
    group_by(hour) %>%
    summarise(avg_calories = mean(Calories, na.rm = TRUE))

```

Let's visualize it to see during which hours people burn the most calories:

```
ggplot(AverageCaloriesPerHour, aes(x = hour, y = avg_calories)) + geom_bar(stat = "identity", fill = "steelblue") + labs(title = "Average Calories", x = "Hour", y = "Calories") +
  theme_minimal()

```
![image](https://github.com/user-attachments/assets/f1129df3-9982-4eb4-9b5f-11ababfffe4d)



**4.3 Daily Average Sleep Time and Average Steps:**
Let's now see the average sleep minutes and average steps for each weekday:

```

weekday_steps_sleep <- daily_activity_sleep %>%
  mutate(weekday = weekdays(date))

weekday_steps_sleep$weekday <-ordered(weekday_steps_sleep$weekday, levels=c("Monday", "Tuesday", "Wednesday", "Thursday",
"Friday", "Saturday", "Sunday"))

 weekday_steps_sleep <-weekday_steps_sleep%>%
  group_by(weekday) %>%
  summarize (mean_steps=mean(TotalSteps), mean_sleep = mean(TotalMinutesAsleep))
```


**4.4 Smart Devices Usage**
Now, let's see how users are utilizing their smart devices.
I grouped the data by user ID and calculated the number of days each user used their smart device. I then categorized their usage into three groups: 'low use' for 1 to 10 days, 'moderate use' for 11 to 20 days, and 'high use' for 21 to 31 days.

```{r}
daily_use <- daily_activity_sleep %>%
  group_by(Id) %>%
  summarize(days_used=sum(n())) %>%
  mutate(usage = case_when(
    days_used >= 1 & days_used <= 10 ~ "low use",
    days_used >= 11 & days_used <= 20 ~ "moderate use", 
    days_used >= 21 & days_used <= 31 ~ "high use", 
  ))
head(daily_use)
```
Lets see the percentage of each:

```
daily_use_percent <- daily_use %>%
  group_by(usage) %>%
  summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(usage) %>%
  summarise(total_percent = total / totals) %>%
  mutate(labels = scales::percent(total_percent))

daily_use_percent$usage <- factor(daily_use_percent$usage, levels = c("high use", "moderate use", "low use"))

head(daily_use_percent)
```
![image](https://github.com/user-attachments/assets/b47e754c-887b-4f63-bd78-e4b976f8f00e)


```
daily_use_percent %>%
  ggplot(aes(x="",y=total_percent, fill=usage)) +
  geom_bar(stat = "identity", width = 1)+
  coord_polar("y", start=0)+
  theme_minimal()+
  theme(axis.title.x= element_blank(),
        axis.title.y = element_blank(),
        panel.border = element_blank(), 
        panel.grid = element_blank(), 
        axis.ticks = element_blank(),
        axis.text.x = element_blank(),
        plot.title = element_text(hjust = 0.5, size=14, face = "bold")) +
  geom_text(aes(label = labels),
            position = position_stack(vjust = 0.5))+
  scale_fill_manual(values = c("#006633","#00e673","#80ffbf"),
                    labels = c("High use - 21 to 31 days",
                                 "Moderate use - 11 to 20 days",
                                 "Low use - 1 to 10 days"))+
  labs(title="Daily use of smart device")
```

![image](https://github.com/user-attachments/assets/c503b20f-cab3-423f-9418-7264414eb621)


**4.5 Correlation**
Now, let's examine the correlation between daily steps and calories, as well as between daily steps and sleep.

```
ggplot(data= subset(daily_activity_sleep,!is.na(TotalMinutesAsleep)),aes(TotalSteps,TotalMinutesAsleep))+
    geom_rug(position= "jitter", size=.08)+
    geom_jitter(alpha= 0.5)+
    geom_smooth(color= "blue", linewidth=.6)+
    stat_cor(method = "pearson", label.x = 15000, label.y = 650)+
    labs(title= "Daily steps vs. sleep", x= "Daily Steps", y= "Minutes Asleep")+
    theme_minimal()

```

![image](https://github.com/user-attachments/assets/be3d46eb-03ba-4120-9e61-621a517487d9)



```
ggplot(daily_activity_sleep,aes(TotalSteps,Calories))+geom_jitter(alpha=.5)+
    geom_rug(position="jitter", linewidth=.08)+
    geom_smooth(linewidth =.6)+
    stat_cor(method = "pearson", label.x = 20000, label.y = 2300)+
    labs(title= "Daily Steps vs. Calories", x= "Daily steps", y="Calories")+
    theme_minimal()
```

![image](https://github.com/user-attachments/assets/97b6afed-7f26-4717-8eb0-f3927cc69db7)


**4.6 User Type vs Time in the Bed without Sleeping**

We want to see the average TimeInBedWithoutSleep for each user type:
```

avg_timeInBed_UserType <- daily_average %>%
  group_by(user_type$user_type) %>%
  summarise(mean_time_in_Bed_per_UserType = mean(mean_time_inBed_without_sleep, na.rm = TRUE))

head(avg_timeInBed_UserType)
```

![image](https://github.com/user-attachments/assets/405f7c67-ef8a-4a61-a49b-1d6b1e0a8951)




```
colnames(avg_timeInBed_UserType) <- c("user_type", "mean_time_in_Bed_per_UserType")
ggplot(avg_timeInBed_UserType, aes(x = user_type, y = mean_time_in_Bed_per_UserType)) +
  geom_bar(stat = "identity", fill = "skyblue", color = "black") +
  labs(title = "Average Time in Bed per User Type",
       x = "User Type",
       y = "Mean Time in Bed") +
  theme_minimal()
```

![image](https://github.com/user-attachments/assets/9bce4f2a-0e53-4b1d-aa1c-5fbf76bdf269)


### 5. Act and Recomendation 



