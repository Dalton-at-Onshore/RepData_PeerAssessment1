---
title: "Reproducible Research: Peer Assessment 1"
author: "Dalton Sloan"
date: "Thursday, January 15, 2015"
output: html_document
---


## Introduction
The following markdown file contains all information needed for the first peer assignment associated with the Reproducible Research course needed to completed the Data Science specialization offered by Coursera.

### Background Information
**Introduciton**  
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.
<br>
<br>
**Data**  
The data for this assignment can be downloaded from the course web site:

- *Dataset:* [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]

The variables included in this dataset are:

- *Steps:* Number of steps taking in a 5-minute interval (missing values are coded as NA)

- *Date:* The date on which the measurement was taken in YYYY-MM-DD format

- *Interval:* Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.
<br>
<br>
**Assignment**  
This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a **single R markdown** document that can be processed by **knitr** and be transformed into an HTML file.

Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use ```echo = TRUE``` so that someone else will be able to read the code. **This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis.**

For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)

Fork/clone the [GitHub repository created for this assignment.](http://github.com/rdpeng/RepData_PeerAssessment1) You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.

NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.


## Loading and preprocessing the data
All code runs under the assumption that data was downloaded and placed in your working directory.


```r
stepData <- read.csv("./activity.csv")
```

## What is mean total number of steps taken per day?
First we will summarize the stepData by the total number of steps taken per day.  
*Note: All days steps were not taken will be excluded*  


```r
totalSteps <- aggregate(steps ~ date, stepData, sum)
```

The following is a histogram of the new summarized data for the total number of steps taken each day.


```r
stephist <- hist(totalSteps$steps, main = "Total Steps Per Day", xlab = "Steps", ylab = "Days")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

Using our totalSteps data.table we can find that the average and median number of steps per day. We will also be rounding the average for easier reading. The Code to calculate this is as follows:


```r
round(mean(totalSteps$steps),0)
```

```
## [1] 10766
```

```r
median(totalSteps$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
To answer this question we will first make a new dataset that we can use on a time series plot. We will be taking the average number of steps taken, averaged across all days for this. The code to create our new dataset is as follows.


```r
#create dataset
averageSteps <- aggregate(steps ~ interval, stepData, mean)
#round steps to a whole number
averageSteps <- transform(averageSteps, steps = round(steps,0))
```

Now that we have a workable dataset we will create our time series plot.


```r
plot(averageSteps$interval, averageSteps$steps, type="l", main = "Daily Step Activity", xlab = "Interval", ylab = "Average Steps")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png) 

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
Using our averageSteps dataset along with our Average Steps plot we can find which 5-minute interval has the highest activity across all of the days in our dataset. We will do this in two steps. The first step will be to find which row in our averageSteps dataset has the highest step average. We can find this with the following code.


```r
which.max(averageSteps$steps)
```

```
## [1] 104
```

With this information we can now view the records within our averageSteps dataset that on average, had the highest steps activity in our dataset.


```r
averageSteps[104,]
```

```
##     interval steps
## 104      835   206
```

## Imputing missing values
There was a number of days/intervals where missing values (coded as NA) were excluded in our previous calculations. The presence of missing days may introduce bias into some calculations or summaries of the data.

To find the total number of missing values we can use the following code.


```r
sum(is.na(stepData))
```

```
## [1] 2304
```

The previous code counts the total missing values over the whole dataset. To ensure that we only have missing values in our steps field we will also count only the steps field and compare it to the previous total. We can do this using the following code.


```r
cbind(sum(is.na(stepData)),sum(is.na(stepData$steps)))
```

```
##      [,1] [,2]
## [1,] 2304 2304
```

In the attempt to avoid bias results we will create a new dataset and replace all missing values with the mean of steps from our stepData dataset. The code to do this is as follows.


```r
newStepData <- stepData
newStepData$steps[is.na(newStepData$steps)] <- mean(newStepData$steps, na.rm = TRUE)
```

**Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?**
Now that we have our new dataset with all missing values replaced with the step mean we will summarize the total number of steps per day like we did with our original dataset so we can compare the impact of replacing all missing values.


```r
newTotalSteps <- aggregate(steps ~ date, newStepData, sum)
```

The following is a histogram of our new dataset for the total number of steps taken each day as well as the average and median values. Once again we will be rounding the average and median for ease of viewing.


```r
stephist <- hist(newTotalSteps$steps, main = "Total Steps Per Day", xlab = "Steps", ylab = "Days")
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13-1.png) 


```r
round(mean(newTotalSteps$steps),0)
```

```
## [1] 10766
```

```r
round(median(newTotalSteps$steps),0)
```

```
## [1] 10766
```

We can see that we had almost no variance between our original dataset and new dataset. To make things a little easier the following is the comparison of mean.


```r
cbind(round(mean(totalSteps$steps)),round(mean(newTotalSteps$steps)))
```

```
##       [,1]  [,2]
## [1,] 10766 10766
```

and here is the comparison of median


```r
cbind(median(totalSteps$steps),round(median(newTotalSteps$steps)))
```

```
##       [,1]  [,2]
## [1,] 10765 10766
```

## Are there differences in activity patterns between weekdays and weekends?
For this part we will use the weekdays() function and our newStepData dataset with the filled-in missing values.

In order to use the weekdays() function we need ensure our date column is in Date format.


```r
class(newStepData$date)
```

```
## [1] "factor"
```

Since it is not in the Date format we will now change it and verify the change has been made.


```r
newStepData <- transform(newStepData, date = as.Date(date))
class(newStepData$date)
```

```
## [1] "Date"
```

We will now apply the weekdays() function to add a new weekdays field to our dataset.


```r
newStepData$weekdays <- weekdays(newStepData$date)
```

The following is a quick view of what our new dataset looks like.


```
##     steps       date interval weekdays
## 1 37.3826 2012-10-01        0   Monday
## 2 37.3826 2012-10-01        5   Monday
## 3 37.3826 2012-10-01       10   Monday
## 4 37.3826 2012-10-01       15   Monday
## 5 37.3826 2012-10-01       20   Monday
## 6 37.3826 2012-10-01       25   Monday
```

We will now replace the days of the week with one of two variables, "weekday" or "weekend". We can do this with the following code.


```r
#Set values for the weekend
newStepData$weekdays[(newStepData$weekdays == "Saturday" | newStepData$weekdays == "Sunday")] <- "weekend"
#Set all other values to weekday
newStepData$weekdays[!(newStepData$weekdays == "weekend")] <- "weekday"
```

The following is a view of what our dataset now looks like.


```r
head(newStepData)
```

```
##     steps       date interval weekdays
## 1 37.3826 2012-10-01        0  weekday
## 2 37.3826 2012-10-01        5  weekday
## 3 37.3826 2012-10-01       10  weekday
## 4 37.3826 2012-10-01       15  weekday
## 5 37.3826 2012-10-01       20  weekday
## 6 37.3826 2012-10-01       25  weekday
```

We can now see a comparison of steps taken on weekdays and steps taken on weekends. The following is a time series plot showing this comparison.


```r
library(lattice)
xyplot(steps ~ interval | weekdays, data = newStepData, type = "l", xlab = "Interval", ylab = "Number of Steps", layout = c(1,2))
```

![plot of chunk unnamed-chunk-23](figure/unnamed-chunk-23-1.png) 
