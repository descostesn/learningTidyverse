# Learning Tidyverse

These are personal notes from my learning tidyverse. I will try to compare tidyverse commands with regular base R style.

# Introduction

As an introduction, I will follow the chapter 5 of R for data science, https://r4ds.had.co.nz/transform.html.

## Filter

```{r eval = FALSE}
library(nycflights13)
library(tidyverse)

## With tidyverse
filter(flights, month == 1, day == 1)

## Without
flights[which(flights$month == 1 & flights$day == 1),]
```
