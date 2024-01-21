---
title: "Data Center Apprenticeship:\nAdvanced treatment of data types and functions in R"
subtitle: "Spring 2024" 
date: "Last updated: 2024-01-21"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [More on data types](#more-on-data-types)
  - [Vectors](#vectors)
    - [Scalars: testing and coercion](#scalars-testing-and-coercion)
    - [Factors: coercion, levels,
      ordering](#factors-coercion-levels-ordering)
    - [Dates: `lubridate`](#dates-lubridate)
    - [Vector attributes](#vector-attributes)
    - [NA, NULL, NaN](#na-null-nan)
  - [Lists](#lists)
  - [Data frames and tibbles](#data-frames-and-tibbles)
- [Writing your own functions](#writing-your-own-functions)
- [Iteration with functionals](#iteration-with-functionals)

``` r
library(tidyverse)
library(lubridate) # for dates
```

# More on data types

Source: Hadley Wickham: [Advanced
R](https://adv-r.hadley.nz/vectors-chap.html) (Chapter 3: Vectors).

## Vectors

### Scalars: testing and coercion

``` r
vectors <- tibble(
  logical = c(TRUE, FALSE, NA),
  integer = as.integer(logical),
  numeric = as.numeric(logical),
  character_log = as.character(logical),
  character_num = as.character(numeric)
)

print(vectors)
```

    ## # A tibble: 3 × 5
    ##   logical integer numeric character_log character_num
    ##   <lgl>     <int>   <dbl> <chr>         <chr>        
    ## 1 TRUE          1       1 TRUE          1            
    ## 2 FALSE         0       0 FALSE         0            
    ## 3 NA           NA      NA <NA>          <NA>

``` r
class(c(vectors$logical, vectors$numeric))
```

    ## [1] "numeric"

``` r
class(c(vectors$logical, vectors$numeric, vectors$character_log, vectors$character_num))
```

    ## [1] "character"

``` r
is.numeric(vectors$logical)
```

    ## [1] FALSE

``` r
is.character(vectors$logical)
```

    ## [1] FALSE

### Factors: coercion, levels, ordering

``` r
factors <- tibble(
  logical = c(TRUE, FALSE, NA),
  factor = as.factor(logical),
  numeric_log = as.numeric(logical),
  numeric_fac = as.numeric(factor)
)

print(factors)
```

    ## # A tibble: 3 × 4
    ##   logical factor numeric_log numeric_fac
    ##   <lgl>   <fct>        <dbl>       <dbl>
    ## 1 TRUE    TRUE             1           2
    ## 2 FALSE   FALSE            0           1
    ## 3 NA      <NA>            NA          NA

``` r
char <- c("x", "x", "x")
factor <- factor(char, levels = c("x", "y"))

as.numeric(char)
```

    ## [1] NA NA NA

``` r
as.numeric(factor)
```

    ## [1] 1 1 1

``` r
table(char)
```

    ## char
    ## x 
    ## 3

``` r
table(factor)
```

    ## factor
    ## x y 
    ## 3 0

``` r
ordered(char, levels = c("x", "y"))
```

    ## [1] x x x
    ## Levels: x < y

### Dates: `lubridate`

``` r
dates <- c(ymd(20201001), dmy("31082022"), Sys.Date(), today())
dates
```

    ## [1] "2020-10-01" "2022-08-31" "2024-01-21" "2024-01-21"

``` r
as_datetime(dates)
```

    ## [1] "2020-10-01 UTC" "2022-08-31 UTC" "2024-01-21 UTC" "2024-01-21 UTC"

``` r
as.numeric(dates)
```

    ## [1] 18536 19235 19743 19743

``` r
ymd(19700101) + days(as.numeric(dates))
```

    ## [1] "2020-10-01" "2022-08-31" "2024-01-21" "2024-01-21"

``` r
now()
```

    ## [1] "2024-01-21 13:39:35 CET"

``` r
date_decimal(c(1990, 1990.1, 1990.5))
```

    ## [1] "1990-01-01 00:00:00 UTC" "1990-02-06 11:59:59 UTC"
    ## [3] "1990-07-02 12:00:00 UTC"

``` r
year(dates)
```

    ## [1] 2020 2022 2024 2024

``` r
quarter(dates)
```

    ## [1] 4 3 1 1

``` r
week(dates)
```

    ## [1] 40 35  3  3

``` r
dates + months(1)
```

    ## [1] "2020-11-01" NA           "2024-02-21" "2024-02-21"

``` r
dates + dmonths(1)
```

    ## [1] "2020-10-31 10:30:00 UTC" "2022-09-30 10:30:00 UTC"
    ## [3] "2024-02-20 10:30:00 UTC" "2024-02-20 10:30:00 UTC"

``` r
floor_date(dates + dmonths(1), unit = "day")
```

    ## [1] "2020-10-31 UTC" "2022-09-30 UTC" "2024-02-20 UTC" "2024-02-20 UTC"

### Vector attributes

``` r
v <- c("a" = 1, "b" = 2, "c" = 3)
attr(v, "other_attribute") <- "x"
attributes(v)
```

    ## $names
    ## [1] "a" "b" "c"
    ## 
    ## $other_attribute
    ## [1] "x"

``` r
names(v) <- c("a1", "b1", "c1")
v <- setNames(v, c("a2", "b2", "c2"))

as.data.frame(v) |> 
  rownames_to_column()
```

    ##   rowname v
    ## 1      a2 1
    ## 2      b2 2
    ## 3      c2 3

``` r
dim(v)
```

    ## NULL

``` r
dim(as.matrix(v))
```

    ## [1] 3 1

### NA, NULL, NaN

``` r
na <- c(NA, NA_integer_, NA_real_, NaN, NULL)
length(na)
```

    ## [1] 4

``` r
is.na(na)
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
is.nan(na)
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
is.null(na)
```

    ## [1] FALSE

``` r
tibble(x = NA)
```

    ## # A tibble: 1 × 1
    ##   x    
    ##   <lgl>
    ## 1 NA

``` r
tibble(x = NaN)
```

    ## # A tibble: 1 × 1
    ##       x
    ##   <dbl>
    ## 1   NaN

``` r
tibble(x = NULL)
```

    ## # A tibble: 0 × 0

## Lists

- List structure
- Referencing elements of a list

## Data frames and tibbles

- Attributes: extracting rownames, colnames, dimensions
- Multiple ways to subset
- Special columns: lists, nested tibbles - unnesting

# Writing your own functions

Source: <https://r4ds.had.co.nz/functions.html>

- view source code of existing functions in console

- why define your own function

- single line or curly braces

- function syntax: function name, returned values, arguments

  - good naming conventions
  - returned values: one or more objects (list return)
  - arguments: names (avoiding `name == name` in function body), default
    values
  - tidy, pipeable functions

- what objects can you use inside and outside the function?

- conditional execution, adding error messages

# Iteration with functionals

Source: <https://adv-r.hadley.nz/functionals.html#map>

- functionals instead of loops - why?

- map and its versions (map2, map_df, map_dbl, pmap, ect.)

- function specification: function name or syntax with `~` and
  `.x`/`.y`(/position/name with `pmap`)

- map in mutate - when is it needed?

- changing multiple variables at once with across
