bellabeat_project
================
Duy Nguyen
2024-09-18

# Clarifying context, questions for analysis, and business tasks (Ask)

## About company

Bellabeat, a high-tech manufacturer of health-focused products for
women. Bellabeat is a successful small company, but they have the
potential to become a larger player in the global smart device market.
Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes
that analyzing smart device fitness data could help unlock new growth
opportunities for the company

## Questions for the analysis

1.  What are some trends in smart device usage?
2.  How could these trends apply to Bellabeat customers?
3.  How could these trends help influence Bellabeat marketing strategy?

## Business task

Identify potential opportunities for growth and recommendations for the
Bellabeat marketing strategy improvement based on trends in smart device
usage.

# Loading packages

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(lubridate)
library(dplyr)
library(ggplot2)
library(tidyr)
library(lubridate)
```

# Importing datasets (Prepare)

For this project I use [FitBit Fitness Tracker
Data](https://www.kaggle.com/datasets/arashnic/fitbit). This Kaggle data
set contains personal fitness tracker from thirty fitness users. It
includes information about daily activity, steps, and heart rate that
can be used to explore users’ habits.

``` r
daily_activity <- read_csv("dailyActivity_merged.csv")
```

    ## Rows: 940 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): ActivityDate
    ## dbl (14): Id, TotalSteps, TotalDistance, TrackerDistance, LoggedActivitiesDi...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
calories <- read_csv("hourlyCalories_merged.csv")
```

    ## Rows: 22099 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (2): Id, Calories
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
intensities <- read_csv("hourlyIntensities_merged.csv")
```

    ## Rows: 22099 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (3): Id, TotalIntensity, AverageIntensity
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
heart_rate <- read_csv("heartrate_seconds_merged.csv")
```

    ## Rows: 2483658 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Time
    ## dbl (2): Id, Value
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
sleep <- read_csv("sleepDay_merged.csv")
```

    ## Rows: 413 Columns: 5
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): SleepDay
    ## dbl (4): Id, TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

## Checking data set

Take a look at the daily_activity data

``` r
head(daily_activity)
```

    ## # A tibble: 6 × 15
    ##           Id ActivityDate TotalSteps TotalDistance TrackerDistance
    ##        <dbl> <chr>             <dbl>         <dbl>           <dbl>
    ## 1 1503960366 4/12/2016         13162          8.5             8.5 
    ## 2 1503960366 4/13/2016         10735          6.97            6.97
    ## 3 1503960366 4/14/2016         10460          6.74            6.74
    ## 4 1503960366 4/15/2016          9762          6.28            6.28
    ## 5 1503960366 4/16/2016         12669          8.16            8.16
    ## 6 1503960366 4/17/2016          9705          6.48            6.48
    ## # ℹ 10 more variables: LoggedActivitiesDistance <dbl>,
    ## #   VeryActiveDistance <dbl>, ModeratelyActiveDistance <dbl>,
    ## #   LightActiveDistance <dbl>, SedentaryActiveDistance <dbl>,
    ## #   VeryActiveMinutes <dbl>, FairlyActiveMinutes <dbl>,
    ## #   LightlyActiveMinutes <dbl>, SedentaryMinutes <dbl>, Calories <dbl>

Identify all the columns in the daily_activity data

``` r
colnames(daily_activity)
```

    ##  [1] "Id"                       "ActivityDate"            
    ##  [3] "TotalSteps"               "TotalDistance"           
    ##  [5] "TrackerDistance"          "LoggedActivitiesDistance"
    ##  [7] "VeryActiveDistance"       "ModeratelyActiveDistance"
    ##  [9] "LightActiveDistance"      "SedentaryActiveDistance" 
    ## [11] "VeryActiveMinutes"        "FairlyActiveMinutes"     
    ## [13] "LightlyActiveMinutes"     "SedentaryMinutes"        
    ## [15] "Calories"

Take a look at the sleep data

``` r
head(sleep)
```

    ## # A tibble: 6 × 5
    ##           Id SleepDay        TotalSleepRecords TotalMinutesAsleep TotalTimeInBed
    ##        <dbl> <chr>                       <dbl>              <dbl>          <dbl>
    ## 1 1503960366 4/12/2016 12:0…                 1                327            346
    ## 2 1503960366 4/13/2016 12:0…                 2                384            407
    ## 3 1503960366 4/15/2016 12:0…                 1                412            442
    ## 4 1503960366 4/16/2016 12:0…                 2                340            367
    ## 5 1503960366 4/17/2016 12:0…                 1                700            712
    ## 6 1503960366 4/19/2016 12:0…                 1                304            320

Identify all the columns in the sleep data

``` r
colnames(sleep)
```

    ## [1] "Id"                 "SleepDay"           "TotalSleepRecords" 
    ## [4] "TotalMinutesAsleep" "TotalTimeInBed"

I’ve also double checked on Excel to ensure these data set was
completely imported. Before go to Analysis step I need to convert
timestamp to datetime and split to date and time.

# Fixing formating timestamp data (Process)

``` r
##daily_activity
daily_activity$ActivityDate = as.POSIXct(daily_activity$ActivityDate, format="%m/%d/%Y", tz=Sys.timezone())
daily_activity$date <- format(daily_activity$ActivityDate, format="%m/%d/%Y")

