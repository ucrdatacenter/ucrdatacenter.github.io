---
title: "Data Center Apprenticeship:\nAdvanced treatment of data types and functions in R"
subtitle: "Spring 2024" 
date: "Last updated: 2024-01-22"
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
  - [What does a function look like?](#what-does-a-function-look-like)
  - [Function name, arguments, body, return
    value(s)](#function-name-arguments-body-return-values)
  - [Tidy functions: pipes, data masking, tidy
    evaluation](#tidy-functions-pipes-data-masking-tidy-evaluation)
  - [Conditional execution](#conditional-execution)
  - [What objects exist in which
    environment?](#what-objects-exist-in-which-environment)
- [Iteration with functionals](#iteration-with-functionals)
  - [Why should you use functionals?](#why-should-you-use-functionals)
  - [`map()` and its versions](#map-and-its-versions)
  - [Functionals in tidy data
    wrangling](#functionals-in-tidy-data-wrangling)
  - [Applying methods to multiple variables with
    `across()`](#applying-methods-to-multiple-variables-with-across)

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
# Create a tibble with logical column, coerce it to other types
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
# Logical is coerced to numeric
class(c(vectors$logical, vectors$numeric))
```

    ## [1] "numeric"

``` r
# Logical and numeric are coerced to character
class(c(vectors$logical, vectors$numeric, vectors$character_log, vectors$character_num))
```

    ## [1] "character"

``` r
# Logical is not numeric or character, just coercible to them
is.numeric(vectors$logical)
```

    ## [1] FALSE

``` r
is.character(vectors$logical)
```

    ## [1] FALSE

### Factors: coercion, levels, ordering

``` r
# Logical and factor is numbered differently when coerced to numeric
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
# Define a character vector
char <- c("x", "x", "x")

# Convert character vector to factor with levels x and y
factor <- factor(char, levels = c("x", "y"))

# Only factor can be coerced to numeric, not character
as.numeric(char)
```

    ## [1] NA NA NA

``` r
as.numeric(factor)
```

    ## [1] 1 1 1

``` r
# Table counts all factor levels, even with no observations
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
# Order character vector with levels x < y
ordered(char, levels = c("x", "y"))
```

    ## [1] x x x
    ## Levels: x < y

### Dates: `lubridate`

``` r
# Create a vector of dates
dates <- c(ymd(20201001), dmy("31082022"), Sys.Date(), today())
dates
```

    ## [1] "2020-10-01" "2022-08-31" "2024-01-22" "2024-01-22"

``` r
# Convert dates to datetime
as_datetime(dates)
```

    ## [1] "2020-10-01 UTC" "2022-08-31 UTC" "2024-01-22 UTC" "2024-01-22 UTC"

``` r
# Convert dates to numeric
as.numeric(dates)
```

    ## [1] 18536 19235 19744 19744

``` r
# Reconstruct dates from numeric representation as number of days since base date (01/01/1970)
ymd(19700101) + days(as.numeric(dates))
```

    ## [1] "2020-10-01" "2022-08-31" "2024-01-22" "2024-01-22"

``` r
# Get current date and time
now()
```

    ## [1] "2024-01-22 17:50:51 CET"

``` r
# Convert decimal years to date
date_decimal(c(1990, 1990.1, 1990.5))
```

    ## [1] "1990-01-01 00:00:00 UTC" "1990-02-06 11:59:59 UTC"
    ## [3] "1990-07-02 12:00:00 UTC"

``` r
# Extract year, quarter, and week from dates
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

    ## [1] 40 35  4  4

``` r
# Difference between time and difftime
dates + months(1)
```

    ## [1] "2020-11-01" NA           "2024-02-22" "2024-02-22"

``` r
dates + dmonths(1)
```

    ## [1] "2020-10-31 10:30:00 UTC" "2022-09-30 10:30:00 UTC"
    ## [3] "2024-02-21 10:30:00 UTC" "2024-02-21 10:30:00 UTC"

``` r
# Round down dates to nearest day 
floor_date(dates + dmonths(1), unit = "day")
```

    ## [1] "2020-10-31 UTC" "2022-09-30 UTC" "2024-02-21 UTC" "2024-02-21 UTC"

### Vector attributes

``` r
# Create a named vector
v <- c("a" = 1, "b" = 2, "c" = 3)

# Add an attribute 'other_attribute' to 'v'
attr(v, "other_attribute") <- "x"

# Display attributes of 'v'
attributes(v)
```

    ## $names
    ## [1] "a" "b" "c"
    ## 
    ## $other_attribute
    ## [1] "x"

``` r
# Change names of 'v'
names(v) <- c("a1", "b1", "c1")
v <- setNames(v, c("a2", "b2", "c2"))

# Convert 'v' to a dataframe and add row names as a column
# Note: as_tibble() loses rownames
as.data.frame(v) |> rownames_to_column()
```

    ##   rowname v
    ## 1      a2 1
    ## 2      b2 2
    ## 3      c2 3

``` r
# Get dimensions of 'v'
# vectors always have NULL dimension
dim(v)
```

    ## NULL

``` r
# Get dimensions of 'v' as a matrix 
# matrices have two dimensions (rows x columns)
dim(as.matrix(v))
```

    ## [1] 3 1

### NA, NULL, NaN

``` r
# NA: missing
# NaN: not a number
# NULL: special object of length 0
na <- c(NA, NA_integer_, NA_real_, NaN, NULL)

# NULL does not contribute to vector length/content
length(na)
```

    ## [1] 4

``` r
# NA, NaN are all NA, but only NaN is NaN
is.na(na)
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
is.nan(na)
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
# object is NULL if it only contains NULL
is.null(na)
```

    ## [1] FALSE

``` r
# NA and NaN create an observation
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
# NULL creates tibble with 0 rows, 0 columns
tibble(x = NULL)
```

    ## # A tibble: 0 × 0

## Lists

``` r
# Define list with elements of different types, lengths
l <- list(
  1:3, 
  "a", 
  c(TRUE, FALSE), 
  x = tibble(y = c(2.3, 5.9), z = c(5, 6)),
  l1 = list(l2 = list(1, 2, 3), l3 = list(4, 5))
)

# Access elements of 'l' using different methods
l[1]
```

    ## [[1]]
    ## [1] 1 2 3

``` r
l[[1]]
```

    ## [1] 1 2 3

``` r
l$x
```

    ## # A tibble: 2 × 2
    ##       y     z
    ##   <dbl> <dbl>
    ## 1   2.3     5
    ## 2   5.9     6

``` r
l$x$y
```

    ## [1] 2.3 5.9

``` r
l["l1"]
```

    ## $l1
    ## $l1$l2
    ## $l1$l2[[1]]
    ## [1] 1
    ## 
    ## $l1$l2[[2]]
    ## [1] 2
    ## 
    ## $l1$l2[[3]]
    ## [1] 3
    ## 
    ## 
    ## $l1$l3
    ## $l1$l3[[1]]
    ## [1] 4
    ## 
    ## $l1$l3[[2]]
    ## [1] 5

``` r
l[["l1"]]
```

    ## $l2
    ## $l2[[1]]
    ## [1] 1
    ## 
    ## $l2[[2]]
    ## [1] 2
    ## 
    ## $l2[[3]]
    ## [1] 3
    ## 
    ## 
    ## $l3
    ## $l3[[1]]
    ## [1] 4
    ## 
    ## $l3[[2]]
    ## [1] 5

``` r
l$l1
```

    ## $l2
    ## $l2[[1]]
    ## [1] 1
    ## 
    ## $l2[[2]]
    ## [1] 2
    ## 
    ## $l2[[3]]
    ## [1] 3
    ## 
    ## 
    ## $l3
    ## $l3[[1]]
    ## [1] 4
    ## 
    ## $l3[[2]]
    ## [1] 5

``` r
l$l1$l2
```

    ## [[1]]
    ## [1] 1
    ## 
    ## [[2]]
    ## [1] 2
    ## 
    ## [[3]]
    ## [1] 3

``` r
l$l1[["l2"]]
```

    ## [[1]]
    ## [1] 1
    ## 
    ## [[2]]
    ## [1] 2
    ## 
    ## [[3]]
    ## [1] 3

``` r
l$l1[[1]]
```

    ## [[1]]
    ## [1] 1
    ## 
    ## [[2]]
    ## [1] 2
    ## 
    ## [[3]]
    ## [1] 3

``` r
# convert list to a single vector (character, due to coercion)
unlist(l)
```

    ##                                                    x.y1    x.y2    x.z1    x.z2 
    ##     "1"     "2"     "3"     "a"  "TRUE" "FALSE"   "2.3"   "5.9"     "5"     "6" 
    ##  l1.l21  l1.l22  l1.l23  l1.l31  l1.l32 
    ##     "1"     "2"     "3"     "4"     "5"

## Data frames and tibbles

``` r
# Create a data frame and a tibble with the same contents
df <- data.frame(x = 1:3, y = letters[1:3])
tbl <- tibble(x = 1:3, y = letters[1:3])

# Compare attributes of 'df' and 'tbl'
attributes(df)
```

    ## $names
    ## [1] "x" "y"
    ## 
    ## $class
    ## [1] "data.frame"
    ## 
    ## $row.names
    ## [1] 1 2 3

``` r
attributes(tbl)
```

    ## $class
    ## [1] "tbl_df"     "tbl"        "data.frame"
    ## 
    ## $row.names
    ## [1] 1 2 3
    ## 
    ## $names
    ## [1] "x" "y"

``` r
# Get number of rows and columns of 'df'
nrow(df)
```

    ## [1] 3

``` r
ncol(df)
```

    ## [1] 2

``` r
# Get dimensions of 'df'
dim(df)
```

    ## [1] 3 2

``` r
# Get names of 'df'
names(df)
```

    ## [1] "x" "y"

``` r
# Create a complex tibble 
d <- tibble("1 problematic variable name" = 1:3, 
            y = letters[1:3], 
            z = list(c(1:3), c(4:6), c(7:9)),
            tib = list(tibble(x1 = 1:2, y1 = letters[1:2]),
                       tibble(x1 = 3:4, y1 = letters[3:4]),
                       tibble(x1 = 5:6, y1 = letters[5:6])))

# Select a variable; result: tibble
select(d, y)
```

    ## # A tibble: 3 × 1
    ##   y    
    ##   <chr>
    ## 1 a    
    ## 2 b    
    ## 3 c

``` r
d[ , "y"]
```

    ## # A tibble: 3 × 1
    ##   y    
    ##   <chr>
    ## 1 a    
    ## 2 b    
    ## 3 c

``` r
d[ , 2]
```

    ## # A tibble: 3 × 1
    ##   y    
    ##   <chr>
    ## 1 a    
    ## 2 b    
    ## 3 c

``` r
# Pull a variable; result: vector
pull(d, y)
```

    ## [1] "a" "b" "c"

``` r
d$y
```

    ## [1] "a" "b" "c"

``` r
d[["y"]]
```

    ## [1] "a" "b" "c"

``` r
d[[2]]
```

    ## [1] "a" "b" "c"

``` r
# Use backquotes for problematic names
select(d, `1 problematic variable name`)
```

    ## # A tibble: 3 × 1
    ##   `1 problematic variable name`
    ##                           <int>
    ## 1                             1
    ## 2                             2
    ## 3                             3

``` r
d[ , "1 problematic variable name"]
```

    ## # A tibble: 3 × 1
    ##   `1 problematic variable name`
    ##                           <int>
    ## 1                             1
    ## 2                             2
    ## 3                             3

``` r
d[ , 1]
```

    ## # A tibble: 3 × 1
    ##   `1 problematic variable name`
    ##                           <int>
    ## 1                             1
    ## 2                             2
    ## 3                             3

``` r
pull(d, `1 problematic variable name`)
```

    ## [1] 1 2 3

``` r
d$`1 problematic variable name`
```

    ## [1] 1 2 3

``` r
d[["1 problematic variable name"]]
```

    ## [1] 1 2 3

``` r
d[[1]]
```

    ## [1] 1 2 3

``` r
# Select all variables containing a single lowercase letter
select(d, matches("^[a-z]$"))
```

    ## # A tibble: 3 × 2
    ##   y     z        
    ##   <chr> <list>   
    ## 1 a     <int [3]>
    ## 2 b     <int [3]>
    ## 3 c     <int [3]>

``` r
# Select which rows and columns to keep
d[2:3, c(1, 3)]
```

    ## # A tibble: 2 × 2
    ##   `1 problematic variable name` z        
    ##                           <int> <list>   
    ## 1                             2 <int [3]>
    ## 2                             3 <int [3]>

``` r
# Unnest 'z' and 'tib' from 'd'
d |> unnest(z)
```

    ## # A tibble: 9 × 4
    ##   `1 problematic variable name` y         z tib             
    ##                           <int> <chr> <int> <list>          
    ## 1                             1 a         1 <tibble [2 × 2]>
    ## 2                             1 a         2 <tibble [2 × 2]>
    ## 3                             1 a         3 <tibble [2 × 2]>
    ## 4                             2 b         4 <tibble [2 × 2]>
    ## 5                             2 b         5 <tibble [2 × 2]>
    ## 6                             2 b         6 <tibble [2 × 2]>
    ## 7                             3 c         7 <tibble [2 × 2]>
    ## 8                             3 c         8 <tibble [2 × 2]>
    ## 9                             3 c         9 <tibble [2 × 2]>

``` r
d |> unnest(tib)
```

    ## # A tibble: 6 × 5
    ##   `1 problematic variable name` y     z            x1 y1   
    ##                           <int> <chr> <list>    <int> <chr>
    ## 1                             1 a     <int [3]>     1 a    
    ## 2                             1 a     <int [3]>     2 b    
    ## 3                             2 b     <int [3]>     3 c    
    ## 4                             2 b     <int [3]>     4 d    
    ## 5                             3 c     <int [3]>     5 e    
    ## 6                             3 c     <int [3]>     6 f

``` r
# Default unnest behavior: unnest all list columns (but length mismatch error)
# d |> unnest()
```

# Writing your own functions

Source: Hadley Wickham and Garrett Grolemund: [R for Data
Science](https://r4ds.had.co.nz/functions.html) (Chapter 19: Functions);
Hadley Wickham, Mine Çetinkaya-Rundel, and Garrett Grolemund: [R for
Data Science (2e)](https://r4ds.hadley.nz/functions) (Chapter 25:
Functions)

## What does a function look like?

``` r
# Access function definition
sd
```

    ## function (x, na.rm = FALSE) 
    ## sqrt(var(if (is.vector(x) || is.factor(x)) x else as.double(x), 
    ##     na.rm = na.rm))
    ## <bytecode: 0x00000216249fd5b8>
    ## <environment: namespace:stats>

``` r
# Defining a simple function: rescale a vector so that all elements are between 0 and 1
rescale01 <- function(x) {
  (x - min(x)) / (max(x) - min(x))
}

# Use function defined in global environment
rescale01(x = c(1, 4, 5, 8, 10))
```

    ## [1] 0.0000000 0.3333333 0.4444444 0.7777778 1.0000000

``` r
# For short functions, use one line and omit the braces
rescale01 <- function(x) (x - min(x)) / (max(x) - min(x))
```

## Function name, arguments, body, return value(s)

## Tidy functions: pipes, data masking, tidy evaluation

## Conditional execution

## What objects exist in which environment?

<!-- * view source code of existing functions in console -->
<!-- * why define your own function -->
<!-- * single line or curly braces -->
<!-- * function syntax: function name, returned values, arguments -->
<!--   + good naming conventions -->
<!--   + returned values: one or more objects (list return) -->
<!--   + arguments: names (avoiding `name == name` in function body), default values -->
<!--   + tidy, pipeable functions -->
<!-- * what objects can you use inside and outside the function? -->
<!-- * conditional execution, adding error messages -->

# Iteration with functionals

Hadley Wickham: [Advanced
R](https://adv-r.hadley.nz/functionals.html#map) (Chapter 9:
Functionals).

## Why should you use functionals?

## `map()` and its versions

## Functionals in tidy data wrangling

## Applying methods to multiple variables with `across()`

<!-- * functionals instead of loops - why? -->
<!-- * map and its versions (map2, map_df, map_dbl, pmap, ect.) -->
<!-- * function specification: function name or syntax with `~` and `.x`/`.y`(/position/name with `pmap`) -->
<!-- * map in mutate - when is it needed? -->
<!-- * changing multiple variables at once with across  -->
