# Reproducible Research Project-1 by Pawan Gupta

## Loading and preprocessing the data

```r
library(ggplot2)
setwd("~/Desktop/Coursera/RepDataProject")
data <- read.csv("activity.csv")
act_data <- subset(data, !is.na(data$steps))
```

## What is mean total number of steps taken per day?

```r
tot_steps <- tapply(act_data$steps, act_data$date, FUN=sum, na.rm=TRUE)
hist(x=tot_steps,
     col="blue",
     breaks=20,
     xlab="total steps",
     ylab="frequency",
     main="The distribution of daily steps")
```

![](PA1_template_files/figure-html/total_steps_taken-1.png)<!-- -->

```r
mean(tot_steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(tot_steps, na.rm=TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
- Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
avgsteps <- aggregate(x=list(steps=act_data$steps), by=list(interval=act_data$interval),
                      FUN=mean, na.rm=TRUE)
ggplot(data=avgsteps, aes(x=interval, y=steps)) +
    geom_line() + xlab("5-minute interval") + ylab("number of steps taken (average)")
```

![](PA1_template_files/figure-html/time_series_plot-1.png)<!-- -->

- Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
avgsteps[which.max(avgsteps$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.
- Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(act_data$steps))
```

```
## [1] 0
```


- Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
- Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
act_data_imp <- data
ndx <- is.na(act_data_imp$steps)
int_avg <- tapply(act_data$steps, act_data$interval, mean, na.rm=TRUE, simplify=T)
act_data_imp$steps[ndx] <- int_avg[as.character(act_data_imp$interval[ndx])]
```

- Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
imp_tot_steps <- tapply(act_data_imp$steps, act_data_imp$date, sum, na.rm=TRUE, simplify=T)

hist(x=imp_tot_steps,
     col="blue",
     breaks=20,
     xlab="daily steps",
     ylab="frequency",
     main="The distribution of daily steps (with missing data imputed)")
```

![](PA1_template_files/figure-html/total_steps_taken_with_imputed-1.png)<!-- -->

```r
mean(imp_tot_steps)
```

```
## [1] 10766.19
```

```r
median(imp_tot_steps)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?
- Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
act_data_imp$dateType <-  ifelse(as.POSIXlt(act_data_imp$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

- Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:

```r
avg_act_data_imp <- aggregate(steps ~ interval + dateType, data=act_data_imp, mean)
ggplot(avg_act_data_imp, aes(interval, steps)) + 
    geom_line() + 
    facet_grid(dateType ~ .) +
    xlab("5-minute interval") + 
    ylab("avarage number of steps")
```

![](PA1_template_files/figure-html/time_series_with_imputed-1.png)<!-- -->
