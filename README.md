# Learning Tidyverse

These are personal notes from learning tidyverse. I will try to compare tidyverse commands with regular base R style.

# Introduction

As an introduction, I will follow the chapter 5 of R for data science, https://r4ds.had.co.nz/transform.html.

## Filter

```{r eval = FALSE}
library(nycflights13)
library(tidyverse)

## Output the flights of the first day of January
flights[which(flights$month == 1 & flights$day == 1),]

## Tidyverse
filter(flights, month == 1, day == 1)
```

Assigning the result to a variable AND printing it:

```{r eval = FALSE}
## Assigning result to a variable and printing it
jan1 <- filter(flights, month == 1, day == 1)
print(jan1)

## Tidyverse
(jan1 <- filter(flights, month == 1, day == 1))
```

## Comparison

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
