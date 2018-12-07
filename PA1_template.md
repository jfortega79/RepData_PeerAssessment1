# Reproducible Research
## Course Project 1



### 1 - Loading and preprocessing the data

Show any code that is needed to Load the data  
Process/transform the data (if necessary) into a format suitable for your analysis


```r
activity <- read.csv("activity.csv")
```

### 2 - What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.  
Calculate the total number of steps taken per day  
If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day  
  

```r
library(dplyr)
aaa <- summarise(group_by(activity,date),steps = sum(steps))
hist(aaa$steps,main = "Histogram of Steps", xlab = "Steps")
```

![plot of chunk q2.1](figure/q2.1-1.png)

Calculate and report the mean and median of the total number of steps taken per day

```r
mean(aaa$steps,na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(aaa$steps,na.rm = TRUE)
```

```
## [1] 10765
```


### 3 - What is the average daily activity pattern?
Make a time series plot (i.e. \color{red}{\verb|type = "l"|}type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
bbb <- aggregate(activity$steps,by=list(activity$interval),FUN = mean, na.rm = TRUE)
plot(bbb$Group.1,bbb$x,type = "l",main = "Steps per Interval",xlab = "Interval",ylab = "Steps")
```

![plot of chunk q3.1](figure/q3.1-1.png)

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
top <- max(bbb$x)
bbb[bbb$x==top,1]
```

```
## [1] 835
```

### 4 - Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as \color{red}{\verb|NA|}NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with \color{red}{\verb|NA|}NAs)


```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

The strategy will be to input "0".  

Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
vacios <- which(is.na(activity$steps))
ceros <- rep(0,times=2304)
activity[vacios,"steps"] <- ceros
```


Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
aaa <- summarise(group_by(activity,date),steps = sum(steps))
hist(aaa$steps,main = "Histogram of Steps", xlab = "Steps")
```

![plot of chunk q4.3](figure/q4.3-1.png)

```r
mean(aaa$steps,na.rm = TRUE)
```

```
## [1] 9354.23
```

```r
median(aaa$steps,na.rm = TRUE)
```

```
## [1] 10395
```

The values of mean and median have changed. Imputing 0 in the missing data reduced the estimates.

### 5- Are there differences in activity patterns between weekdays and weekends?
For this part the \color{red}{\verb|weekdays()|}weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity$date <- as.POSIXct(activity$date,format = "%Y-%m-%d")
activity <- mutate(activity,dia = weekdays(date))
activity <- mutate(activity,tipodia=ifelse(dia == "sábado"|dia=="domingo","weekend","weekday"))
```


Make a panel plot containing a time series plot (i.e. \color{red}{\verb|type = "l"|}type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
library(lattice)
ccc <- aggregate(activity$steps,by=list(activity$interval,activity$tipodia),FUN = mean, na.rm = TRUE)
xyplot(x~Group.1 | Group.2, ccc,type = "l",layout=c(1,2),xlab="Interval",ylab="Number of Steps")
```

![plot of chunk q5.2](figure/q5.2-1.png)
 
