# R-NYC-Flights-Data-Analysis

---
title: "Homework Assignment 1"
author: "Kexin Wang"
date: 'Assigned: Sept 14, 2019, Due Sep 20, 2019 11:59PM'
output:
  html_document:
    toc: yes
  pdf_document:
    toc: yes
---

## HW-1 Instructions

> Our class emphasizes clear communication of data analysis results to non-technical audiences. I expect your HTML output documents to be readable and well formatted. I expect you to put ample comments in your R code to make the code understandable. Along with accuracy of results, I am looking for quality of presentation as well. This homework is due by **11:59PM on Sep 20**.  To complete this assignment, follow these steps:

1. Download the `HW1.Rmd` file from Canvas.

2. Open `HW1.Rmd` in RStudio.

3. Replace the "Your Name Here" text in the `author:` field with names of the students in HW team.

4. Supply your solutions to the homework by editing `HW1.Rmd`.

5. When you have completed the homework and have **checked** that your code both runs in the Console and knits correctly when you click `Knit HTML`, rename the R Markdown file to `HW1_YourNamesHere.Rmd`, and submit on Canvas.  (YourNameHere should be changed to your own names.) You should submit both the RMD file and its html output.

6. Run your code in the Console and Knit HTML frequently to check for errors.

7. You may find it easier to solve a problem by interacting only with the Console at first. 

8. Note - you can insert an R block for writing your R code by either clicking the **insert** button or pressing Control-Alt-I.


## Problem 1: Vectors

Let's first start working with vectors. I am providing you with the following vector:
```{r}
somedata <- c(10.4, 9.8, 5.6, 4,9, 2,4, 3.1, 7.2, 6.4, 8.8, 12.5, 21.7)

```

I am asking you to do the tasks below. For each of these, you should insert a new R code block (use the icone named `Insert` or use thge keyboard shortcut `Control-Alt-I`) and type your code there.

- What is the 5th value in vector `somedata` (Hint: Use Logical Indexing)
```{r}
somedata[5]
```

- Show all values except the 5th value in vector `somedata` (Hint: Use Logical Indexing)
```{r}
somedata[-5]
```


- Calculate how many values there are in the vector `somedata` (Hint: Use `length` function)

```{r}
length(somedata)
```

- Calculate the difference between the maximum value and the minimum value in the vector `somedata` (Hint: use the functions `max` and `min`)
```{r}
max(somedata) - min(somedata)
```


- Show all values in `somedata` more than 10 ((Hint: Use Logical Indexing))
```{r}
somedata[somedata>10]
```


- Check whether the value 7.2 exists in the vector `somedata` or not. Code should output `TRUE` or `FALSE` depending upon whether the value exists or not in the vector. (Hint: Google a little for how to find whether a particular element exists in a vector or not)
```{r}
is.element(7.2, somedata)
```


## Problem 2: Factors

Assume that we have collected address data from several students. We are storing the state of residence information in a vector named `student.state`. I have created the vector for you below.

```{r}
student.state <- c("MI", "IL", "NY", "MI", "NY", "HI", "IL", "MI", "MI", "NY")
```

I am asking you to do the tasks below. Again do them in separate R code blocks.

- Convert the contents of student.state into factors (Hint: use the command `as.factor`)

```{r}
state_level = as.factor(student.state)
```


- Count the number of levels in the factor created above (Hint: use the command: `levels`)
```{r}
which(length(state_level)==1)
```

- Figure out which state(s) appears in the list only once (this is a hard one!) (Hint: there are various ways to approach this problem. I personally prefer starting with the command `table`)
```{r}
tb = table(state_level)
df = as.data.frame(tb)
df$state_level[which(df$Freq == 1)]
# Alternative: df$state_level[c(df$Freq == 1)]
```


## Problem 3: Data frame basics

We will continue working with the nycflights13 dataset we looked at last class. **Please be sure to keep the data file in the same directory as the RMD file.**

First - lets read the data into an object named `nyc`.

```{r}
nyc <- read.csv("nycflightsjan13.csv")
```

I now would like you to answer the following - all in their own separate R code blocks.

### Data Exploration

Let's first do some simple exploration of this data. I would like for you to answer each of the questions below with an **inline R code** integrated into the text and not in a separate R code block.

- How many airlines are there? (Hint: `levels` and `length` can be useful here)  `r length(levels(nyc$carrier))`

- How many flights there were by the airline with code `OO`? (Hint: `nrow` can be useful here along with logical indexing) 
`r nrow(nyc[nyc$carrier == "OO",])` 

- How long is the shortest flight out of any NYC airport? (Hint: `min` can be useful, remember to handle `NA` values) 
`r min( nyc$air_time ,na.rm = TRUE) `

- How many flights where there by United Airlines (code: UA) on Jan 12th 2013? 
`r nrow(nyc[nyc$carrier == "UA" & nyc$day == 12,])`


### Arrival Delay

Lets focus on Arrival Delay.

- What was the average arrival delay for all airports and all airlines combined in Jan 2013? `r mean(nyc$arr_delay, na.rm = TRUE)`

- Whats was the median arrival delay for all airports and all airlines combined in Jan 2013 `r median(nyc$arr_delay, na.rm = TRUE)`

Based on your answers to the two questions above, what can you say about the distribution of arrival delays? Provide your answer in a text paragraph form.

Answer: Normally more than half of the airlines arrive earlier than scheduled. Also, median to be smaller than mean indicates that for those delayed airlines, they will delay more than those arrive earlier.

### Airline Performance

Lets see if all airlines are equally terrible as far as flight arrival delays are concerned. For this question you will have to make sure that airline column is coded as a factor.

- Calculate average arrival delays by airline (Hint: look up the command `tapply`)
```{r}
tapply(nyc$arr_delay, nyc$carrier, mean, na.rm = 1)
arr_delay_per_car = tapply(nyc$arr_delay, nyc$carrier, mean, na.rm = 1)
```



- Draw a Bar Plot of Average Arrival Delays for all the Airlines (Hint: command for making a Bar Plot is simply `barplot`)
```{r}
barplot(tapply(nyc$arr_delay, nyc$carrier, mean, na.rm = 1))
```


- Which airline has the highest average arrival delay? Which airline has the smallest average arrival delay? Are there airlines that actually have negative average delay? Provide answer to this question in a text paragraph using inline R code.

Ans: The airline with highest average arrival delay is `r names(arr_delay_per_car[arr_delay_per_car == max(arr_delay_per_car)])`.
The airline with the smallest average arrival delay is `r names(arr_delay_per_car[arr_delay_per_car == min(arr_delay_per_car)])`. It means that this airlines arrive earlier than expected. 


That's it. Once you are done, make sure everything works and knits well and then you can uplaod the RMD flile and the html output to Canvas.

> Note that I would like you to do this HW in a team of 2 students. Please indicate in your file who all worked together. I only need one submission from a team. Deadline: EoD Fri 09/20.

**Have Fun!**

Sanjeev
