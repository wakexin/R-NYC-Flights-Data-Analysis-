# R-NYC-Flights-Data-Analysis
---
title: "HW2"
author: "Tianze Wang, Kexin Wang"
date: "9/24/2019"
output: rmarkdown::github_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

#0Initialization
Run the next line only once
```{r}
#install.packages("nycflights13")
```
Then always run next block
```{r}
library(nycflights13)
nyc <- nycflights13::flights
```

# Prob1
```{r}
nyc$air_gain = nyc$dep_delay - nyc$arr_delay
```

## 1.a
```{r}
mean(nyc$air_gain, na.rm = TRUE)
median(nyc$air_gain, na.rm = TRUE)
```
Since both mean and median of the air_gain value is bigger than 0, we could say airlines actually gain time on average.

## 1.b
```{r}
tapply(nyc$air_gain, nyc$carrier , mean, na.rm = TRUE)
```

## 1.c
```{r}
barplot(sort(tapply(nyc$air_gain, nyc$carrier , mean, na.rm = TRUE)), las=1, cex.names=.8)
```

# 2
```{r}
good_airport_monthly <- function(x) {
  nyc_temp = nyc[nyc$month == x,]
  grouped_result = tapply(nyc_temp$arr_delay, nyc_temp$origin, mean, na.rm = TRUE)
  return(names(sort(grouped_result)[1]))
}
good_airport_monthly(6)
```


# 3
Here I choose another method instead of the if else statement.
```{r}
morning <- nyc$dep_time >= 400 & nyc$dep_time < 1200
noon <- nyc$dep_time >= 1200 & nyc$dep_time < 1600
evening <- nyc$dep_time >= 1600 & nyc$dep_time < 2000
night <- nyc$dep_time >= 2000 | nyc$dep_time < 400
nyc$time_region = NA
nyc$time_region[morning] = "morning"
nyc$time_region[noon] = "noon"
nyc$time_region[evening] = "evening"
nyc$time_region[night] = "night"
nyc_atl = nyc[nyc$dest == "ATL",]
names(sort(tapply(nyc_atl$arr_delay, nyc_atl$time_region, mean, na.rm = TRUE))[1])
nyc_atl = nyc_atl[complete.cases(nyc_atl),]
nyc$origin = as.factor(nyc$origin)
nyc$time_region = as.factor(nyc$time_region)
```

# 4 

First we observe the distribution within these two columns
```{r}
hist(nyc$air_gain)
lines(density(nyc$air_gain, na.rm = TRUE))
hist(nyc$distance)
```


```{r}
plot(nyc$distance, nyc$air_gain)
scatter.smooth(nyc$distance, nyc$air_gain)
cor(nyc$distance, nyc$air_gain,use="complete.obs")
```

We study the relation with different time zones
```{r}
library(ggplot2)
baseplot <- ggplot(data = nyc, aes(x=distance, y= air_gain, colour = time_region))
baseplot + geom_smooth()
```
Next we study the relation within different destination
```{r}
library(ggplot2)
baseplot <- ggplot(data = nyc, aes(x=distance, y= air_gain, colour = origin))
baseplot + geom_smooth()
```
```{r}
library(ggplot2)
baseplot <- ggplot(data = nyc, aes(x=distance, y= air_gain))
baseplot + geom_smooth()
```
# 4. Conclusion
```{}
From the above figures, we can denote following observations.
1. Generally, the longer the distance, the bigger airgain is. However, airgain usually will not exceed 15 minutes.
2. The airgain for flights travelled with distance less than 2000 have an airgain with bigger sample deviation. 
3. Night flights tend to have negative airgain at long distance, which indicates the flight spend more time on air than estimated. This may due to fatigue of pilots, or constraints on night flights.
```


# 5

```{r}
library(ggplot2)
baseplot <- ggplot(data = nyc, aes(x=distance, y= air_gain, colour = origin))
baseplot + geom_violin()
```
From the violin plot above, we can understand tha EWR and JFK mainly have long distance travels, while LGA mostly has short distance travels.
```{r}
library(ggplot2)
baseplot <- ggplot(data = nyc, aes(x=distance, y= dep_delay, colour = air_gain))
baseplot + stat_density2d(aes(fill=..level..), geom="polygon",bins=12) +   scale_fill_gradient(low="skyblue2", high="firebrick1", name="Distribution")
```

This graph is a density graph showing that, considering both distance and dep delay, which combination will laed to most airgain density. Most airgain density is labeled as red, while low density is labeled as blue.











