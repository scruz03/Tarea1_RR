# Project 1
---
title: "Project1"
author: "Sergio Cruz"
date: "15 de octubre de 2016"
output: html_document
---

## Loading and preprocessing

Download the data and unzip the file

```{r}
library("downloader", lib.loc="~/R/win-library/3.3")
url1 <-"https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download(url1, "activity.zip", mode="wb")
unzip("activity.zip")
```

Read the data

```{r, echo=TRUE}
data <- read.csv("activity.csv", header = TRUE, sep = ",")
head(data)
summary(data)
```

## What is the mean total number of steps taken per day?

### Calculate the number of steps taken per day
### Make a histogram of the total number of steps taken each day

```{r , echo=TRUE}
daily_steps <- aggregate(steps ~ date, data, sum)
hist(daily_steps$steps, main = "Total steps per day", xlab = "Number of steps", col = "red")
```
![alt text] 

### Calculate and report the mean and median of the total number of steps taken per day

```{r , echo=TRUE}
steps_mean <- mean(daily_steps$steps)
paste("The mean is",steps_mean)
steps_median <- median(daily_steps$steps)
paste("The median is",steps_median)
```

## What is the average daily activity pattern?

### Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```{r , echo=TRUE}
daily_interval <- aggregate(steps ~ interval, data, mean)
plot(daily_interval$steps, type = "l", main = "Average daily activity", xlab = "Interval", ylab = "Average number of steps", col = "green")
```


### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```{r , echo=TRUE}
max1 <- daily_interval[which.max(daily_interval$steps),1]
max1
```


## Imputing missing values

### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```{r , echo=TRUE}
NA_qty <- sum(is.na(data$steps))
paste("The number of missing values in the dataset is",NA_qty)
```


### Devise a strategy for filling in all of the missing values in the dataset.

The strategy for filling in all of the missing values in the dataset is to use the mean for that day.

### Create a new dataset that is equal to the original dataset but with the missing data filled in.

```{r , echo=TRUE}
data_filled <- data
data_filled$steps[is.na(data_filled$steps)] <- mean(data$steps,na.rm = TRUE)
```


### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

## Histogram
```{r , echo=TRUE}
daily_steps_2 <- aggregate(steps ~ date, data_filled, sum)
hist(daily_steps_2$steps, main = "Total steps per day filled missing data", xlab = "Number of steps", col = "blue")
```

## Mean and Median
```{r , echo=TRUE}
steps_mean_2 <- mean(daily_steps_2$steps)
paste("The new mean is",steps_mean_2)
steps_median_2 <- median(daily_steps_2$steps)
paste("The new meadian is",steps_median_2)
```

The mean is the same because I did the replacement using the mean. However, the median change because the filling values (mean) affect the calculation.


## Are there differences in activity patterns between weekdays and weekends?

###  Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```{r , echo=TRUE}
library("lubridate", lib.loc="~/R/win-library/3.3")
data$date <- as.Date(data$date)
data$day <- wday(data$date, label=TRUE)
data$wkd <- factor((data$day %in% c("Sat", "Sun")), levels = c(FALSE, TRUE), labels = c("weekday", "weekend"))
```


### Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```{r , echo=TRUE}
daily_steps_3 <- aggregate(steps ~ interval+ wkd, data, mean)
library(ggplot2)
qplot(interval, steps, geom = "line", data = daily_steps_3, color = wkd)+ggtitle(expression("Average steps per interval: Weekdays versus Weekends"))
```

There is a difference in the pattern of activity between weekdays and weekends. During the weekdays there is higher average steps in the range of intervals below 1000. However, during the weekends the activity is in general higher above the interval 1000.
