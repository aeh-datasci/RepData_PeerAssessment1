# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
activity=read.csv("activity.csv")
```

## Distribution of total number of steps taken per day


```r
hist(activity$steps, main="Distribution of total number of steps per day", xlab="Total number of steps per day")
```

![plot of chunk histogram](figure/histogram.png) 

## What is mean total number of steps taken per day?


```r
meansteps=mean(activity$steps, na.rm=TRUE)
mediansteps=median(activity$steps, na.rm=TRUE)
```
Mean number of steps: 37.3826

Median number of steps: 0

## What is the average daily activity pattern?


```r
means=aggregate(activity$steps~activity$interval, activity, mean)
plot(means[,1], means[,2], type="l", ylab="Average number of steps", xlab="Time of day")
```

![plot of chunk plot](figure/plot.png) 
## Imputing missing values


```r
totmsngsteps=sum(is.na(activity$steps))
totmsngintv=sum(is.na(activity$interval))
totmsngdate=sum(is.na(activity$date))
```
The number of missing values in the step variable is 2304.
The number of missing values in the interval variable is 0.
The number of missing values in the date variable is 0.


```r
mergeMeans=merge(activity,means,by.x="interval",by.y="activity$interval")
mergeMeans$steps[is.na(mergeMeans$steps)] = mergeMeans[is.na(mergeMeans$steps),4]

hist(mergeMeans$steps, main="Distribution of total number of steps per day with missing values imputed", xlab="Total number of steps")
```

![plot of chunk imputevalues](figure/imputevalues.png) 

```r
meansteps=mean(mergeMeans$steps)
mediansteps=median(mergeMeans$steps)
```

Mean number of steps with missing values imputed: 37.3826

Median number of steps with missing values imputed : 0

## Are there differences in activity patterns between weekdays and weekends?


```r
daysweek=weekdays(as.Date(mergeMeans$date))
weekend=c("Saturday","Sunday")
weekenddata=daysweek %in% weekend
datadays=cbind(mergeMeans,daysweek,weekenddata)
weekendfactor=factor(datadays$weekenddata, labels=c("Weekday","Weekend"))
datadays2=cbind(datadays,weekendfactor)

means2=aggregate(datadays2$steps~datadays2$interval+datadays2$weekendfactor, datadays2, mean)

library(ggplot2)
names(means2) = c("interval","weekendfactor","steps")
qplot(interval,steps,data=means2,facets=.~weekendfactor, geom="line", xlab="Time of day", ylab="Average number of steps")
```

![plot of chunk plotweekend](figure/plotweekend.png) 

