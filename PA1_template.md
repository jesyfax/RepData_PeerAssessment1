# Reproducible Research: Peer Assessment 1


```r
library(lattice)
```


## Loading and cleaning the data

```r
activity_data <- read.csv("activity.csv")
activity_data$date <- as.Date(activity_data$date,"%Y-%m-%d")
```

## Mean total number of steps taken per day

```r
total_steps_per_day <- tapply(activity_data$steps, activity_data$date,sum)
```
## Histogram of total number of steps per day

```r
hist(total_steps_per_day,col="blue",xlab="Total Steps per Day", 
      ylab="Frequency", main="Histogram of Total Steps taken per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->
Compute Mean total steps taken per day

```r
mean(total_steps_per_day,na.rm=TRUE)
```

```
## [1] 10766.19
```

Compute Median total steps taken per day

```r
median(total_steps_per_day,na.rm=TRUE)
```

```
## [1] 10765
```

## Average daily activity pattern
Compute mean of steps over all days by time interval

```r
mean_steps_by_interval <- tapply(activity_data$steps,activity_data$interval,
                                 mean,na.rm=TRUE)
```
Timeseries plot of of the 5-minute interval and the average number of steps taken, averaged across all days

```r
plot(row.names(mean_steps_by_interval),mean_steps_by_interval,type="l",
     xlab="Time Intervals", 
     ylab="Mean number of steps taken (all Days)", 
     main="Average number of Steps Taken at different 5 minute Intervals",
     col="darkblue")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
Time interval that contains maximum average number of steps over all days

```r
interval_num <- which.max(mean_steps_by_interval)
interval_max_steps <- names(interval_num)
interval_max_steps
```

```
## [1] "835"
```
The  r  interval_max_steps  minute  or ** 104th ** 5 minute interval contains the maximum number of steps on average across all the days


## Inputing missing values
Compute the number of NA values in the activity dataset

```r
num_na_values <- sum(is.na(activity_data))
num_na_values #Print number of NA values
```

```
## [1] 2304
```

Fill in missing values using the **average interval value across all days**

```r
na_indices <-  which(is.na(activity_data))
imputed_values <- mean_steps_by_interval[as.character(activity_data[na_indices,3])]
names(imputed_values) <- na_indices
for (i in na_indices) {
    activity_data$steps[i] = imputed_values[as.character(i)]
}
sum(is.na(activity_data)) # The number of NAs after imptation should be 0
```

```
## [1] 0
```

## Finding differences in activity patterns between weekdays and weekends


```r
days <- weekdays(activity_data$date)
activity_data$day_type <- ifelse(days == "Saturday" | days == "Sunday", 
                                "Weekend", "Weekday")
mean_steps_by_interval <- aggregate(activity_data$steps,
                                    by=list(activity_data$interval,
                                            activity_data$day_type),mean)
names(mean_steps_by_interval) <- c("interval","day_type","steps")
xyplot(steps~interval | day_type, mean_steps_by_interval,type="l",
       layout=c(1,2),xlab="Interval",ylab = "Number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
Mean, median, max and min of the steps across all intervals and days by Weekdays/Weekends

```r
tapply(mean_steps_by_interval$steps,mean_steps_by_interval$day_type,
       function (x) { c(MIN=min(x),MEAN=mean(x),
                        MEDIAN=median(x),MAX=max(x))})
```

```
## $Weekday
##       MIN      MEAN    MEDIAN       MAX 
##   0.00000  35.61058  25.80314 230.37820 
## 
## $Weekend
##       MIN      MEAN    MEDIAN       MAX 
##   0.00000  42.36640  32.33962 166.63915
```
