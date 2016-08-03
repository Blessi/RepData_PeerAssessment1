# Repdata Project 1
 
Blessing Ehinmowo 30th July, 2016 
###This is the first project for the **Reproducible Research** course in Coursera's Data Science specialization track. 
The purpose of the project was to answer a series of questions using data collected from a [FitBit](http://en.wikipedia.org/wiki/Fitbit).
#The purpose of this project is to practice: 
* loading and preprocessing data 
* imputing missing values
* interpreting data to answer research questions 

# Data The data for this assignment was downloaded from the course web site: 
* Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K] 

The variables included in this dataset are: 
* **steps**: Number of steps taking in a 5-minute interval (missing values are coded as `NA`) 
* **date**: The date on which the measurement was taken in YYYY-MM-DD format 
* **interval**: Identifier for the 5-minute interval in which measurement was taken The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset. 
# Loading and preprocessing the data 
After downloadingg the data, it is unzipped and loaded as:

data <- read.csv("activity.csv")  
# A brief description of the dataset 

```{r} 
head(activity)
``` 
```{r} 
dim(activity)
``` 

```{r}
 str(activity)
```
```{r} 
summary(activity)
``` 
## To calculate the mean total number of steps taken per day 

Sum steps by day, create Histogram, and calculate mean and median.

 ```{r} 
steps_per_day <- aggregate(steps ~ date, activity, sum) 
hist(steps_per_day$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps") 
``` 

![plot of Fig 1](https://github.com/Blessi/RepData_PeerAssessment1/blob/master/Fig%201.png) 

```{r} 
rmean <- mean(steps_per_day$steps) 
rmean 
rmedian <- median(steps_per_day$steps) 
rmedian 
``` 
The `mean` is 10766.19 and the `median` is 10765. 
## What is the average daily activity pattern? 
* The average steps for each interval for all days is calculated. 
* The Average Number Steps per Day by Interval is plotted. 
* The interval with most average steps is obtained. 

```{r} 
steps_per_interval <- aggregate(steps ~ interval, activity, mean) 
plot(steps_per_interval$interval,steps_per_interval$steps, type="l", xlab="Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")
``` 
![plot of Fig 2](https://github.com/Blessi/RepData_PeerAssessment1/blob/master/Fig%202.png) 

```{r} 
max_interval <- steps_per_interval[which.max(steps_per_interval$steps),1] max_interval
``` 

The answer is 835. 

## Impute missing values. Compare imputed to non-imputed data.
 Using a simple averaging approach ,missing values were imputed by inserting the average for each interval.
 Therefore, if interval 10 was missing on 10-02-2012 for example, the average for that interval for all days (0.1320755), replaced the NA.
 
```{r} 
incomplete <- sum(!complete.cases(activity)) 
imputed_data <- transform(activity, steps = ifelse(is.na(activity$steps),
 steps_per_interval$steps[match(activity$interval,
 steps_per_interval$interval)], activity$steps)) 
``` 
Zeroes were imputed for 10-01-2012 because it was the first day and would have been over 9,000 steps higher than the following day, which had only 126 steps. 
NAs then were assumed to be zeros to fit the rising trend of the data.

```{r}
 imputed_data[as.character(imputed_data$date) == "2012-10-01", 1] <- 0 

``` 
Recount total steps by day and create Histogram. 

```{r} steps_per_day_i <- aggregate(steps ~ date, imputed_data, sum) 

hist(steps_per_day_i$steps, main = paste("Total Steps Each Day"), col="green", xlab="Number of Steps")

#Create Histogram to show difference. 

hist(steps_per_day$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps", add=T) legend("topright", c("Imputed", "non-imputed"), col=c("green", "blue"), lwd=10)```

![plot of Figure 3](https://github.com/Blessi/RepData_PeerAssessment1/blob/master/Fig%203.png)
 

```{r} 
# Calculate new mean and median for imputed data.


rmean.i <- mean(steps_per_day_i$steps) 

rmean.i 

rmedian.i <- median(steps_per_day_i$steps)

rmedian.i

``` 
Calculate difference between imputed and non-imputed data. 

```{r} 
mean_diff <- rmean.i - rmean med_diff <- rmedian.i - rmedian 
``` 
Calculate total difference. 

```{r} 
total_diff <- sum(steps_per_day_i$steps) - sum(steps_per_day$steps) total_diff

``` 
* The imputed data mean is 10589.69 
* The imputed data median is 10766.19 
* The difference between the non-imputed mean and imputed mean is -176.4949 
* The difference between the non-imputed mean and imputed mean is 1.1887 
* The difference between total number of steps between imputed and non-imputed data is 75363. 
Thus, there were 75363 more steps in the imputed data.
 ## Are there differences in activity patterns between weekdays and weekends? 

Created a plot to compare and contrast number of steps between the week and weekend. 
There is a higher peak earlier on weekdays, and more overall activity on weekends. 

```{r} weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday") imputed_data$dow = as.factor(ifelse(is.element(weekdays(as.Date(imputed_data$date)),weekdays), "Weekday", "Weekend")) steps_per_interval_i <- aggregate(steps ~ interval + dow, imputed_data, mean) 
library(lattice) xyplot(steps_per_interval_i$steps ~ steps_per_interval_i$interval|steps_per_interval_i$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l") ``` 
![plot of Fig 4](https://github.com/Blessi/RepData_PeerAssessment1/blob/master/Fig%204.png)