# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
## Unzip first.
## unzip("activity.zip")
activity <- read.csv("activity.csv")

## Check info
colnames(activity)
```

```
## [1] "steps"    "date"     "interval"
```

```r
dim(activity)
```

```
## [1] 17568     3
```

## What is mean total number of steps taken per day?

1(2). Make a histogram of the total number of steps taken each day


```r
## Calculate the total number of steps taken per day
steps_of_day <- aggregate(steps ~ date,activity,sum)
barplot(height = steps_of_day$steps, names.arg = steps_of_day$date, 
        xlab="Date", ylab="Steps in per day", 
        main="Total number of steps taken per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

3. Calculate and report the mean and median of the total number of steps taken per day


```r
mean(steps_of_day$steps)
```

```
## [1] 10766.19
```

```r
median(steps_of_day$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1(2). Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
library(lattice)
## Calculate the total number of steps taken every 5-minute interval
steps_of_interval <- aggregate(steps ~ interval,activity,mean)
xyplot(steps ~ interval, steps_of_interval, type="l", 
       xlab="Every 5-minute interval", ylab="Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 


3. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max_location <- which.max(steps_of_interval$steps)
steps_of_interval$interval[max_location]
```

```
## [1] 835
```


## Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)

```r
## colSum(is.na(activity))
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```
I will use the mean for that 5-minute interval for filling all the missing values. 
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
 

```r
## Merge the mean interval to original dataset
new_activity <- merge(activity, steps_of_interval,  
                      by="interval" , suffixes=c("", "_avg"))
steps_na <- is.na(new_activity$steps)
## Substitute the NAs with the mean interval
new_activity$steps[steps_na] <- new_activity$steps_avg[steps_na]
new_activity <- new_activity[, 1:3]
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

- Make a histogram of the total number of steps taken each day 

```r
newsteps_of_day <- aggregate(steps ~ date, new_activity, sum)
barplot(height = newsteps_of_day$steps, names.arg = newsteps_of_day$date, 
        xlab="Date", ylab="Steps in per day", 
        main="New Total number of steps taken per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

- Calculate and report the mean and median of the total number of steps taken per day


```r
mean(newsteps_of_day$steps)
```

```
## [1] 10766.19
```

```r
median(newsteps_of_day$steps)
```

```
## [1] 10766.19
```

- Do these values differ from the estimates from the first part of the assignment?
```
The median value is differ from the first part of the assignment.
```

- What is the impact of imputing missing data on the estimates of the total daily number of steps?
```
The median value is bigger, because the NAs is filled with the mean for that 5-minute interval. It's much more logical.
```

## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
## Caculate weekdays
date_weekday <-weekdays(as.Date(new_activity$date))
## Query weather the day is weekday
weekend_flags <- date_weekday %in% c("Saturday", "Sunday")
date_weekday[weekend_flags] <- "weekend"
date_weekday[!weekend_flags] <- "weekday"
new_activity$type <- as.factor(date_weekday)
```


2. Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
## Calculate the total number of steps taken per day of weekdays
steps_of_type <- aggregate(steps ~ interval + type, new_activity, mean)
## type is conditioning variables
xyplot(steps ~ interval | factor(type), data = steps_of_type, 
       layout = c(1, 2),  type="l", ylab = "Mean numbers if steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 
