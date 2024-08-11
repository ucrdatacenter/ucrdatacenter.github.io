---
layout: page
title: "Data wrangling: filtering rows and selecting columns"
date: "Last updated: 2024-08-11"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

**This tutorial is not ready yet. Please come back later.**

## Introduction

Often you don’t need all the data in your dataset, but only a subset of
it. This can be because you are only interested in a specific subset of
observations or because you only need a subset of variables. In this
tutorial, we will show you how to filter rows and select columns from a
tibble using `tidyverse` functions and the `diamonds` dataset (which
comes pre-loaded with `tidyverse` so you don’t need to import it).

Let’s load the `tidyverse` package and have a look at the `diamonds`
dataset:

``` r
# load tidyverse
library(tidyverse)

# add diamonds to the environment
data(diamonds)
```

## Selecting and renaming variables

Often you don’t need all variables included in your downloaded dataset.
Then you can select the subset of variables you need (or the subset you
would like to remove). The function for doing so is `select()`, and the
arguments of the function are your dataset, followed by the names of the
variables you would like to keep (or remove, if the variable names are
preceded by `-`). The following examples show how to use the function
with and without the pipe operator.

``` r
# keep only variables price and carat
select(diamonds, price, carat)
```

    ## # A tibble: 53,940 × 2
    ##    price carat
    ##    <int> <dbl>
    ##  1   326  0.23
    ##  2   326  0.21
    ##  3   327  0.23
    ##  4   334  0.29
    ##  5   335  0.31
    ##  6   336  0.24
    ##  7   336  0.24
    ##  8   337  0.26
    ##  9   337  0.22
    ## 10   338  0.23
    ## # ℹ 53,930 more rows

``` r
# remove variables price and carat
select(diamonds, -price, -carat)
```

    ## # A tibble: 53,940 × 8
    ##    cut       color clarity depth table     x     y     z
    ##    <ord>     <ord> <ord>   <dbl> <dbl> <dbl> <dbl> <dbl>
    ##  1 Ideal     E     SI2      61.5    55  3.95  3.98  2.43
    ##  2 Premium   E     SI1      59.8    61  3.89  3.84  2.31
    ##  3 Good      E     VS1      56.9    65  4.05  4.07  2.31
    ##  4 Premium   I     VS2      62.4    58  4.2   4.23  2.63
    ##  5 Good      J     SI2      63.3    58  4.34  4.35  2.75
    ##  6 Very Good J     VVS2     62.8    57  3.94  3.96  2.48
    ##  7 Very Good I     VVS1     62.3    57  3.95  3.98  2.47
    ##  8 Very Good H     SI1      61.9    55  4.07  4.11  2.53
    ##  9 Fair      E     VS2      65.1    61  3.87  3.78  2.49
    ## 10 Very Good H     VS1      59.4    61  4     4.05  2.39
    ## # ℹ 53,930 more rows

Especially if you want to combine datasets from different sources, you
may want variable names to be consistent across datasets or convenient
to work with. This problem can be easily fixed using the `rename()`
function, which has the format `rename(data, "new_name" = "old_name")`.
For example, let’s rename the variable `price` to `price_USD` to make it
clear that the price is in US dollars:

``` r
rename(diamonds, price_USD = price)
```

    ## # A tibble: 53,940 × 10
    ##    carat cut       color clarity depth table price_USD     x     y     z
    ##    <dbl> <ord>     <ord> <ord>   <dbl> <dbl>     <int> <dbl> <dbl> <dbl>
    ##  1  0.23 Ideal     E     SI2      61.5    55       326  3.95  3.98  2.43
    ##  2  0.21 Premium   E     SI1      59.8    61       326  3.89  3.84  2.31
    ##  3  0.23 Good      E     VS1      56.9    65       327  4.05  4.07  2.31
    ##  4  0.29 Premium   I     VS2      62.4    58       334  4.2   4.23  2.63
    ##  5  0.31 Good      J     SI2      63.3    58       335  4.34  4.35  2.75
    ##  6  0.24 Very Good J     VVS2     62.8    57       336  3.94  3.96  2.48
    ##  7  0.24 Very Good I     VVS1     62.3    57       336  3.95  3.98  2.47
    ##  8  0.26 Very Good H     SI1      61.9    55       337  4.07  4.11  2.53
    ##  9  0.22 Fair      E     VS2      65.1    61       337  3.87  3.78  2.49
    ## 10  0.23 Very Good H     VS1      59.4    61       338  4     4.05  2.39
    ## # ℹ 53,930 more rows

## Filtering observations

Often you only need a subset of your data, e.g. observations from a
particular location, after a given year, or meeting some other
condition. You can filter your dataset using the `filter()` function and
logical expressions (e.g. keep if the value for the price of the diamond
is greater than \$10,000, or keep if the value for the variable `cut` is
“Ideal”). The first function argument is the name of the dataset, and
the second argument is the logical expression. When defining your
logical expression, you need to use the logical operators: `==` means
equal to, `!=` means not equal to, and `>=,<=,>,<` define numeric
comparisons.

