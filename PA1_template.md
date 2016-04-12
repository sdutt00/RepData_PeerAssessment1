# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

The data is loaded from the *"activity.csv"* file by using read.csv() into a data frame *aData*. There are 3 variables and 17568 observations. The variables are steps, date and interval. The total number of steps taken per day is calculated by preprocessing the data and stored in the variable *dailySteps*. The variable steps is summed for each date in the dataset. 

```r
aData<-read.csv("activity.csv")
dailySteps<-by(aData$steps, aData$date, sum)
```

## What is mean total number of steps taken per day?

The histogram of the total number of steps taken per day is shown below.

```r
hist(dailySteps, breaks=50, main="Histogram of Total Steps taken per day", xlab="Total Steps per Day")
```

![](PA1_template_files/figure-html/dailysteps_hist-1.png)

```r
mean_ds<-mean(dailySteps, na.rm=T)
median_ds<-median(dailySteps, na.rm=T)
```
The mean total number of steps taken per day is 1.0766189\times 10^{4} and the median value calculated is 10765.

## What is the average daily activity pattern?
The total number of steps taken for every 5 minute interval during the day is calculated in the variable *intervalSteps*. Here is the plot of the average number of steps taken for each 5 minute interval in a day, averaged across all days. Intervals with NA values were ignored in calculating the total steps during each interval.

```r
intervalSteps<-by(aData$steps, aData$interval, function(x) sum(data=x, na.rm=T))
totalDays<-nlevels(aData$date)
plot(x=names(intervalSteps), y=intervalSteps/totalDays, type='l', main="Daily Average Activity Pattern for Steps", xlab="5-min Intervals in a Day", ylab="Average Number of Steps")
```

![](PA1_template_files/figure-html/avg_steps-1.png)

## Imputing missing values
The number of missing values (NA) in the variable steps are calculated here.

```r
sum(is.na(aData$steps))
```

```
## [1] 2304
```
It was decided to impute the missing values for steps by replacing the value for a 5 min interval on a particular day with the average of the 5 min intervals across all days which is available from the variable *intervalSteps*. The index where *steps* is NA is obtained first. Then the *aData$interval* value for where *aData$steps* is NA is found and matched to the index of *intervalSteps*. This means we now have a valid total number of steps value for that interval where *steps* was NA. Next this value is averaged by total number of days, rounded and used to replace the original steps value of NA in a new data frame.

```r
nsteps<-aData$steps # Store steps as it is first into new variable
index<-is.na(aData$steps)==T # Index of all where steps is NA
#Find the index of corresponding interval when steps is NA in the intervalSteps list
#Now take that value from intervalSteps, divide by TotalDays and round it.
nsteps[index]<-round(intervalSteps[match(aData$interval[index],names(intervalSteps))]/totalDays + 0.5)
# new data frame with new values of steps without any NA
nData <- data.frame(steps=nsteps, date=aData$date, interval=aData$interval)
```

## Are there differences in activity patterns between weekdays and weekends?
