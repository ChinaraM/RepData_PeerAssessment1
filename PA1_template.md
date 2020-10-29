

Reproducible Research Course Project 1  
10/29/2020


Loading the data:


```r
library(ggplot2)
activity<-read.csv("activity.csv")
summary(activity)
```

```
##      steps            date              interval     
##  Min.   :  0.00   Length:17568       Min.   :   0.0  
##  1st Qu.:  0.00   Class :character   1st Qu.: 588.8  
##  Median :  0.00   Mode  :character   Median :1177.5  
##  Mean   : 37.38                      Mean   :1177.5  
##  3rd Qu.: 12.00                      3rd Qu.:1766.2  
##  Max.   :806.00                      Max.   :2355.0  
##  NA's   :2304
```

What is mean total number of steps taken per day?


```r
total_steps_perday<-aggregate(steps~date, activity, sum)
hist(total_steps_perday$steps, xlab="Total steps per-day", ylab="Number of days", main="Total number of steps per-day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png)

The mean total number of steps taken per day:

```r
mean(total_steps_perday$steps)
```

```
## [1] 10766.19
```

The median total number of steps taken per day:

```r
median(total_steps_perday$steps)
```

```
## [1] 10765
```

What is the average daily activity pattern?

1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis):

```r
average_steps<-aggregate(steps~interval, activity, mean)
with(average_steps, plot(interval, steps, type = "l", xlab="Interval", ylab="Average number of steps", main="Average number of steps by interval"))
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
average_steps[which.max(average_steps[,2]),1]
```

```
## [1] 835
```

Imputing missing values

1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs):

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```
2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
filled_values<-mean(average_steps$steps)
```
3.Create a new dataset that is equal to the original dataset but with the missing data filled in:

```r
new_activity<-activity
missing_value<-is.na(activity$steps)
new_activity[missing_value,1]<-filled_values
```
4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

1.Histogram of the total number of steps taken each day:

```r
final_total_steps_perday<-aggregate(steps~date, new_activity, sum)
hist(final_total_steps_perday$steps, xlab="Final total number of steps per-day", ylab="Number of days", main="Full number of steps taken per-day")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

2.The mean of the total number of steps taken per-day:

```r
mean(final_total_steps_perday$steps)
```

```
## [1] 10766.19
```
3.The median of the total number of steps taken per-day:

```r
median(final_total_steps_perday$steps)
```

```
## [1] 10766.19
```

Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1.Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day:

```r
new_activity$updated_date <- as.Date(new_activity$date, format = "%Y-%m-%d")
new_activity$weekday <- weekdays(new_activity$updated_date)
new_activity$Days <- ifelse(new_activity$weekday=='Saturday' | new_activity$weekday=='Sunday', 'weekend','weekday')
head(new_activity)
```

```
##     steps       date interval updated_date weekday    Days
## 1 37.3826 2012-10-01        0   2012-10-01  Monday weekday
## 2 37.3826 2012-10-01        5   2012-10-01  Monday weekday
## 3 37.3826 2012-10-01       10   2012-10-01  Monday weekday
## 4 37.3826 2012-10-01       15   2012-10-01  Monday weekday
## 5 37.3826 2012-10-01       20   2012-10-01  Monday weekday
## 6 37.3826 2012-10-01       25   2012-10-01  Monday weekday
```
2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:


```r
activity_by_date <- aggregate(steps~interval+Days, new_activity, mean)
plot<- ggplot(activity_by_date, aes(x = interval , y = steps),labs(title = "Average daily steps by type of date", x = "Interval", y = "Average number of steps"))
print(plot)
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png)

