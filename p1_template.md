R Coursera Project
===================
```{r global-options,include=TRUE}
knitr::opts_chunk$set(echo=TRUE)
```


ASSIGNMENT #
To begin with the project,first of all download the zipped data and extract it .
Set the folder containing the extracted data as the working file directory.
Time to load the data into Rstudio.
```{r}
data<-read.csv("activity.csv",na.strings="NA",header=TRUE)
```
converting the dates in the data to class date.
```{r}
data$date<-as.Date(data$date)
```
Processing data to obtain the histogram
```{r}
rdata<-subset(data,!is.na(data$steps))
kith<-with(rdata,tapply(steps,date,sum,na.rm=TRUE))
hist(x=kith,breaks=20,xlab="total steps",col="pink",main="Distribution of total daily steps")
```
To find the mean and media 
```{r}
mean(kith)
median(kith)
```
To construct the time series plot.
first determine the mean of steps taken per interval 

```{r}
avg<-with(rdata,tapply(steps,interval,mean,na.rm=T))
```
plot the graph between intervals and steps taken .

```{r}
num<-as.numeric(names(avg))
plot(num,avg,type="l",xlab="Intervals",ylab="Steps",main="Average Daily activity Pattern")
```


To find the interval having the highest average steps taken.
first create a dataframe having the average steps taken and intervals as its column and then  find the interval having max. average with the following:

```{r}
interval<-as.numeric(names(avg))
df<-data.frame(interval,avg)
max=max(df$avg)
df[df$avg==max,]
```
To impute data:
first of all lets determine the number of missing values in data
```{r}
sum(is.na(data))
```
for the process of imputing .Lets make another object called dataf<-data and an object "na" such that it only contains observations of dataf that have missing steps.And sets it value to the means of the intervals where it has  missing values individually.
```{r}
dataf<-data
nax<-is.na(dataf$steps)
dataf$steps[nax]<-avg[as.character(dataf$interval[nax])]

```
the nax variable has been created so that the code works as intended in the future tasks,as in place of nax ,if we write "is.na(dataf$steps)" it gives a result of numeric (0) instead of the intended newly assigned values.


To create a histogram of the new total daily steps taken.
```{r}
new<-with(dataf,tapply(steps,date,sum,na.rm=T))
hist(x=new,breaks=20,col="pink",xlab="steps",main="Distribution of new data")
```

Finding out mean and median:

```{r}
mean(new)

median(new)
```

creating a new factor variable that has two levels-weekday and weekend.
```{r}
dataf$date<-as.Date(strptime(dataf$date,format="%Y-%m-%d"))
dataf$day<-weekdays(dataf$date)
for(i in 1:nrow(dataf)){
 if(dataf[i,]$day %in% c("Sunday","Saturday")){dataf[i,]$day<-"weekend"}
 else{dataf[i,]$day<-"weekday"}
}
```
COnstructing the plot 
```{r}
stepsByDay <- aggregate(dataf$steps ~ dataf$interval + dataf$day, dataf, mean)
names(stepsByDay) <- c("interval", "day", "steps")
library(lattice)
xyplot(steps ~ interval | day, stepsByDay, type = "l", layout = c(1, 2), 
    xlab = "Interval", ylab = "Number of steps")

```











































