---
title: "Data Center Apprenticeship:\nAdvanced treatment of data types and functions in R"
subtitle: "Spring 2024" 
date: "Last updated: 2024-01-23"
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
  - [Tidy functions: pipes, tidy
    evaluation](#tidy-functions-pipes-tidy-evaluation)
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
  - [Filtering with `if_any` and
    `if_all`](#filtering-with-if_any-and-if_all)

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

    ## [1] "2020-10-01" "2022-08-31" "2024-01-23" "2024-01-23"

``` r
# Convert dates to datetime
as_datetime(dates)
```

    ## [1] "2020-10-01 UTC" "2022-08-31 UTC" "2024-01-23 UTC" "2024-01-23 UTC"

``` r
# Convert dates to numeric
as.numeric(dates)
```

    ## [1] 18536 19235 19745 19745

``` r
# Reconstruct dates from numeric representation as number of days since base date (01/01/1970)
ymd(19700101) + days(as.numeric(dates))
```

    ## [1] "2020-10-01" "2022-08-31" "2024-01-23" "2024-01-23"

``` r
# Get current date and time
now()
```

    ## [1] "2024-01-23 17:27:14 CET"

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

    ## [1] "2020-11-01" NA           "2024-02-23" "2024-02-23"

``` r
dates + dmonths(1)
```

    ## [1] "2020-10-31 10:30:00 UTC" "2022-09-30 10:30:00 UTC"
    ## [3] "2024-02-22 10:30:00 UTC" "2024-02-22 10:30:00 UTC"

``` r
# Round down dates to nearest day 
floor_date(dates + dmonths(1), unit = "day")
```

    ## [1] "2020-10-31 UTC" "2022-09-30 UTC" "2024-02-22 UTC" "2024-02-22 UTC"

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
    ## <bytecode: 0x000001ffd66244d0>
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

Function naming:

- informative, with verbs
- if using multiple related functions, use common prefix (see
  e.g. `str_...` family)
- don’t override built-in functions

``` r
# Arguments with default values
rescale01 <- function(x = 1:10) {
  (x - min(x)) / (max(x) - min(x))
}

rescale01()
```

    ##  [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
    ##  [8] 0.7777778 0.8888889 1.0000000

``` r
rescale01(1:10)
```

    ##  [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
    ##  [8] 0.7777778 0.8888889 1.0000000

``` r
rescale01(x = 1:10)
```

    ##  [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
    ##  [8] 0.7777778 0.8888889 1.0000000

``` r
# ... argument
commas <- function(...) paste(..., collapse = ", ")
commas(letters[1:10])
```

    ## [1] "a, b, c, d, e, f, g, h, i, j"

``` r
# Unexpected results in logical statements
names <- tibble(id = 1:3, name = LETTERS[1:3])

filter_name <- function(data, name) filter(data, name == name)
filter_name(names, name = "B")
```

    ## # A tibble: 3 × 2
    ##      id name 
    ##   <int> <chr>
    ## 1     1 A    
    ## 2     2 B    
    ## 3     3 C

``` r
filter_name <- function(data, n) filter(data, name == n)
filter_name(names, n = "B")
```

    ## # A tibble: 1 × 2
    ##      id name 
    ##   <int> <chr>
    ## 1     2 B

``` r
# Return values with and without return statement
rescale01 <- function(x) {
  (x - min(x)) / (max(x) - min(x))
}

rescale01 <- function(x) {
  result <- (x - min(x)) / (max(x) - min(x))
  result
}

rescale01 <- function(x) {
  result <- (x - min(x)) / (max(x) - min(x))
  return(result)
}

# access result only if assigned to an object
rescale01 <- function(x) {
  result <- (x - min(x)) / (max(x) - min(x))
}

# List returns
rescale01 <- function(x) {
  min <- min(x)
  max <- max(x)
  result <- (x - min) / (max - min)
  
  # returned list
  list(x = result, original_minimum = min, original_maximum = max)
}

# Access elements of list return
rescale01(1:10)$original_minimum
```

    ## [1] 1

``` r
# better method for computation efficiency
res <- rescale01(1:10)
res$x
```

    ##  [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
    ##  [8] 0.7777778 0.8888889 1.0000000

``` r
# Invisible returns: return object without displaying it
show_missings <- function(df) {
  n <- sum(is.na(df))
  cat("Missing values: ", n, "\n", sep = "")
  
  invisible(df)
}

tibble(x = rep(c(1, 2, NA), times = 4),
       y = letters[1:12]) |> 
  show_missings() |> 
  drop_na() |> 
  show_missings()
```

    ## Missing values: 4
    ## Missing values: 0

## Tidy functions: pipes, tidy evaluation

``` r
## Mutate functions
# input: one vector; output: one vector (same length)
z_score <- function(x) {
  (x - mean(x, na.rm = TRUE)) / sd(x, na.rm = TRUE)
}

first_upper <- function(x) {
  str_sub(x, 1, 1) <- str_to_upper(str_sub(x, 1, 1))
  x
}

tibble(x = 1:10,
       y = paste0("test", x)) |> 
  mutate(z_score = z_score(x),
         y_upper = first_upper(y))
```

    ## # A tibble: 10 × 4
    ##        x y      z_score y_upper
    ##    <int> <chr>    <dbl> <chr>  
    ##  1     1 test1   -1.49  Test1  
    ##  2     2 test2   -1.16  Test2  
    ##  3     3 test3   -0.826 Test3  
    ##  4     4 test4   -0.495 Test4  
    ##  5     5 test5   -0.165 Test5  
    ##  6     6 test6    0.165 Test6  
    ##  7     7 test7    0.495 Test7  
    ##  8     8 test8    0.826 Test8  
    ##  9     9 test9    1.16  Test9  
    ## 10    10 test10   1.49  Test10

``` r
## Summarize functions
# input: one vector; output: one value
n_missing <- function(x) {
  sum(is.na(x))
} 

commas <- function(x) {
  str_flatten(x, collapse = ", ", last = " and ")
}

tibble(x = rep(c(1, 2, NA), times = 4),
       y = letters[1:12]) |> 
  summarize(x_missing = n_missing(x),
            y = commas(y))
```

    ## # A tibble: 1 × 2
    ##   x_missing y                                    
    ##       <int> <chr>                                
    ## 1         4 a, b, c, d, e, f, g, h, i, j, k and l

``` r
tibble(x = rep(c(1, 2, NA), times = 4),
       y = letters[1:12],
       group = rep(1:2, each = 6)) |> 
  group_by(group) |> 
  summarize(x_missing = n_missing(x),
            y = commas(y))
```

    ## # A tibble: 2 × 3
    ##   group x_missing y                  
    ##   <int>     <int> <chr>              
    ## 1     1         2 a, b, c, d, e and f
    ## 2     2         2 g, h, i, j, k and l

Dataframe functions can be embedded into pipe workflows of data
wrangling: input and output are both dataframes, so you can take
additional steps of data manipulation before and after calling the
function.

The issue you’re likely to encounter when writing dataframe functions is
having to override the default tidy evaluation. Tidy evaluation is why
you can refer to variable names as objects in `dplyr` functions
(e.g. `filter(type == 1)`). Because of tidy evaluation, if you specify a
variable name as a function argument, referring to the argument as an
object inside the function body (as you would with vector functions), R
will take the argument name instead the argument value you define in the
function call to be the variable used in the function body. Embracing
the variable names in question tells R that you’re referring to the
function argument, not the variable name directly.

``` r
## Dataframe functions
# input: dataframe; output: dataframe

# group_var and mean_var treated as variable names, not function arguments
grouped_mean <- function(df, group_var, mean_var) {
  df |> 
    group_by(group_var) |> 
    summarize(mean(mean_var))
}

# error: variables not found
tibble(x = 1:20,
       group = rep(1:2, each = 10)) |> 
  grouped_mean(group_var = group, mean_var = x)
```

``` r
# embracing arguments means they take on the value defined in the function call
grouped_mean <- function(df, group_var, mean_var) {
  df |> 
    group_by({{ group_var }}) |> 
    summarize(mean({{ mean_var }}))
}

# works as expected
tibble(x = 1:20,
       group = rep(1:2, each = 10)) |> 
  grouped_mean(group_var = group, mean_var = x)
```

    ## # A tibble: 2 × 2
    ##   group `mean(x)`
    ##   <int>     <dbl>
    ## 1     1       5.5
    ## 2     2      15.5

``` r
# More examples
count_prop <- function(df, var, sort = FALSE) {
  df |>
    count({{ var }}, sort = sort) |>
    mutate(prop = n / sum(n))
}

diamonds |> count_prop(clarity)
```

    ## # A tibble: 8 × 3
    ##   clarity     n   prop
    ##   <ord>   <int>  <dbl>
    ## 1 I1        741 0.0137
    ## 2 SI2      9194 0.170 
    ## 3 SI1     13065 0.242 
    ## 4 VS2     12258 0.227 
    ## 5 VS1      8171 0.151 
    ## 6 VVS2     5066 0.0939
    ## 7 VVS1     3655 0.0678
    ## 8 IF       1790 0.0332

``` r
unique_where <- function(df, condition, var) {
  df |> 
    filter({{ condition }}) |> 
    distinct({{ var }}) |> 
    arrange({{ var }})
}

diamonds |> unique_where(cut == "Ideal", color)
```

    ## # A tibble: 7 × 1
    ##   color
    ##   <ord>
    ## 1 D    
    ## 2 E    
    ## 3 F    
    ## 4 G    
    ## 5 H    
    ## 6 I    
    ## 7 J

``` r
## Plot functions

# Define the repetitive part of ggplot code
histogram <- function(df, var) {
  df |> 
    ggplot(aes(x = {{ var }})) + 
    geom_histogram() 
}

histogram(diamonds, carat)

# Add more plot elements, data wrangling
diamonds |> 
  filter(color == "G") |> 
  histogram(carat) +
  facet_wrap(~cut)

# Add labels with rlang::englue
# variables in {{ }}, other arguments in { }
histogram <- function(df, var, binwidth) {
  label <- rlang::englue("A histogram of {{var}} with binwidth {binwidth}")
  
  df |> 
    ggplot(aes(x = {{ var }})) + 
    geom_histogram(binwidth = binwidth) + 
    labs(title = label)
}

diamonds |> histogram(carat, 0.1)
```

## Conditional execution

``` r
## Use for early return/stopping/error messages

# Return normal mean if weights are not specified
weighted_mean <- function(x, w = NULL) {
  if (is.null(w)) {
    return(mean(x))
  }
  sum(w * x) / sum(w)
}

# Stop with custom error message
weighted_mean <- function(x, w) {
  if (length(x) != length(w)) {
    stop("`x` and `w` must be the same length")
  }
  sum(w * x) / sum(w)
}

# Alternatively, use stopifnot for a generic error
weighted_mean <- function(x, w) {
  stopifnot(length(x) == length(w))
  sum(w * x) / sum(w)
}

## Use conditions for different behavior with different arguments
weighted_mean <- function(x, w, na.rm = FALSE) {
  stopifnot(length(x) == length(w), is.logical(na.rm), length(na.rm) == 1)
  
  # Drop NA values from x and w if na.rm == TRUE
  if (na.rm) {
    na <- is.na(x) | is.na(w)
    x <- x[!na]
    w <- w[!na]
  }
  
  sum(w * x) / sum(w)
}
```

``` r
## Nested if else
if (this) {
  # do that
} else if (that) {
  # do something else
} else {
  # do a different thing
}
```

## What objects exist in which environment?

``` r
## Functions can find objects defined outside the function

# works if y is defined outside the function (but not advised)
f <- function(x) {
  x + y
} 

y <- 5
f(2)
```

    ## [1] 7

``` r
## Functions defined inside the function don't exist outside the function
f <- function(x) {
  res <- x + y
  res
}

y <- 5
f(2)
```

    ## [1] 7

``` r
res
```

    ## $x
    ##  [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
    ##  [8] 0.7777778 0.8888889 1.0000000
    ## 
    ## $original_minimum
    ## [1] 1
    ## 
    ## $original_maximum
    ## [1] 10

# Iteration with functionals

Hadley Wickham: [Advanced
R](https://adv-r.hadley.nz/functionals.html#map) (Chapter 9:
Functionals).

## Why should you use functionals?

## `map()` and its versions

## Functionals in tidy data wrangling

## Applying methods to multiple variables with `across()`

``` r
df <- tibble(
  a = rnorm(10),
  b = rnorm(10),
  c = rnorm(10),
  d = rnorm(10)
)

df |> summarize(
  n = n(),
  a = median(a),
  b = median(b),
  c = median(c),
  d = median(d)
)
```

    ## # A tibble: 1 × 5
    ##       n     a     b     c     d
    ##   <int> <dbl> <dbl> <dbl> <dbl>
    ## 1    10 0.191 0.472 0.366 0.184

``` r
# Get column medians for columns a to d
df |> summarize(
  n = n(),
  across(a:d, median)
)
```

    ## # A tibble: 1 × 5
    ##       n     a     b     c     d
    ##   <int> <dbl> <dbl> <dbl> <dbl>
    ## 1    10 0.191 0.472 0.366 0.184

``` r
# Get column medians for all columns
df |> summarize(
  n = n(),
  across(everything(), median)
)
```

    ## # A tibble: 1 × 5
    ##       n     a     b     c     d
    ##   <int> <dbl> <dbl> <dbl> <dbl>
    ## 1    10 0.191 0.472 0.366 0.184

``` r
# Other variable list specifications:

# starts_with("s") # equivalent to regex "^s.*"
# ends_with("s") # equivalent to regex ".*s$"
# contains("s") # equivalent to regex "s"
# matches("s") # equivalent to contains() but "s" can be regex
# where(is.numeric) # all numeric (or other type) variables
# ! negates selectors (e.g. !where(is.numeric))

# Explicit function arguments
df |> summarize(
  n = n(),
  across(everything(), ~mean(., na.rm = TRUE))
)
```

    ## # A tibble: 1 × 5
    ##       n     a     b     c     d
    ##   <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1    10 0.227 0.211 0.504 0.138

``` r
# More complex functions, overwrite existing variable
df |> 
  mutate(across(everything(), ~ (. - min(.)) / (max(.) - min(.))))
```

    ## # A tibble: 10 × 4
    ##        a     b      c     d
    ##    <dbl> <dbl>  <dbl> <dbl>
    ##  1 0.489 0.765 1      0    
    ##  2 0.565 0.486 0      0.385
    ##  3 0.621 0.333 0.0327 0.646
    ##  4 0.481 1     0.128  0.662
    ##  5 0.590 0.914 0.350  0.574
    ##  6 0.633 0.679 0.248  0.207
    ##  7 0     0.949 0.0900 0.191
    ##  8 1     0.685 0.602  0.412
    ##  9 0.551 0.395 0.616  1    
    ## 10 0.978 0     0.376  0.720

``` r
# More complex functions, create new variable
df |> 
  mutate(across(everything(), list(rescaled = ~ (. - min(.)) / (max(.) - min(.)))))
```

    ## # A tibble: 10 × 8
    ##          a      b      c       d a_rescaled b_rescaled c_rescaled d_rescaled
    ##      <dbl>  <dbl>  <dbl>   <dbl>      <dbl>      <dbl>      <dbl>      <dbl>
    ##  1 -0.0550  0.825  2.52  -1.52        0.489      0.765     1           0    
    ##  2  0.155  -0.361 -0.554 -0.190       0.565      0.486     0           0.385
    ##  3  0.312  -1.01  -0.454  0.713       0.621      0.333     0.0327      0.646
    ##  4 -0.0789  1.82  -0.161  0.767       0.481      1         0.128       0.662
    ##  5  0.226   1.46   0.523  0.465       0.590      0.914     0.350       0.574
    ##  6  0.345   0.459  0.209 -0.807       0.633      0.679     0.248       0.207
    ##  7 -1.41    1.61  -0.277 -0.859       0          0.949     0.0900      0.191
    ##  8  1.36    0.485  1.30  -0.0973      1          0.685     0.602       0.412
    ##  9  0.116  -0.748  1.34   1.94        0.551      0.395     0.616       1    
    ## 10  1.30   -2.43   0.602  0.969       0.978      0         0.376       0.720

``` r
# Multiple functions
df |> summarize(
  n = n(),
  across(everything(), 
         list(mean = ~mean(., na.rm = TRUE), 
              median = median),
         .names = "{.fn}_{.col}")
)
```

    ## # A tibble: 1 × 11
    ##       n mean_a median_a mean_b median_b mean_c median_c mean_d median_d mean_n
    ##   <int>  <dbl>    <dbl>  <dbl>    <dbl>  <dbl>    <dbl>  <dbl>    <dbl>  <dbl>
    ## 1    10  0.227    0.191  0.211    0.472  0.504    0.366  0.138    0.184     10
    ## # ℹ 1 more variable: median_n <int>

## Filtering with `if_any` and `if_all`

``` r
df |> filter(if_any(a:d, ~ . > 0))
```

    ## # A tibble: 10 × 4
    ##          a      b      c       d
    ##      <dbl>  <dbl>  <dbl>   <dbl>
    ##  1 -0.0550  0.825  2.52  -1.52  
    ##  2  0.155  -0.361 -0.554 -0.190 
    ##  3  0.312  -1.01  -0.454  0.713 
    ##  4 -0.0789  1.82  -0.161  0.767 
    ##  5  0.226   1.46   0.523  0.465 
    ##  6  0.345   0.459  0.209 -0.807 
    ##  7 -1.41    1.61  -0.277 -0.859 
    ##  8  1.36    0.485  1.30  -0.0973
    ##  9  0.116  -0.748  1.34   1.94  
    ## 10  1.30   -2.43   0.602  0.969

``` r
df |> filter(if_all(a:b, ~ . > 0))
```

    ## # A tibble: 3 × 4
    ##       a     b     c       d
    ##   <dbl> <dbl> <dbl>   <dbl>
    ## 1 0.226 1.46  0.523  0.465 
    ## 2 0.345 0.459 0.209 -0.807 
    ## 3 1.36  0.485 1.30  -0.0973

``` r
# across() is equivalent to if_all but less intuitive
df |> filter(across(a:b, ~ . > 0))
```

    ## # A tibble: 3 × 4
    ##       a     b     c       d
    ##   <dbl> <dbl> <dbl>   <dbl>
    ## 1 0.226 1.46  0.523  0.465 
    ## 2 0.345 0.459 0.209 -0.807 
    ## 3 1.36  0.485 1.30  -0.0973

<!-- * functionals instead of loops - why? -->
<!-- * map and its versions (map2, map_df, map_dbl, pmap, ect.) -->
<!-- * function specification: function name or syntax with `~` and `.x`/`.y`(/position/name with `pmap`) -->
<!-- * map in mutate - when is it needed? -->
