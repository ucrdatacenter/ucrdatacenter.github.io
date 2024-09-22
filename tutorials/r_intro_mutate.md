---
layout: page
title: "Data wrangling: creating new variables"
date: "Last updated: 2024-09-22"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

## Introduction

In some cases you might need to do additional calculations with your
data. In this tutorial, we show you how to define new variables and
overwrite existing ones using the `mutate()` function from `tidyverse`
functions and the `diamonds` dataset (which comes pre-loaded with
`tidyverse` so you don’t need to import it).

Let’s load the `tidyverse` package and have a look at the `diamonds`
dataset:

``` r
# load tidyverse
library(tidyverse)

# add diamonds to the environment
data(diamonds)
```

## Creating new variables

The `mutate()` function is used to create new variables in a dataset.
The syntax is `mutate(data, variable = expression)`. The `data` argument
is the dataset you want to modify, `variable` is the name of the new
variable, and `expression` is the calculation you want to perform. If
`variable` already exists in the dataset, `mutate()` will overwrite it.

Let’s create a new variable called `price_per_carat` that calculates the
price per carat of each diamond:

``` r
# create a new variable price_per_carat
mutate(diamonds, price_per_carat = price / carat)
```

    ## # A tibble: 53,940 × 11
    ##    carat cut   color clarity depth table price     x     y     z price_per_carat
    ##    <dbl> <ord> <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>           <dbl>
    ##  1  0.23 Ideal E     SI2      61.5    55   326  3.95  3.98  2.43           1417.
    ##  2  0.21 Prem… E     SI1      59.8    61   326  3.89  3.84  2.31           1552.
    ##  3  0.23 Good  E     VS1      56.9    65   327  4.05  4.07  2.31           1422.
    ##  4  0.29 Prem… I     VS2      62.4    58   334  4.2   4.23  2.63           1152.
    ##  5  0.31 Good  J     SI2      63.3    58   335  4.34  4.35  2.75           1081.
    ##  6  0.24 Very… J     VVS2     62.8    57   336  3.94  3.96  2.48           1400 
    ##  7  0.24 Very… I     VVS1     62.3    57   336  3.95  3.98  2.47           1400 
    ##  8  0.26 Very… H     SI1      61.9    55   337  4.07  4.11  2.53           1296.
    ##  9  0.22 Fair  E     VS2      65.1    61   337  3.87  3.78  2.49           1532.
    ## 10  0.23 Very… H     VS1      59.4    61   338  4     4.05  2.39           1470.
    ## # ℹ 53,930 more rows

You can also create multiple variables at once by separating them with a
comma. It is good practice to start each new variable on a new line to
keep your code readable.

Let’s create a second variable: a logical (TRUE/FALSE) variable that
checks if the diamond costs more than \$10,000. In this case, the
expression is a logical condition.

``` r
# create two new variables
mutate(diamonds,
       price_per_carat = price / carat,
       expensive = price > 10000)
```

    ## # A tibble: 53,940 × 12
    ##    carat cut   color clarity depth table price     x     y     z price_per_carat
    ##    <dbl> <ord> <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>           <dbl>
    ##  1  0.23 Ideal E     SI2      61.5    55   326  3.95  3.98  2.43           1417.
    ##  2  0.21 Prem… E     SI1      59.8    61   326  3.89  3.84  2.31           1552.
    ##  3  0.23 Good  E     VS1      56.9    65   327  4.05  4.07  2.31           1422.
    ##  4  0.29 Prem… I     VS2      62.4    58   334  4.2   4.23  2.63           1152.
    ##  5  0.31 Good  J     SI2      63.3    58   335  4.34  4.35  2.75           1081.
    ##  6  0.24 Very… J     VVS2     62.8    57   336  3.94  3.96  2.48           1400 
    ##  7  0.24 Very… I     VVS1     62.3    57   336  3.95  3.98  2.47           1400 
    ##  8  0.26 Very… H     SI1      61.9    55   337  4.07  4.11  2.53           1296.
    ##  9  0.22 Fair  E     VS2      65.1    61   337  3.87  3.78  2.49           1532.
    ## 10  0.23 Very… H     VS1      59.4    61   338  4     4.05  2.39           1470.
    ## # ℹ 53,930 more rows
    ## # ℹ 1 more variable: expensive <lgl>

To be able to work with this new dataset, you need to save it to a new
object. Let’s assign the result of the `mutate()` function to a new
object called `diamonds_new`:

``` r
# save the result to a new object
diamonds_new <- mutate(diamonds,
                        price_per_carat = price / carat,
                        expensive = price > 10000)
```
