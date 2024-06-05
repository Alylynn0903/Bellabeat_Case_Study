# Bellabeat

Bellabeat is a high tech company that manufactures health-focused smart products for women. Since launching in 2013, they have grown rapidly and quickly positioned themselves to become a large player in global smart devices. Urška Sršen, Co-founder and Chief Creative Officer of Bellabeat wants an analyst to review the data that is received from other smart devices to help unlock new growth opportunities for the company.


### **Questions for the Analyst**

1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?


### **Business Task**

To identify growth opportunities for which Bellabeat may improve marketing strategies to keep up with trends in smart device usage.


### **Loading Packages**

```{r loading packages}
library(tidyverse)
library(dplyr)
library(ggplot2)
```


### **Importing Files**

For this case study I will be using the data provided by [FitBit Fitness Tracker](https://www.kaggle.com/datasets/arashnic/fitbit)

```{r}
activity <- read_csv("C:/Users/crazz/Desktop/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
calories <- read_csv("C:/Users/crazz/Desktop/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
intensities <- read.csv("C:/Users/crazz/Desktop/Fitabase Data 4.12.16-5.12.16/hourlyIntensities_merged.csv")
sleep <- read_csv("C:/Users/crazz/Desktop/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
weight <- read_csv("C:/Users/crazz/Desktop/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
steps <- read_csv("C:/Users/crazz/Desktop/Fitabase Data 4.12.16-5.12.16/hourlySteps_merged.csv")
```
### **Exploring the data**

```{r activity}
head(activity)
```
```{r Calories}
head(calories)
```

```{r}
head(sleep)
```

```{r}
head(intensities)
```

```{r}
head(steps)
```

### **Fixing Format**

As an analyst, I went through the data sets and realized the dates and times were inconsistent. Some dates were written in character format and not date format. All dates were changed to be in date format and time format was converted to hour, minutes, and seconds to make everything consistent in all charts for analysis. 

```{r}
str(activity)
```

```{r}
str(calories)
```

```{r}
str(calories)
```

```{r}
str(intensities)
```


```{r}
str(sleep)
```
```{r}
str(weight)
```

```{r}
str(steps)
```


```{r}
#activity
activity_cleaned <- activity %>% 
  rename(Date = ActivityDate) %>% 
  mutate(Date = as.Date(Date, format = "%m/%d/%y")) %>%
  rename_with(tolower)
```

```{r}
#calories
calories_cleaned <- calories %>% 
  rename(Date = ActivityDay) %>% 
  mutate(Date = as.Date(Date, format = "%m/%d/%y"))%>%
  rename_with(tolower)
```


```{r}
#sleep
sleep_cleaned <- sleep %>% 
  rename(Date = SleepDay) %>% 
  mutate(Date = as.Date(Date, format = "%m/%d/%y"))%>%
  rename_with(tolower)
```


```{r eval=FALSE, include=FALSE}
#weight
weight_cleaned <- weight %>%
  rename(Date = Date) %>%
  mutate(Date = as.Date(Date, format = "%m,$d,$y,"))%>%
  rename_with(tolower)
```

```{r include=FALSE}
#intensities
intensityies_cleaned <- intensities %>% 
  rename(Date = ActivityHour) %>% 
  mutate(Date = as.Date(Date, format = "%m/%d/%y"))
intensities$time <- format(intensities$ActivityHour, format = "%H:%M:%S")
intensities$date <- format(intensities$ActivityHour, format = "%m/%d/%y")
```


```{r}
#steps
steps_cleaned <-steps %>%
  rename(Date = ActivityHour) %>% 
  mutate(Date = as.Date(Date, format = "%m/%d/%y"))%>%
  rename_with(tolower)
steps$time <- format(steps$ActivityHour, format = "%H:%M:%S")
steps$date <- format(steps$ActivityHour, format = "%m/%d/%y")
```

### **Exploring and Summarizing Data**

```{r}
n_distinct(activity$Id)
n_distinct(calories$Id)
n_distinct(intensities$Id)
n_distinct(sleep$Id)
n_distinct(weight$Id)
n_distinct(steps$Id)
```

Running this code allows us to get a number of how many participant IDs are recorded in each data set.

Thirty-three participants were recorded for activity, calories, steps, and intensities.

Twenty-four participants recorded their sleep records.

Only eight participants recorded their weight.

```{r}
# activity
activity %>%  
  select(TotalSteps,
         TotalDistance,
         SedentaryMinutes, Calories) %>%
  summary()

# explore num of active minutes per category
activity %>%
  select(VeryActiveMinutes, FairlyActiveMinutes, LightlyActiveMinutes) %>%
  summary()

# calories
calories %>%
  select(Calories) %>%
  summary()
# sleep
sleep %>%
  select(TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed) %>%
  summary()
# weight
weight %>%
  select(WeightKg, BMI) %>%
  summary()
```

Most participants do little moving, averaging around 16 hours, and are lightly active.

The average amount of sleep everyone gets in a night is 7 hours.

The average amount of steps a person per day is 7,638 steps, which is a little below the average goal of 8,000 per day.


### **Merging Data for Further Analysist**


I've decided to merge the sleep and activity tables together as they go hand in hand. If you do not get a good amount of sleep, it may have an affect on activity levels during the next day.

```{r}
activity_sleep <- merge(sleep_cleaned, activity_cleaned, by=c("id", "date"))
```

```{r}
head(activity_sleep)
```

```{r}
activity2 <- activity_cleaned %>% 
  mutate(totalactiveminutes = veryactiveminutes + fairlyactiveminutes + lightlyactiveminutes)
```

### **Visualizing the Data**


```{r}
ggplot(data = activity_cleaned, aes(x=totalsteps, y=calories)) + geom_point()  +
  geom_smooth(method = "loess") +
  geom_smooth() + 
  labs(title = "Total Steps vs Calories Burn", x = "Total Steps", y = "Calories")+
  theme(plot.title = element_text(size=12), text = element_text(size=10))
```


It is clear that you can see a positive correlation in this graph. The more steps a person takes the more calories they burn.


```{r}
ggplot(data=sleep, aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) + 
  geom_point()+ 
  labs(title="Total Minutes Asleep vs. Total Time in Bed", x= "Minutes Asleep", y= "Time in Bed")+
  theme(plot.title = element_text(size=12), text = element_text(size=10))
```

The relationship between time spent in bed and time spent asleep is pretty linear. Bellabeat could improve participants' sleep habits by setting reminders for them to go to sleep. Another option Bellabeat could do is track and find a correlation in a person's sleep habit and predict the best time for them to go to bed and amount of sleep a person will need a night to better function. Some people function better sleeping 5-6 hours a night while others need 8-10 hours a night.


```{r}
ggplot(data=activity_sleep, aes(x=totalminutesasleep, y=sedentaryminutes)) + 
geom_point() + geom_smooth() +
  labs(title="Minutes Asleep vs. Sedentary Minutes", x= "Minutes Asleep", y="Sedentary Minutes")+
  theme(plot.title = element_text(size=12), text = element_text(size=10))
```

Here it is shown that there is a negative relationship between sleep time and sedentary minutes. Although we can not fully say that this little sleep is the cause of sedentary minutes, it sure has an effect on it.

```{r}
ggplot(activity2, aes(x= totalactiveminutes, y=calories))+
  geom_point()+
  geom_smooth()+
  labs(title="Daily Activity vs. Calories Burned", x= "Active Minutes", y="Calories Burned")+
  theme(plot.title = element_text(size=12), text = element_text(size=10))
```

### **Summary**

Since being founded in 2013, Bellabeat is on the rise to becoming one of the top fitness trackers for women’s health. They have developed a product focused on women, to assist them with their day to day activities. Bellabeat has helped millions of women become more in tune with their body as they go through cycles and even pregnancies.

After analyzing the [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) some things stood out that would influence Bellabeats marketing team. 

Without knowing the key details of the participants for this study,such as age, gender, or occupational status, the data still shows that not many people are moving during the day. The participants could be a teenage student is high school,a middle age entrepreneur in meetings all day, or a retired senior enjoying a quiet life. There are simple day to day movements but nothing allowing a full healthy lifestyle.

A big part of maintaining a healthy lifestyle is sleep. Yes, recording a persons sleep is a good start but another factor that should be at play is how much sleep a person actually needs. The normal statement has always been, that a person needs 7-9 hours of sleep a night, but not everyone functions properly on that. Some people need 6 hours while others may need 10 hours to feel rested. Adding and having the smart watch analyze how much sleep a person gets along with how much they achieve in a day could help people realize how much sleep they actually need to help maintain that healthy lifestyle.

With reading the data and realizing a lot of people have jobs where they are not moving much or are a stay at home parent, having a guide to help with their lifestyle would be a huge benefit. Implementing a small quiz when users are first signing up for the app would help Bellabeat understand what people need most. Doing this would allow your smart watch experience to become more personal. For the women who are stuck sitting at a desk all day, it will help provide simple workouts they can do while sitting down that won't take them away from working. For stay at home moms it can provide kid friendly workouts or stretches that your kids can join in on. Those are just a few scenario examples, as there are a plethora of lifestyles that a woman can have.

Bellabeat, being a smart watch company geared specifically towards women opens up a whole other dynamic that they can use to become the number one smart watch brand for women. They already provide the ability to help women track and become more in-tune with their monthly cycles and pregnancies but taking it one step further with providing helpful information on those events would be ideal. Women go through a lot of different discomforts during those times. Providing information on hormone balancing tips, nutritional values, or even doing mental exercises that would allow that pesky time of the month to go easier and not be an emotional roller coaster.

Bellabeat has a lot of potential to become the world's top smart watch brand for women, empowering them to be the best they can. Bellabeat is not just another smart watch company, they are a guide to a better and healthier lifestyle. It can be a lifestyle choice for women if they are willing to take the leap on this up and coming company.