``` r
# keep only diamonds with a price greater than $10,000
filter(diamonds, price > 10000)
```

    ## # A tibble: 5,222 × 10
    ##    carat cut       color clarity depth table price     x     y     z
    ##    <dbl> <ord>     <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
    ##  1  1.7  Ideal     J     VS2      60.5    58 10002  7.73  7.74  4.68
    ##  2  1.03 Ideal     E     VVS2     60.6    59 10003  6.5   6.53  3.95
    ##  3  1.23 Very Good G     VVS2     60.6    55 10004  6.93  7.02  4.23
    ##  4  1.25 Ideal     F     VS2      61.6    55 10006  6.93  6.96  4.28
    ##  5  2.01 Very Good I     SI2      61.4    63 10009  8.19  7.96  4.96
    ##  6  1.21 Very Good F     VS1      62.3    58 10009  6.76  6.85  4.24
    ##  7  1.51 Premium   I     VS2      59.9    60 10010  7.42  7.36  4.43
    ##  8  1.01 Fair      D     SI2      64.6    58 10011  6.25  6.2   4.02
    ##  9  1.05 Ideal     F     VVS2     60.5    55 10011  6.67  6.58  4.01
    ## 10  1.6  Ideal     J     VS1      62      53 10011  7.57  7.56  4.69
    ## # ℹ 5,212 more rows

``` r
# keep only diamonds with cut equal to "Ideal"
filter(diamonds, cut == "Ideal")
```

    ## # A tibble: 21,551 × 10
    ##    carat cut   color clarity depth table price     x     y     z
    ##    <dbl> <ord> <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
    ##  1  0.23 Ideal E     SI2      61.5    55   326  3.95  3.98  2.43
    ##  2  0.23 Ideal J     VS1      62.8    56   340  3.93  3.9   2.46
    ##  3  0.31 Ideal J     SI2      62.2    54   344  4.35  4.37  2.71
    ##  4  0.3  Ideal I     SI2      62      54   348  4.31  4.34  2.68
    ##  5  0.33 Ideal I     SI2      61.8    55   403  4.49  4.51  2.78
    ##  6  0.33 Ideal I     SI2      61.2    56   403  4.49  4.5   2.75
    ##  7  0.33 Ideal J     SI1      61.1    56   403  4.49  4.55  2.76
    ##  8  0.23 Ideal G     VS1      61.9    54   404  3.93  3.95  2.44
    ##  9  0.32 Ideal I     SI1      60.9    55   404  4.45  4.48  2.72
    ## 10  0.3  Ideal I     SI2      61      59   405  4.3   4.33  2.63
    ## # ℹ 21,541 more rows

You can combine multiple logical expressions using the logical operators
`&` (AND) and `|` (OR) in one `filter()` function.

``` r
# keep only diamonds with a price greater than $10,000 and cut equal to "Ideal"
filter(diamonds, price > 10000 & cut == "Ideal")
```

    ## # A tibble: 1,770 × 10
    ##    carat cut   color clarity depth table price     x     y     z
    ##    <dbl> <ord> <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
    ##  1  1.7  Ideal J     VS2      60.5    58 10002  7.73  7.74  4.68
    ##  2  1.03 Ideal E     VVS2     60.6    59 10003  6.5   6.53  3.95
    ##  3  1.25 Ideal F     VS2      61.6    55 10006  6.93  6.96  4.28
    ##  4  1.05 Ideal F     VVS2     60.5    55 10011  6.67  6.58  4.01
    ##  5  1.6  Ideal J     VS1      62      53 10011  7.57  7.56  4.69
    ##  6  1.51 Ideal H     SI1      61.3    56 10012  7.44  7.4   4.55
    ##  7  1.13 Ideal F     VS1      60.9    57 10016  6.73  6.76  4.11
    ##  8  1.04 Ideal E     VVS2     62.9    55 10019  6.47  6.51  4.08
    ##  9  1.22 Ideal G     VVS2     62.3    56 10038  6.81  6.84  4.25
    ## 10  1.3  Ideal G     VS1      62      55 10038  6.98  7.02  4.34
    ## # ℹ 1,760 more rows

The `%in%` operator is useful when you want to filter observations based
on multiple values of a variable, for example, if cut should be either
“Ideal” or “Premium”. Then you concatenate “Ideal” and “Premium” into a
vector and use `%in%` to filter the dataset based on a match with any
element of this vector.

``` r
# keep only diamonds with cut equal to "Ideal" or "Premium"
filter(diamonds, cut %in% c("Ideal", "Premium"))
```

    ## # A tibble: 35,342 × 10
    ##    carat cut     color clarity depth table price     x     y     z
    ##    <dbl> <ord>   <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
    ##  1  0.23 Ideal   E     SI2      61.5    55   326  3.95  3.98  2.43
    ##  2  0.21 Premium E     SI1      59.8    61   326  3.89  3.84  2.31
    ##  3  0.29 Premium I     VS2      62.4    58   334  4.2   4.23  2.63
    ##  4  0.23 Ideal   J     VS1      62.8    56   340  3.93  3.9   2.46
    ##  5  0.22 Premium F     SI1      60.4    61   342  3.88  3.84  2.33
    ##  6  0.31 Ideal   J     SI2      62.2    54   344  4.35  4.37  2.71
    ##  7  0.2  Premium E     SI2      60.2    62   345  3.79  3.75  2.27
    ##  8  0.32 Premium E     I1       60.9    58   345  4.38  4.42  2.68
    ##  9  0.3  Ideal   I     SI2      62      54   348  4.31  4.34  2.68
    ## 10  0.24 Premium I     VS1      62.5    57   355  3.97  3.94  2.47
    ## # ℹ 35,332 more rows

To be able to work with this new dataset, you need to save it to a new
object. Let’s assign the result of the `mutate()` function to a new
object called `diamonds_filtered`:

``` r
# save the result to a new object
diamonds_filtered <- filter(diamonds, cut %in% c("Ideal", "Premium"))
```

## Video tutorial TBA
