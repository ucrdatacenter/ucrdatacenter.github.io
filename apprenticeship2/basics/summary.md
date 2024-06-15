---
title: "Data Center Apprenticeship:\nR basics: Summary statistics"
subtitle: "June 2024" 
date: "Last updated: 2024-06-15"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Go to](#go-to)

To get a descriptive statistic of a single variable in a tibble, we can
use that variable as an argument to a relevant function (using `$` to
refer to a variable in a tibble).

``` r
mean(data$age)
```

    ## [1] 19.68276

``` r
median(data$age)
```

    ## [1] 19

``` r
sd(data$grade)
```

    ## [1] 1.281705

To get the frequencies of a categorical variable, we can use the
`count()` function, with the `sort = TRUE` argument returning the values
in descending frequency. `count()` is a tidy function that works well
with pipe workflows and can count the joint frequencies of multiple
variables.

``` r
# frequencies of a single variable
count(data, reading)
```

    ## # A tibble: 2 × 2
    ##   reading     n
    ##   <lgl>   <int>
    ## 1 FALSE      76
    ## 2 TRUE       69

``` r
# joint frequency distribution
count(data, reading, listening, notes)
```

    ## # A tibble: 8 × 4
    ##   reading listening notes     n
    ##   <lgl>   <lgl>     <lgl> <int>
    ## 1 FALSE   FALSE     FALSE    14
    ## 2 FALSE   FALSE     TRUE     23
    ## 3 FALSE   TRUE      FALSE    20
    ## 4 FALSE   TRUE      TRUE     19
    ## 5 TRUE    FALSE     FALSE    19
    ## 6 TRUE    FALSE     TRUE     14
    ## 7 TRUE    TRUE      FALSE    15
    ## 8 TRUE    TRUE      TRUE     21

To get the correlation coefficient between two variables, we can use the
`cor()` function in the same way we used other descriptives such as
`mean()`.

``` r
cor(data$age, data$grade)
```

    ## [1] 0.1856025

The easiest way to get summary statistics of all variables in a tibble
is with the `summary()` function: this function shows the distribution
of numeric variables, the frequencies of categorical variables, and the
number of missing values for each variable.

``` r
summary(data)
```

    ##        id            age         scholarship     additional_work
    ##  Min.   :5001   Min.   :18.00   Min.   : 25.00   Mode :logical  
    ##  1st Qu.:5037   1st Qu.:18.00   1st Qu.: 50.00   FALSE:96       
    ##  Median :5073   Median :19.00   Median : 50.00   TRUE :49       
    ##  Mean   :5073   Mean   :19.68   Mean   : 64.76                  
    ##  3rd Qu.:5109   3rd Qu.:21.00   3rd Qu.: 75.00                  
    ##  Max.   :5145   Max.   :26.00   Max.   :100.00                  
    ##                                 NA's   :1                       
    ##   reading          notes         listening           grade      
    ##  Mode :logical   Mode :logical   Mode :logical   Min.   :0.000  
    ##  FALSE:76        FALSE:68        FALSE:70        1st Qu.:1.500  
    ##  TRUE :69        TRUE :77        TRUE :75        Median :3.000  
    ##                                                  Mean   :2.755  
    ##                                                  3rd Qu.:4.000  
    ##                                                  Max.   :4.000  
    ## 

The `summary()` function is useful for viewing the data in the Console,
but doesn’t export to outside of R nicely. There are a few packages
available for generating simple summary statistics tables that contain
information about the central tendencies and dispersion of the data,
such as `vtable` or `stargazer` (with many more available). These
packages all have different default settings, output types, and
customization options.

``` r
library(vtable)
library(stargazer)

# vtable

data |> 
  # by default creates HTML table; out = "csv" returns a dataframe
  # can change which descriptives to keep
  # can report group-level descriptives
  sumtable(out = "csv", group = "reading")
```

    ##           Variable  N Mean  SD   N Mean  SD
    ## 1          reading No          Yes         
    ## 2               id 76 5080  44  69 5066  39
    ## 3              age 76   20 2.3  69   20 1.7
    ## 4      scholarship 75   65  18  69   64  21
    ## 5  additional_work 76           69         
    ## 6           ... No 47  62%      49  71%    
    ## 7          ... Yes 29  38%      20  29%    
    ## 8            notes 76           69         
    ## 9           ... No 34  45%      34  49%    
    ## 10         ... Yes 42  55%      35  51%    
    ## 11       listening 76           69         
    ## 12          ... No 37  49%      33  48%    
    ## 13         ... Yes 39  51%      36  52%    
    ## 14           grade 76  2.5 1.3  69    3 1.2

``` r
# stargazer

data |> 
  # input needs to be a data.frame, not tibble
  as.data.frame() |> 
  # default output is LaTeX table
  # can be exported with the out argument or a following write() function
  # can change which descriptives to keep or omit
  # limited to numeric variables
  stargazer(type = "text")
```

    ## 
    ## ==================================================
    ## Statistic        N    Mean    St. Dev.  Min   Max 
    ## --------------------------------------------------
    ## id              145 5,073.000  42.002  5,001 5,145
    ## age             145  19.683    1.992    18    26  
    ## scholarship     144  64.757    19.480   25    100 
    ## additional_work 145   0.338    0.475     0     1  
    ## reading         145   0.476    0.501     0     1  
    ## notes           145   0.531    0.501     0     1  
    ## listening       145   0.517    0.501     0     1  
    ## grade           145   2.755    1.282   0.000 4.000
    ## --------------------------------------------------

Alternatively, we can define our own summary statistics with the `dplyr`
functions `group_by()` and `summarize()`, which also easily allows the
calculation of more complex descriptive statistics, including grouped
statistics based on categorical variables. The `across()` helper
function in the `summarize()` function can be used to apply the same
calculation to multiple variables at once: it requires the first
argument as the list of variables (potentially with the help of selector
functions) and the function we’d like to apply.

``` r
# tibble of mean and sd for a single variable
data |> 
  summarize(mean_grade = mean(grade),
            sd_grade = sd(grade))
```

    ## # A tibble: 1 × 2
    ##   mean_grade sd_grade
    ##        <dbl>    <dbl>
    ## 1       2.76     1.28

``` r
# mean and sd of age and grade variables, grouped by reading
data |> 
  group_by(reading) |> 
  # .names allows overriding default option to reuse original column names
  summarize(across(c(age, grade), mean, .names = "mean_{.col}"),
            across(c(age, grade), sd, .names = "sd_{.col}"))
```

    ## # A tibble: 2 × 5
    ##   reading mean_age mean_grade sd_age sd_grade
    ##   <lgl>      <dbl>      <dbl>  <dbl>    <dbl>
    ## 1 FALSE       19.7       2.54   2.27     1.34
    ## 2 TRUE        19.7       2.99   1.66     1.18

``` r
# mean of all numeric variables, grouped by reading
data |> 
  group_by(reading) |> 
  # where() is a helper function evaluating the contents of variables
  # specify full function call with ~ at the start and .x replacing the variable name
  summarize(across(where(is.numeric), ~mean(.x, na.rm = TRUE)))
```

    ## # A tibble: 2 × 5
    ##   reading    id   age scholarship grade
    ##   <lgl>   <dbl> <dbl>       <dbl> <dbl>
    ## 1 FALSE   5080.  19.7        65    2.54
    ## 2 TRUE    5066.  19.7        64.5  2.99

``` r
# mean of all variables with names containing the letter a
data |> 
  summarize(across(contains("a"), ~mean(.x, na.rm = TRUE)))
```

    ## # A tibble: 1 × 5
    ##     age scholarship additional_work reading grade
    ##   <dbl>       <dbl>           <dbl>   <dbl> <dbl>
    ## 1  19.7        64.8           0.338   0.476  2.76

``` r
# sample size of each group and correlation between age and grade per group
data |> 
  group_by(reading, listening) |> 
  summarize(age_grade_correlation = cor(age, grade),
            n = n())
```

    ## # A tibble: 4 × 4
    ## # Groups:   reading [2]
    ##   reading listening age_grade_correlation     n
    ##   <lgl>   <lgl>                     <dbl> <int>
    ## 1 FALSE   FALSE                    0.267     37
    ## 2 FALSE   TRUE                     0.334     39
    ## 3 TRUE    FALSE                    0.0720    33
    ## 4 TRUE    TRUE                    -0.0777    36

The list of helper functions that can be used instead of listing which
variables to include/exclude is in the help file accessible with
`?dplyr_tidy_select`.

To export a descriptive statistics table, we can use the relevant
`write...()` function shown in the data importing section
(e.g. `write_csv()` for tibbles, general `write()` for HTML, plain text,
LaTeX, other general types). CSV tables already copy nicely into e.g. MS
Word. If using LaTeX or RMarkdown, the `knitr` package contains the
`kable()` function that directly improves on the design of the table
without needing formatting afterwards.

``` r
data |> 
  count(reading, listening) |> 
  write_csv("table1.csv")
```

``` r
data |> 
  count(reading, listening) |> 
  # knitr:: allows using function from the package without library(knitr)
  knitr::kable()
```

| reading | listening |   n |
|:--------|:----------|----:|
| FALSE   | FALSE     |  37 |
| FALSE   | TRUE      |  39 |
| TRUE    | FALSE     |  33 |
| TRUE    | TRUE      |  36 |

# Go to

- [Introduction to R](intro)
- [Finding and importing data](import)
- [Data cleaning with `dplyr`](clean)
- [Data visualization with `ggplot2`](ggplot)
- [Hypothesis testing / modelling](tests)
