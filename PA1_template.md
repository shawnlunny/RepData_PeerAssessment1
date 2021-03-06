---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


# Loading and preprocessing the data
1. Load the data (i.e. read.csv())

```r
unzip ("activity.zip")
activityData <- read.csv(file="activity.csv", header=TRUE, sep=",")
```
Sanity check that the row count is 17568

```r
nrow(activityData)
```

```
## [1] 17568
```

# What is mean total number of steps taken per day?

For this part of the assignment, you can **ignore the missing values** in the dataset.

1.Calculate the total number of steps taken per day

```r
totalSteps <- aggregate(steps ~ date, data=activityData, FUN="sum")
```
2.Make a histogram of the total number of steps taken each day

```r
hist(totalSteps$steps, xlab="Steps Taken", ylim=c(0,30), main="Histogram of Steps Taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

3.Calculate and report the mean and median of the total number of steps taken per day

_**There is no such thing as partial steps so round the mean and median**_


```r
round(mean(totalSteps$steps), 0)
```

```
## [1] 10766
```

```r
round(median(totalSteps$steps), 0)
```

```
## [1] 10765
```
# What is the average daily activity pattern?
1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
totalIntervals <- aggregate(steps ~ interval, data=activityData, FUN="mean")

plot(steps ~ interval, data=totalIntervals, type="l", xlab="Interval", ylab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
totalIntervals$interval[which.max(totalIntervals$steps)]
```

```
## [1] 835
```
# Imputing missing values
1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activityData$steps))
```

```
## [1] 2304
```
2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

**The mean for 5-minute interval was chosen**

3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
filledActivityData <- activityData
matchedIntervalIndex<-match(filledActivityData$interval, totalIntervals$interval)
filledActivityData$steps <- ifelse(is.na(filledActivityData$steps), totalIntervals$steps[matchedIntervalIndex], filledActivityData$steps)
```
4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
filledTotalSteps <- aggregate(steps ~ date, data=filledActivityData, FUN="sum")
hist(filledTotalSteps$steps, xlab="Number of Steps", ylim=c(0,40), main="Histogram of Steps Taken (Missing Step Filled In)")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
round(mean(filledTotalSteps$steps), 0)
```

```
## [1] 10766
```

```r
round(median(filledTotalSteps$steps), 0)
```

```
## [1] 10766
```
**The mean stayed the same and the median increased one step. This makes sense as we used the mean of each 5-minute interval to fill in the missing data. Thus the mean would not change. It also makes sense that with the extra values filled in that the median might shift slightly (1 step in this case).**

# Are there differences in activity patterns between weekdays and weekends?
1.Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
filledActivityData$day <- ifelse(strftime(filledActivityData$date, '%u') < 6, "weekday", "weekend")
```
2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
filledTotalSteps <- aggregate(steps ~ interval + day, data=filledActivityData, mean)
require("lattice")
```

```
## Loading required package: lattice
```

```r
xyplot(steps ~ interval | day, data=filledTotalSteps, type='l',layout=c(1,2), xlab="Interval", ylab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
