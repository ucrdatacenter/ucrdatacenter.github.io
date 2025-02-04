---
layout: page
title: "Data wrangling: summarizing data (within groups)"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

## Video tutorial

Please watch [this video
(3:34)](https://vimeo.com/1011768227/2f3d24644f), then read and follow
along with the written tutorial below. Compare your own output to what
you see printed below to make sure all of your code runs as expected.

## Introduction

In some cases you might need to summarize your data – that is, collapse
a lot of information into a few key statistics– to understand it better
and to compare different groups. In this tutorial, we show you how to
summarize data within groups using the `group_by()` and `summarize()`
functions from `tidyverse` functions and the `diamonds` dataset (which
comes pre-loaded with `tidyverse` so you don’t need to import it).

Let’s load the `tidyverse` package and have a look at the `diamonds`
dataset:

``` r
# load tidyverse
library(tidyverse)

# add diamonds to the environment
data(diamonds)
```

## Default summary

The `summary()` function provides a quick overview of the data, printed
in the console. For numeric variables, it shows the minimum, 1st
quartile, median, mean, 3rd quartile, maximum, and the number of missing
values. Let’s use the `summary()` function to get an overview of the
`diamonds` dataset:

``` r
# get a summary of the diamonds dataset
summary(diamonds)
```

    ##      carat               cut        color        clarity          depth           table           price             x         
    ##  Min.   :0.2000   Fair     : 1610   D: 6775   SI1    :13065   Min.   :43.00   Min.   :43.00   Min.   :  326   Min.   : 0.000  
    ##  1st Qu.:0.4000   Good     : 4906   E: 9797   VS2    :12258   1st Qu.:61.00   1st Qu.:56.00   1st Qu.:  950   1st Qu.: 4.710  
    ##  Median :0.7000   Very Good:12082   F: 9542   SI2    : 9194   Median :61.80   Median :57.00   Median : 2401   Median : 5.700  
    ##  Mean   :0.7979   Premium  :13791   G:11292   VS1    : 8171   Mean   :61.75   Mean   :57.46   Mean   : 3933   Mean   : 5.731  
    ##  3rd Qu.:1.0400   Ideal    :21551   H: 8304   VVS2   : 5066   3rd Qu.:62.50   3rd Qu.:59.00   3rd Qu.: 5324   3rd Qu.: 6.540  
    ##  Max.   :5.0100                     I: 5422   VVS1   : 3655   Max.   :79.00   Max.   :95.00   Max.   :18823   Max.   :10.740  
    ##                                     J: 2808   (Other): 2531                                                                   
    ##        y                z         
    ##  Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 4.720   1st Qu.: 2.910  
    ##  Median : 5.710   Median : 3.530  
    ##  Mean   : 5.735   Mean   : 3.539  
    ##  3rd Qu.: 6.540   3rd Qu.: 4.040  
    ##  Max.   :58.900   Max.   :31.800  
    ## 

## Summarizing the data

The `summarize()` function can produce the same summary statistics as
the `summary()` function and more, and it allows you to save the results
to a new dataset. The `summarize()` function uses the argument structure
`summarize(data, variable = expression)` where `data` is the dataset you
want to summarize, `variable` is the name of the new variable, and
`expression` is the calculation you want to perform. (Note that it has
the same argument structure as the `mutate()` function for creating new
variables. The difference is that `mutate()` calculates one value per
observation and `summarize()` calculates one value for the whole
dataset.)

For example, let’s calculate the mean price of diamonds:

``` r
# calculate the mean price of diamonds
summarize(diamonds, mean_price = mean(price))
```

    ## # A tibble: 1 × 1
    ##   mean_price
    ##        <dbl>
    ## 1      3933.

You can also calculate multiple summary statistics at once by separating
them with a comma (and as a good practice, a new line).

Let’s calculate the mean price and the number of observations in the
dataset:

``` r
# calculate the mean price and the number of observations
summarize(diamonds,
          mean_price = mean(price),
          n = n())
```

    ## # A tibble: 1 × 2
    ##   mean_price     n
    ##        <dbl> <int>
    ## 1      3933. 53940

To be able to work with this new dataset, you need to save it to a new
object. Let’s assign the result of the `summarize()` function to a new
object called `diamonds_summary`:

``` r
# save the result to a new object
diamonds_summary <- summarize(diamonds,
                              mean_price = mean(price),
                              n = n())
```

## Summarizing data within groups

The `group_by()` function is used to group the data by one or more
variables. Then summary statistics can be calculated within each group
using the `summarize()` function in the same way as before. This is
useful when you want to compare different groups in your data, for
example the mean price of diamonds by cut.

Note that the following code uses the pipe operator `|>` to chain the
functions together. The pipe operator is used to pass the output of one
function as the first input to the next function, making the code more
readable. To read more about the pipe operator, see the tutorial on the
[tidy workflow](../r_intro_workflow).

``` r
# start with the diamonds tibble
diamonds |> 
  # group by cut
  group_by(cut) |> 
  # calculate the mean price within each group
  summarize(mean_price = mean(price))
```

    ## # A tibble: 5 × 2
    ##   cut       mean_price
    ##   <ord>          <dbl>
    ## 1 Fair           4359.
    ## 2 Good           3929.
    ## 3 Very Good      3982.
    ## 4 Premium        4584.
    ## 5 Ideal          3458.

<!-- ## Video tutorial TBA -->
