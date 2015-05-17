# Reproducible Research: Peer Assessment 1
## Settings

If the localization of your system is different from English, it is required to make the following settings for the correct display of dates on the chart.

```r
Sys.setlocale("LC_TIME", "en_GB")
```

```
## [1] "en_GB"
```

```r
library(ggplot2)
library(lattice)
```
## Loading and preprocessing the data

#### 1. Load the data (i.e. read.csv())

```r
unzip("activity.zip")
adata <- read.csv("activity.csv")
```
#### 2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
adata$date <- as.Date(adata$date)
head(adata)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

## What is mean total number of steps taken per day?

#### 1. Calculate the total number of steps taken per day

```r
daySteps <- tapply(adata$steps, adata$date, sum, na.rm=TRUE)
summary(daySteps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0    6778   10400    9354   12810   21190
```

#### 2. Make a histogram of the total number of steps taken each day

```r
qplot(date, weight = steps, data = adata, geom = "histogram", ylab='Total steps per day', xlab='Dates')
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

#### 3. Calculate and report the mean and median of the total number of steps taken per day
* Mean:

```r
mean(daySteps)
```

```
## [1] 9354.23
```
* Median:

```r
median(daySteps)
```

```
## [1] 10395
```

## What is the average daily activity pattern?

#### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
intSteps <- aggregate(steps ~ interval, adata, mean, na.rm = TRUE)
ggplot(data = intSteps, aes(x = interval, y = steps)) + geom_line() + 
xlab("5-minute interval") + ylab("average number of steps taken") 
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

#### 2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
intSteps[which.max(intSteps$steps),1]
```

```
## [1] 835
```



## Imputing missing values

#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(adata$steps))
```

```
## [1] 2304
```
#### 2. Devise a strategy for filling in all of the missing values in the dataset. 
Use the mean, to replace the missing values.

#### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
fdata <- adata
fdata[is.na(fdata$steps), 1] <- mean(intSteps$steps)
summary(fdata$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.00    0.00    0.00   37.38   37.38  806.00
```

```r
head(fdata)
```

```
##     steps       date interval
## 1 37.3826 2012-10-01        0
## 2 37.3826 2012-10-01        5
## 3 37.3826 2012-10-01       10
## 4 37.3826 2012-10-01       15
## 5 37.3826 2012-10-01       20
## 6 37.3826 2012-10-01       25
```
#### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 

```r
qplot(date, weight = steps, data = fdata, geom = "histogram", ylab='Total steps per day', xlab='Dates')
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 


* Mean:

```r
mean(fdata$steps)
```

```
## [1] 37.3826
```
* Median:

```r
median(fdata$steps)
```

```
## [1] 0
```
## Are there differences in activity patterns between weekdays and weekends?

#### 1.Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
fdata$dayType <- as.factor(ifelse(weekdays(fdata$date) %in% c("Saturday", "Sunday"),  "Weekend", "Weekday"))
head(fdata)
```

```
##     steps       date interval dayType
## 1 37.3826 2012-10-01        0 Weekday
## 2 37.3826 2012-10-01        5 Weekday
## 3 37.3826 2012-10-01       10 Weekday
## 4 37.3826 2012-10-01       15 Weekday
## 5 37.3826 2012-10-01       20 Weekday
## 6 37.3826 2012-10-01       25 Weekday
```

#### 2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
avgFdata <- aggregate(steps ~ interval + dayType, data=fdata, mean)
xyplot(avgFdata$steps ~ avgFdata$interval | avgFdata$dayType, 
       layout = c(1, 2), type = "l", 
       xlab = "Interval", ylab = "Number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-16-1.png) 
