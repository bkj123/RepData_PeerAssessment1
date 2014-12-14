---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---
Reproducible Research - Peer Assessment 1
========================================================
Johns Hopkins | Coursera   
-------------------------------------------------------
<h3>Brian K. Jones | BKJ123</h3>


<H1>Introduction</H1>
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

<H1>Data</H1>

The data for this assignment can be downloaded from the course web site:

Dataset: Activity monitoring data [52K]
The variables included in this dataset are:

steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)

date: The date on which the measurement was taken in YYYY-MM-DD format

interval: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

<H1>Assignment</H1>

This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a single R markdown document that can be processed by knitr and be transformed into an HTML file.

Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use echo = TRUE so that someone else will be able to read the code. This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis.

For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)

Fork/clone the GitHub repository created for this assignment. You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.

NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.


<H2>Loading and preprocessing the data</H2>

* Show any code that is needed to 

* Load the data (i.e. read.csv())

Process/transform the data (if necessary) into a format suitable for your analysis

Outside of this code, I downloaded and uncompressed the file from https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip in case internet connectivity wasn't available

read in activity.csv and converted date column to date format


```r

setwd("c:/_coursera")
actdata <- read.csv("activity.csv")
actdata$date <- as.Date(actdata$date, format = "%Y-%m-%d")
```


<H2>What is mean total number of steps taken per day?</H2>

* For this part of the assignment, you can ignore the missing values in the dataset.

* Make a histogram of the total number of steps taken each day

* Calculate and report the mean and median total number of steps taken per day

<H3>What is mean total number of steps taken per day?</h3>

I rolled up the step counts by date

```r
actdaysteps <- aggregate(steps ~ date, actdata, sum, na.rm = TRUE)
```


Create histogram of steps for each date

```r
histogram <- barplot(actdaysteps$steps, names.arg = actdaysteps$date, xlab = "total steps per day", 
    main = "Hisogram of Steps Per Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 


Calculate the mean first and then the median of steps per date

```r

actdaystepsmean <- mean(actdaysteps$steps, na.rm = T)
actdaystepsmedian <- median(actdaysteps$steps, na.rm = T)
```


The mean is 1.0766 &times; 10<sup>4</sup> and the median is 10765.

  
<H3>What is the average daily activity pattern?</h3>

* Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

calc the average number of steps

```r
avginterval <- aggregate(steps ~ interval, actdata, mean)
```


plot the interval (5 minute) across the x axis and the average number of steps across all dates

```r
plot(avginterval, type = "l", main = "Avg Step by Interval across Dates", xlab = "Interval", 
    ylab = "Avg Steps")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


Identify the interval with the most steps.


```r
maxintervalsteps <- avginterval$interval[which.max(avginterval$steps)]
```


The interval with the most steps is 835



<H3>Imputing missing values</h3>

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

* Create a new dataset that is equal to the original dataset but with the missing data filled in.

* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

Calculating the total number of rows with missing values in the dataset.

```r
navalues <- sum(is.na(actdata))
```


count of NA values is 2304.
 
* Using the mean to fill the missing value

Use the average number of steps per interval (avginterval$steps) to replace missing values.

```r
actdatanona <- actdata
head(actdatanona)
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

```r
for (i in 1:nrow(actdatanona)) {
    if (is.na(actdatanona$steps[i])) {
        actdatanona$steps[i] <- avginterval[which(actdatanona$interval[i] == 
            avginterval$interval), ]$steps
    }
}
head(actdatanona)
```

```
##     steps       date interval
## 1 1.71698 2012-10-01        0
## 2 0.33962 2012-10-01        5
## 3 0.13208 2012-10-01       10
## 4 0.15094 2012-10-01       15
## 5 0.07547 2012-10-01       20
## 6 2.09434 2012-10-01       25
```



Create histogram for steps by date with imputed data.

```r
actdaystepsnona <- aggregate(steps ~ date, actdatanona, sum, na.rm = TRUE)
histogram <- barplot(actdaystepsnona$steps, names.arg = actdaystepsnona$date, 
    xlab = "total steps per day", main = "Hisogram of Steps Per Day")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 


Calculate the mean first and then the median of steps per date of the imputed data then see how it compares to the original data with the missing values

```r
actdaystepsnonamean <- mean(actdaystepsnona$steps, na.rm = T)
actdaystepsnonamedian <- median(actdaystepsnona$steps, na.rm = T)
```


For the original data: the mean is 1.0766 &times; 10<sup>4</sup> and the median is 10765.
For the imputed data: the mean is 1.0766 &times; 10<sup>4</sup> and the median is 1.0766 &times; 10<sup>4</sup>.
It makes sense that the means are so close while the medians are off a bit as we used the mean to replace the missing values.

<H3>Are there differences in activity patterns between weekdays and weekends?</H3>

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

* Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

* Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

Create a new factor variable called daytype.  Values will be weekday or weekend

```r
actdatanona$daytype <- factor(format(actdatanona$date, "%A"))
table(actdatanona$daytype)
```

```
## 
##    Friday    Monday  Saturday    Sunday  Thursday   Tuesday Wednesday 
##      2592      2592      2304      2304      2592      2592      2592
```

```r
levels(actdatanona$daytype) <- list(weekend = c("Saturday", "Sunday"), weekday = c("Monday", 
    "Tuesday", "Wednesday", "Thursday", "Friday"))
table(actdatanona$daytype)
```

```
## 
## weekend weekday 
##    4608   12960
```


weekday and weekend plot grids below for comparison.

```r
library(ggplot2)
qplot(interval, steps, data = actdatanona, facets = . ~ daytype, geom = "smooth")
```

```
## geom_smooth: method="auto" and size of largest group is >=1000, so using gam with formula: y ~ s(x, bs = "cs"). Use 'method = x' to change the smoothing method.
## geom_smooth: method="auto" and size of largest group is >=1000, so using gam with formula: y ~ s(x, bs = "cs"). Use 'method = x' to change the smoothing method.
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 

