# Learning Tidyverse

These are personal notes from learning tidyverse. I will try to compare tidyverse commands with regular base R style.

# Introduction

As an introduction, I will follow the chapter 5 of R for data science, https://r4ds.had.co.nz/transform.html. Some parts here are copied/pasted from the book. The solutions to exercises were verified on https://jrnold.github.io/r4ds-exercise-solutions/transform.html.

## Filter

```{r eval = FALSE}
library(nycflights13)
library(tidyverse)

## Output the flights of the first day of January

## Tidyverse
filter(flights, month == 1, day == 1)

## Using R core
flights[which(flights$month == 1 & flights$day == 1),]
```

Assigning the result to a variable AND printing it:

```{r eval = FALSE}
## Tidyverse
(jan1 <- filter(flights, month == 1, day == 1))

## Assigning result to a variable and printing it
jan1 <- filter(flights, month == 1, day == 1)
print(jan1)
```

Different logical operators can be used if one needs more sophisticated comparisons. & is “and”, | is “or”, and ! is “not”. Check fig. [5.1](https://r4ds.had.co.nz/transform.html). Below are different examples:

```{r eval = FALSE}
#######
## finds all flights that departed in November or December
#######

## Tidyverse
resulttidy <- filter(flights, month == 11 | month == 12)

## Using R core
result <- flights[which(flights$month == 11 | flights$month == 12),]

isTRUE(all.equal(result, resulttidy))
```

filter() only includes rows where the condition is TRUE; it excludes both FALSE and NA values. If you want to preserve missing values, ask for them explicitly:

```{r eval = FALSE}
df <- tibble(x = c(1, NA, 3))
filter(df, x > 1)

filter(df, is.na(x) | x > 1)
```

### Comparison

```{r eval = FALSE}
## Comparison that should be true
> (1/49 * 49) == 1
[1] FALSE

## With R core
> isTRUE(all.equal((1/49 * 49), 1))
[1] TRUE

## Tidyverse
> near(1/49 * 49,1)
[1] TRUE
```

###  Exercises

Find all flights that:

* Had an arrival delay of two or more hours
* Flew to Houston (IAH or HOU)
* Were operated by United, American, or Delta (TIP: If it does not work, check the 'airlines' table)
* Departed in summer (July, August, and September)
* Arrived more than two hours late, but didn’t leave late
* Were delayed by at least an hour, but made up over 30 minutes in flight
* Departed between midnight and 6am (inclusive)
* Another useful dplyr filtering helper is between(). What does it do? Can you use it to simplify the code needed to answer the previous challenges?

* How many flights have a missing dep_time? What other variables are missing? What might these rows represent?

* Why is NA ^ 0 not missing? Why is NA | TRUE not missing? Why is FALSE & NA not missing? Can you figure out the general rule? (NA * 0 is a tricky counterexample!)


### Solutions

```{r eval = FALSE}

## Had an arrival delay of two or more hours

filter(flights, arr_delay >= 120)

## Flew to Houston (IAH or HOU)

res1 <- filter(flights, dest == "IAH" | dest == "HOU")
res2 <- filter(flights, dest %in% c("IAH", "HOU"))
isTRUE(all.equal(res1, res2))

## Were operated by United, American, or Delta

filter(flights, carrier == "United" | carrier == "American" | carrier == "Delta") ## This does not work
res1 <- filter(flights, carrier == "UA" | carrier == "AA" | carrier == "DL")
res2 <- filter(flights, carrier %in% c("UA", "AA", "DL"))
isTRUE(all.equal(res1, res2))

## Departed in summer (July, August, and September)

res1 <- filter(flights, month == 7 | month == 8 | month == 9)
res2 <- filter(flights, month %in% c(7, 8, 9))
res3 <- filter(flights, month >= 7, month <= 9)
res4 <- filter(flights, month %in% 7:9)
isTRUE(all.equal(res1, res2))
isTRUE(all.equal(res1, res3))
isTRUE(all.equal(res1, res4))


## Arrived more than two hours late, but didn’t leave late

filter(flights, dep_delay <= 0, arr_delay > 120)

## Were delayed by at least an hour, but made up over 30 minutes in flight

filter(flights, dep_delay >= 60, dep_delay - arr_delay > 30)

## Departed between midnight and 6am (inclusive)

filter(flights, dep_time <= 600 | dep_time == 2400)

## Another useful dplyr filtering helper is between(). What does it do? Can you use it to simplify the code needed to answer the previous challenges?

The expression between(x, left, right) is equivalent to x >= left & x <= right.

filter(flights, between(month, 7, 9))

## How many flights have a missing dep_time? What other variables are missing? What might these rows represent?

filter(flights, is.na(dep_time))


## Why is NA ^ 0 not missing? Why is NA | TRUE not missing? Why is FALSE & NA not missing? Can you figure out the general rule? (NA * 0 is a tricky counterexample!)

NA ^ 0 == 1 since for all numeric values  x^0 =1

NA | TRUE is TRUE because the value of the missing TRUE or FALSE,  x  or TRUE is TRUE for all values of  x.
Likewise, anything and FALSE is always FALSE.

Because the value of the missing element matters in NA | FALSE and NA & TRUE, these are missing.
```


## Arrange

To sort a table according to difference columns:

```{r eval = FALSE}

## Tidyverse
arrange(flights, year, month, day)

## R base
flightsdf <- as.data.frame(flights)
flightsdf[order(flightsdf[,"year"], flightsdf[,"month"], flightsdf[,"day"]),]
```
Use desc() to re-order by a column in descending order:

```{r eval = FALSE}

## Tidyverse
arrange(flights, desc(dep_delay))

## R base
flightsdf[order(flightsdf[,"dep_delay"], decreasing = TRUE),]
```

Missing values are always sorted at the end (as it is in R base):

```{r eval = FALSE}
## Tidyverse
df <- tibble(x = c(5, NA, 2))
arrange(df, x)
arrange(df, desc(x))

## R base
df2 <- data.frame(x=c(5,NA,2))
df2[order(df2),]
df2[order(df2, decreasing=T),]
```

### Exercises

* How could you use arrange() to sort all missing values to the start? (Hint: use is.na()).
* Sort flights to find the most delayed flights. Find the flights that left earliest.
* Sort flights to find the fastest flights.
* Which flights travelled the longest? Which travelled the shortest?

### Solutions

```{r eval = FALSE}
## How could you use arrange() to sort all missing values to the start? (Hint: use is.na()).
## Tidyverse 
arrange(df, desc(is.na(x)),desc(x))
## R base
df2[order(is.na(df2), df2, decreasing=T),]

## Sort flights to find the most delayed flights. 
arrange(flights, desc(dep_delay))

## Find the flights that left earliest.
arrange(flights, dep_delay)

## Sort flights to find the fastest flights.

arrange(flights, air_time)
or
arrange(flights, distance / air_time * 60)

## Which flights travelled the longest? Which travelled the shortest?
arrange(flights, desc(distance))
arrange(flights, distance)
```


