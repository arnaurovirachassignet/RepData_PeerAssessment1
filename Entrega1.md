---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---



## Loading and preprocessing the data

The code used to read and process the data is the following:


```r
activity <- read_csv("Reproducible Research/week2/activity.csv")
data.table::setDT(activity)
activity
```


```
## 
## ── Column specification ────────────────────────────────────────────────────────
## cols(
##   steps = col_double(),
##   date = col_date(format = ""),
##   interval = col_double()
## )
```


## What is mean total number of steps taken per day?

Once the total steps per day are calculated I proceed to generate an histogram to visualize it:

![](Entrega1_files/figure-html/2-1.png)<!-- -->

The mean steps taken per day is:


```
## [1] 9354.23
```

The median steps taken per day is:


```
## [1] 10395
```


## What is the average daily activity pattern?

We can see the average number of steps taken, averaged across all days:

![](Entrega1_files/figure-html/4-1.png)<!-- -->

The 5-minute interal that contains, on average, the maximim number of steps is:


```
## [1] 835
```


## Imputing missing values

There are sme missing values on the data as we can see here:


```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

To impute the data I impute the missing values with the mean of daily activity. Once I have done it the next step is to generate a nwe dataset with the imputed data.



Finally, with the new imputed dataset, we can regenerate the histogram:

![](Entrega1_files/figure-html/7-1.png)<!-- -->


## Are there differences in activity patterns between weekdays and weekends?

Using the function weekdays() I managed to get a new variable indicating either the tay is a weekday or a weekend day.

With that info we can see the differences of the distribution between weekdays & weekend:

![](Entrega1_files/figure-html/8-1.png)<!-- -->


## R Code

The code used to generate all this information is the following:


```r
knitr::opts_chunk$set(echo = TRUE, warning = FALSE, fig.width = 9, fig.height = 4)

library(readr)

activity <- read_csv("Reproducible Research/week2/activity.csv")
data.table::setDT(activity)
activity

activity_clear_days <- activity[,sum(steps,  na.rm = TRUE),by=date]
names(activity_clear_days)[2] <- "steps"

hist(activity_clear_days [,sum(steps),by=date]$V1, main = "Total steps by day", xlab = "Steps")
mean(activity_clear_days$steps)
median(activity_clear_days$steps)

activity_clear_interval <- activity[,mean(steps, na.rm = TRUE),by=interval]
plot(activity_clear_interval, type="l", main="Steps per Interval", ylab = "Avg. Steps")

activity_clear_interval[V1==max(activity_clear_interval$V1)]$interval

sum(is.na(activity$steps))

activity_clear_interval[V1==max(activity_clear_interval$V1)]

pasos_imputados <- activity_clear_interval$V1[match(activity$interval, activity_clear_interval$interval)]
actividad_imputada <- transform(activity, 
                             steps = ifelse(is.na(activity$steps), yes = pasos_imputados, no = activity$steps))
actividad_imputada2 <- actividad_imputada[,mean(steps),by=date]
sum(is.na(actividad_imputada2$dailySteps))

hist(actividad_imputada[,sum(steps),by=date]$V1, main = "Total steps by day", xlab = "Steps")

actividad_imputada[,fecha:=weekdays(date)]

actividad_imputada[,dia:="Weekday"]
actividad_imputada[fecha %in% c("Saturday","Sunday"),dia:="Weekend"]

actividad_imputada_dia <- actividad_imputada[,mean(steps),by=.(interval,dia)]

par(mfrow=c(2,1))
plot(actividad_imputada_dia[dia=="Weekday",c(1,3)], type="l", main = "Weekday distribution", ylab = "Avg. Steps")
plot(actividad_imputada_dia[dia=="Weekend",c(1,3)], type="l", main = "Weekend distribution", ylab = "Avg. Steps")
```