##calories
calories$ActivityHour = as.POSIXct(calories$ActivityHour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
calories$date <- format(calories$ActivityHour, format="%m/%d/%Y")
calories$time <- format(calories$ActivityHour, format="%H:%M:%S")

##intensities
intensities$ActivityHour = as.POSIXct(intensities$ActivityHour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
intensities$date <- format(intensities$ActivityHour, format="%m/%d/%Y")
intensities$time <- format(intensities$ActivityHour, format="%H:%M:%S")

##heart_rate
heart_rate$Time = as.POSIXct(heart_rate$Time, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
heart_rate$date <- format(heart_rate$Time, format="%m/%d/%Y")
heart_rate$time <- format(heart_rate$Time, format="%H:%M:%S")

##sleep
sleep$SleepDay = as.POSIXct(sleep$SleepDay, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
sleep$date <- format(sleep$SleepDay, format="%m/%d/%Y")
sleep$time <- format(sleep$SleepDay, format="%H:%M:%S")
```

Now, these data sets are ready to explore and find some interesting
insights!

**Exploring and summarizing data**

``` r
n_distinct(daily_activity$Id)
```

    ## [1] 33

``` r
n_distinct(calories$Id)
```

    ## [1] 33

``` r
n_distinct(intensities$Id)
```

    ## [1] 33

``` r
n_distinct(heart_rate$Id) 
```

    ## [1] 14

``` r
n_distinct(sleep$Id) 
```

    ## [1] 24

There is 33 participants in the daily_activity, calories and intensities
data sets, 24 in the sleep and only 14 in the weight data set. 14
participants might not strong enough to make any recommendations and
conclusions based on this data.

**Summarize data set**

``` r
#summary daily_activity
daily_activity %>% 
  select(TotalSteps,TotalDistance,SedentaryMinutes,Calories) %>% 
  summary()
```

    ##    TotalSteps    TotalDistance    SedentaryMinutes    Calories   
    ##  Min.   :    0   Min.   : 0.000   Min.   :   0.0   Min.   :   0  
    ##  1st Qu.: 3790   1st Qu.: 2.620   1st Qu.: 729.8   1st Qu.:1828  
    ##  Median : 7406   Median : 5.245   Median :1057.5   Median :2134  
    ##  Mean   : 7638   Mean   : 5.490   Mean   : 991.2   Mean   :2304  
    ##  3rd Qu.:10727   3rd Qu.: 7.713   3rd Qu.:1229.5   3rd Qu.:2793  
    ##  Max.   :36019   Max.   :28.030   Max.   :1440.0   Max.   :4900

``` r
#explore num of active minutes per category
daily_activity %>% 
  select(VeryActiveMinutes,FairlyActiveMinutes,LightlyActiveMinutes) %>% 
  summary()
```

    ##  VeryActiveMinutes FairlyActiveMinutes LightlyActiveMinutes
    ##  Min.   :  0.00    Min.   :  0.00      Min.   :  0.0       
    ##  1st Qu.:  0.00    1st Qu.:  0.00      1st Qu.:127.0       
    ##  Median :  4.00    Median :  6.00      Median :199.0       
    ##  Mean   : 21.16    Mean   : 13.56      Mean   :192.8       
    ##  3rd Qu.: 32.00    3rd Qu.: 19.00      3rd Qu.:264.0       
    ##  Max.   :210.00    Max.   :143.00      Max.   :518.0

``` r
#calories
calories %>% 
  select(Calories) %>% 
  summary()
```

    ##     Calories     
    ##  Min.   : 42.00  
    ##  1st Qu.: 63.00  
    ##  Median : 83.00  
    ##  Mean   : 97.39  
    ##  3rd Qu.:108.00  
    ##  Max.   :948.00

``` r
#intensities
intensities %>% 
  select(TotalIntensity) %>% 
  summary()
```

    ##  TotalIntensity  
    ##  Min.   :  0.00  
    ##  1st Qu.:  0.00  
    ##  Median :  3.00  
    ##  Mean   : 12.04  
    ##  3rd Qu.: 16.00  
    ##  Max.   :180.00

``` r
#sleep
sleep %>% 
  select(TotalSleepRecords,TotalMinutesAsleep,TotalTimeInBed) %>% 
  summary()
```

    ##  TotalSleepRecords TotalMinutesAsleep TotalTimeInBed 
    ##  Min.   :1.000     Min.   : 58.0      Min.   : 61.0  
    ##  1st Qu.:1.000     1st Qu.:361.0      1st Qu.:403.0  
    ##  Median :1.000     Median :433.0      Median :463.0  
    ##  Mean   :1.119     Mean   :419.5      Mean   :458.6  
    ##  3rd Qu.:1.000     3rd Qu.:490.0      3rd Qu.:526.0  
    ##  Max.   :3.000     Max.   :796.0      Max.   :961.0

``` r
#heart_rate
heart_rate %>% 
  select(Value) %>% 
  summary()
```

    ##      Value       
    ##  Min.   : 36.00  
    ##  1st Qu.: 63.00  
    ##  Median : 73.00  
    ##  Mean   : 77.33  
    ##  3rd Qu.: 88.00  
    ##  Max.   :203.00

**Some discoveries from this summary**

- Average sleep time per day is 7 hours, and participants seem use phone
  before and after fall as sleep.
- The majority of the participants are lightly active.
- Average sedentary time is 991 minutes, need to reduce by adding
  physical activities.
- Daily energy consumption is not too high, averaging 2300 calories.
  Bella app might need to develop calculate energy feature to help user
  easily control weight.

# Analyzing and Visualization (Analyze)

**Let see the relationship between steps taken in a day and sedentary
minutes**

``` r
ggplot(data = daily_activity, aes(x=TotalSteps, y=SedentaryMinutes)) + geom_point(color='navy') + geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](bellabeat_project_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

This is a low negative correlation between sedentary minutes and steps
which means the more participants walk the less sedentary time they
have.

**Let look more the relationship between steps taken in a day and
calories burn.**

``` r
ggplot(data = daily_activity, aes(x=TotalSteps, y=Calories)) + geom_point(color='navy') + geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](bellabeat_project_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

This is a high positive correlation between steps and calories. We can
easily see the more participants walk the more calories they burn.

**What about TotalMinutesAsleep and TotalTimeInBed?**

``` r
ggplot(data=sleep, aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) + geom_point(color='navy')+
geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](bellabeat_project_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

The relationship between Total Minutes Asleep and Total Time in Bed
looks linear.

**Let’s look at intensities data**

``` r
intensities %>% 
  group_by(time) %>% 
  summarise(avg_intensities = mean(TotalIntensity)) %>% 
  ggplot(mapping=aes(x=time, y=avg_intensities)) + geom_col(fill="navy") +
  theme(axis.text.x=element_text(angle=90)) + labs(title = "Average Total Intensites Vs Time")
```

![](bellabeat_project_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

- This chart shows that participants start their day at 5 am and go to
  bed at 11 pm.
- They are more active between 8 am and 8 pm, from 5 pm to 7 pm they
  seem to have some physical activities.

I would like to know what the participants daily activities are like on
weekdays.

Firstly, I will create and fix the day of the week are out of order.

``` r
#Create day of week
daily_activity$new_day <- mdy(daily_activity$date)
daily_activity$day_of_week <- weekdays(daily_activity$new_day)

#fix the day of the week are out of order
daily_activity$day_of_week <- ordered(daily_activity$day_of_week, levels=c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday","Sunday"))
```

Next, I need to merge two data sets daily_activiy and sleep

``` r
merged_steps_sleep <- merge(daily_activity, sleep, by=c('Id','date'))
```

Let’s see sleep time during the week

``` r
merged_steps_sleep %>% 
  group_by(day_of_week) %>% 
  summarise(avg_sleep = mean(TotalMinutesAsleep)) %>% 
  ggplot(mapping = aes(x=day_of_week, y=avg_sleep)) + geom_col(fill='cadetblue') +
  geom_hline(yintercept = mean(merged_steps_sleep$TotalMinutesAsleep))+
  labs(title = "Average sleep time") +
  annotate(geom="text", x="Saturday", y=430,label="Average sleep time")
```

![](bellabeat_project_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

Participants sleep more on Sunday, I suppose that they want to spend
more time relaxing and recovering energy after the hard working week.

I continue check their steps in weekdays

``` r
merged_steps_sleep %>% 
  group_by(day_of_week) %>% 
  drop_na() %>% 
  summarise(avg_step = mean(TotalSteps)) %>% 
  ggplot(mapping = aes(x=day_of_week, y=avg_step)) + geom_col(fill='#EEAD0E') +
  labs(title = "Average step by day")
```

![](bellabeat_project_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

From Wednesday to Friday, participants walked significantly less than
the other days. I assume that these are the days when they work the
most, they do not walk much and spend more time on outdoor activities on
Saturday, which is evidenced by the highest number of steps.

Finally I want to see the relationship between Total Minutes Asleep and
Sedentary Minutes

``` r
merged_steps_sleep %>% 
  ggplot(aes(x=TotalMinutesAsleep, y=SedentaryMinutes))+
  geom_point(color='#8B4789',position = "jitter")+
  geom_smooth()+
  labs(title = "Total Minutes As Sleep vs Sedentary Minutes")
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](bellabeat_project_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

- This is the negative relationship between sedentary and sleep time.
- If they want to have more quality sleep time they need to reduce
  sedentary time by add more activity in their routine.

# Summarize and recommendation for the business (Share and Act)

Here are some conclusions and recommendations for Bellabeat

**Conclusion:**

- Most users spend significant amount of time being sedentary, mainly
  engaging light activities.
- Users tend to allocate time for sports activities after work in the
  evening
- During the weekdays, they tend to be less active, while on weekends
  the spend the most time walking and resting

**Recommendation:**

- Target audience: Women with office jobs who sit frequently and are
  less physically activities, and who have a need to control their
  weight.
- Solution Bellabeat can offer:
  - Develop virtual coach feature: This allow users to set goals for
    number of steps they need to complete or the number calories they
    need to burn each day. It will suggest the amount of calories and
    steps need daily based on the user’s BMI and their desired weight
    goal.
  - During working hours, from 8 AM to 7 PM, send reminders to users to
    engage in light activities hour.
