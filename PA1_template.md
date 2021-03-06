# Reproducible research : peer assessments 1"

## Load data set


```r
data <- read.csv("activity.csv", as.is=TRUE)
```

## Process data set


```r
# create a subset with NA removed
data.activity <- na.omit(data)
```

## What is mean total number of steps taken per day?

* Make a histogram of the total number of steps taken each day
* Calculate and report the mean and median total number of steps taken per day


```r
totalStepsPerDay <- tapply(data.activity$steps, data.activity$date, sum)
hist(totalStepsPerDay, breaks = 6, main = "Frequency of number of steps per day", 
    xlab = "Number of steps per day", ylab = "Frequency", col = "red")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 


```r
meanStepsPerDay <- tapply(data.activity$steps, data.activity$date, mean, na.rm = T)
```

Mean of total number of steps taken per day:

```r
mean(totalStepsPerDay)
```

```
## [1] 10766.19
```
Median of total number of steps taken per day:


```r
median(totalStepsPerDay)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

* Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
* Which 5-minute interval, on average across all the days in the data.activityset, contains the maximum number of steps?


```r
stepsMeanPerInterval <- tapply(data.activity$steps, data.activity$interval, mean, na.rm = T)
plot(stepsMeanPerInterval, type = "l", main = ("Steps vs. Interval (daily average)"), 
    ylab = "# of steps")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

Interval with the maximum number of steps:


```r
seq(along = stepsMeanPerInterval)[stepsMeanPerInterval == max(stepsMeanPerInterval)]
```

```
## [1] 104
```

## Imputing missing values

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute
* Create a new dataset that is equal to the original dataset but with the missing data filled in.
* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

Number of missing data:

```r
sum(as.numeric(is.na(data$steps)))
```

```
## [1] 2304
```


```r
# Get the steps mean per interval as a vector
tmp_stepsMeanPerInterval <- as.vector(stepsMeanPerInterval)
# Repeat it to be the same for each of the 61 days
tmp_stepsMeanPerInterval <- rep(tmp_stepsMeanPerInterval, 61)
# Set it one where there is no missin data
tmp_stepsMeanPerInterval[!is.na(data$steps)] = 1

# Get the steps data as a vector
tmp_dataTest <- as.vector(data$steps)
# Set it to one where data is missing
tmp_dataTest[is.na(tmp_dataTest)] = 1

data_NoMissing <- data
data_NoMissing$steps <- tmp_stepsMeanPerInterval * tmp_dataTest

totalStepsPerDay_NoMissing <- tapply(data_NoMissing$steps, data_NoMissing$date, 
    sum)
hist(totalStepsPerDay_NoMissing, breaks = 6, main = "Frequency of number of steps per day", 
    xlab = "Number of steps per day", ylab = "Frequency", col = "red")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 

```r
stepsMeanPerInterval_NoMissing <- tapply(data_NoMissing$steps, data_NoMissing$interval, 
    mean)
```
The impact of the missing data on histogram is that the number (i.e. frequency) of data in the middle of histogram has increased since number of new data with the mean has been added.

Mean total number of steps taken per day (missing replaced by mean for that interval):


```r
mean(totalStepsPerDay_NoMissing)
```

```
## [1] 10766.19
```
Median total number of steps taken per day (missing replaced by mean for that interval):


```r
median(totalStepsPerDay_NoMissing)
```

```
## [1] 10766.19
```

```r
plot(stepsMeanPerInterval_NoMissing, type = "l", xlab = "Interval", ylab = "# of Steps", 
    main = "Steps vs. Interval (missing replaced with mean)")
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png) 
## Are there differences in activity patterns between weekdays and weekends?

* Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

* Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```r
# Create a factor variable with two levels (weekday, weekend-day)
tmpLT <- as.POSIXlt(data.activity$date, format = "%Y-%m-%d")
tmpWeekDays <- tmpLT$wday
tmpWeekDays[tmpWeekDays == 0] = 0
tmpWeekDays[tmpWeekDays == 6] = 0
tmpWeekDays[tmpWeekDays != 0] = 1
tmpWeekDaysFactor <- factor(tmpWeekDays, levels = c(0, 1))
# Add the factor variable to the data
data.activity$WD <- tmpWeekDaysFactor
# Calculate the mean
stepsMeanPerWeekday <- tapply(data.activity$steps, list(data.activity$interval, data.activity$WD), mean, 
    na.rm = T)

par(mfrow = c(2, 1))
# Display the 2 plots
with(data.activity, {
    par(mai = c(0, 1, 1, 0))
    plot(stepsMeanPerWeekday[, 1], type = "l", main = ("Steps vs. Interval"), 
        xaxt = "n", ylab = "Week ends")
    title = ("# of Steps v.s. Interval")
    par(mai = c(1, 1, 0, 0))
    plot(stepsMeanPerWeekday[, 2], type = "l", xlab = "Interval", ylab = "Week days")

})
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png) 
