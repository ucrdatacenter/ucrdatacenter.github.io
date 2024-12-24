---
title: "Data Center Apprenticeship:\nStatistics in R"
subtitle: "January 2025" 
date: "Last updated: 2024-12-24"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Introduction](#introduction)
- [Descriptive statistics](#descriptive-statistics)
- [Hypothesis testing](#hypothesis-testing)
  - [t-tests](#t-tests)
  - [Correlation test](#correlation-test)
  - [Simple regression](#simple-regression)
  - [Multiple regression](#multiple-regression)
  - [ANOVA](#anova)
  - [Chi-square test](#chi-square-test)
  - [Logistic regression](#logistic-regression)
  - [Non-parametric tests](#non-parametric-tests)
  - [PCA and factor analysis](#pca-and-factor-analysis)

# Introduction

This document provides an overview of the basic statistical functions in
R, including descriptive statistics and summary tables. We work with
some example data of student characteristics and grades. You can import
the data from a CSV file with the following code:

``` r
library(tidyverse)
data <- read_csv("https://github.com/ucrdatacenter/projects/raw/refs/heads/main/apprenticeship/2025h1/student_data.csv")
```

# Descriptive statistics

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

    ##        id            age            sex             scholarship    
    ##  Min.   :5001   Min.   :18.00   Length:145         Min.   : 25.00  
    ##  1st Qu.:5037   1st Qu.:18.00   Class :character   1st Qu.: 50.00  
    ##  Median :5073   Median :19.00   Mode  :character   Median : 50.00  
    ##  Mean   :5073   Mean   :19.68                      Mean   : 64.76  
    ##  3rd Qu.:5109   3rd Qu.:21.00                      3rd Qu.: 75.00  
    ##  Max.   :5145   Max.   :26.00                      Max.   :100.00  
    ##                                                    NA's   :1       
    ##  additional_work  reading          notes         listening      
    ##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
    ##  FALSE:96        FALSE:76        FALSE:68        FALSE:70       
    ##  TRUE :49        TRUE :69        TRUE :77        TRUE :75       
    ##                                                                 
    ##                                                                 
    ##                                                                 
    ##                                                                 
    ##      grade      
    ##  Min.   :0.000  
    ##  1st Qu.:1.500  
    ##  Median :3.000  
    ##  Mean   :2.755  
    ##  3rd Qu.:4.000  
    ##  Max.   :4.000  
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
    ## 4              sex 76           69         
    ## 5       ... Female 32  42%      26  38%    
    ## 6         ... Male 44  58%      43  62%    
    ## 7      scholarship 75   65  18  69   64  21
    ## 8  additional_work 76           69         
    ## 9           ... No 47  62%      49  71%    
    ## 10         ... Yes 29  38%      20  29%    
    ## 11           notes 76           69         
    ## 12          ... No 34  45%      34  49%    
    ## 13         ... Yes 42  55%      35  51%    
    ## 14       listening 76           69         
    ## 15          ... No 37  49%      33  48%    
    ## 16         ... Yes 39  51%      36  52%    
    ## 17           grade 76  2.5 1.3  69    3 1.2

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

# Hypothesis testing

Most of the simple statistical tests are from base R, so they don’t rely
on tidy principles, but many are compatible with tidy workflows to at
least some extent. In the following we’ll cover some of the key methods
that show up in methods and statistics courses at UCR. In addition, the
`tidy()` function from the `broom` package converts most text output
into simple tibbles, which are useful for exporting and visualizing
results.

``` r
library(broom)
```

## t-tests

We can run one and two samples t-tests to evaluate group means with the
`t.test()` function. The function supports various options and model
specifications: a simple one-sample t-test only requires specifying the
variable of interest, either with `x = data$variable` or
`x = variable, data = data` syntax. For two-sample t-tests, we can use
the formula syntax `y ~ x` to specify the dependent and independent
variables or the `x` and `y` (and optionally `data`) arguments.
Additional options include specifying the alternative hypothesis, the
confidence level, the value of $\mu$, and whether we want a paired
t-test and assume equal variances. Helper functions such as `tidy()`
convert the Console output to an easy-to-export tibble of results.

``` r
library(broom) # for tidy() function

# simple t-test (H0: mean=mu)
t.test(data$scholarship, mu = 50)
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  data$scholarship
    ## t = 9.0903, df = 143, p-value = 7.493e-16
    ## alternative hypothesis: true mean is not equal to 50
    ## 95 percent confidence interval:
    ##  61.54805 67.96584
    ## sample estimates:
    ## mean of x 
    ##  64.75694

``` r
# use data argument instead of data$... to work in pipe workflows
data |> 
  # grade ~ reading is formula specification: variable ~ group
  # _ is placeholder if the pipe input is not the first argument of the next function
  t.test(grade ~ reading, alternative = "greater", data = _)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  grade by reading
    ## t = -2.1671, df = 142.85, p-value = 0.9841
    ## alternative hypothesis: true difference in means between group FALSE and group TRUE is greater than 0
    ## 95 percent confidence interval:
    ##  -0.7995718        Inf
    ## sample estimates:
    ## mean in group FALSE  mean in group TRUE 
    ##            2.539474            2.992754

``` r
data |> 
  t.test(grade ~ reading, alternative = "greater", data = _) |> 
  tidy()
```

    ## # A tibble: 1 × 10
    ##   estimate estimate1 estimate2 statistic p.value parameter conf.low conf.high
    ##      <dbl>     <dbl>     <dbl>     <dbl>   <dbl>     <dbl>    <dbl>     <dbl>
    ## 1   -0.453      2.54      2.99     -2.17   0.984      143.   -0.800       Inf
    ## # ℹ 2 more variables: method <chr>, alternative <chr>

## Correlation test

The `cor.test()` function calculates the correlation between two
variables. Again, the function supports various specifications: `x` and
`y` arguments, formula syntax (see below for an example), and the `data`
argument.

``` r
cor.test( ~ grade + age, data = data)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  grade and age
    ## t = 2.2587, df = 143, p-value = 0.02541
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.02329811 0.33837421
    ## sample estimates:
    ##       cor 
    ## 0.1856025

``` r
# assign the outcome to an object
cor_result <- cor.test( ~ grade + age, data = data)
# tibble of results
tidy(cor_result)
```

    ## # A tibble: 1 × 8
    ##   estimate statistic p.value parameter conf.low conf.high method     alternative
    ##      <dbl>     <dbl>   <dbl>     <int>    <dbl>     <dbl> <chr>      <chr>      
    ## 1    0.186      2.26  0.0254       143   0.0233     0.338 Pearson's… two.sided

## Simple regression

A key building block of statistical analysis is linear regression. The
`lm()` function fits a linear model to the data, with a wide range of
specifications, passed as the formula argument (first argument if
unnamed). The formula syntax is `y ~ x`, where `y` is the dependent
variable and `x` is the independent variable. Again, optional function
arguments allow for a lot of customization, but the default settings are
sufficient for most applications. Helper functions such as `tidy()` and
`summary()` provide extensive summaries of the model fit and
coefficients, and `stargazer()` creates neat tables of the results.
Assigning the result of a model to an object saves computational time,
as then we can work with the results without having to re-run the
analysis every time.

``` r
# assign outcome to object
fit <- lm(grade ~ age, data = data)

# extensive result summary
fit |> summary()
```

    ## 
    ## Call:
    ## lm(formula = grade ~ age, data = data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.9125 -1.0542  0.3264  1.0875  1.4458 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)  
    ## (Intercept)  0.40464    1.04592   0.387   0.6994  
    ## age          0.11942    0.05287   2.259   0.0254 *
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.264 on 143 degrees of freedom
    ## Multiple R-squared:  0.03445,    Adjusted R-squared:  0.0277 
    ## F-statistic: 5.102 on 1 and 143 DF,  p-value: 0.02541

``` r
# tidy coefficients
fit |> tidy()
```

    ## # A tibble: 2 × 5
    ##   term        estimate std.error statistic p.value
    ##   <chr>          <dbl>     <dbl>     <dbl>   <dbl>
    ## 1 (Intercept)    0.405    1.05       0.387  0.699 
    ## 2 age            0.119    0.0529     2.26   0.0254

``` r
# display-ready table
fit |> 
  stargazer(type = "text", title = "Grade - agre regression results")
```

    ## 
    ## Grade - agre regression results
    ## ===============================================
    ##                         Dependent variable:    
    ##                     ---------------------------
    ##                                grade           
    ## -----------------------------------------------
    ## age                           0.119**          
    ##                               (0.053)          
    ##                                                
    ## Constant                       0.405           
    ##                               (1.046)          
    ##                                                
    ## -----------------------------------------------
    ## Observations                    145            
    ## R2                             0.034           
    ## Adjusted R2                    0.028           
    ## Residual Std. Error      1.264 (df = 143)      
    ## F Statistic            5.102** (df = 1; 143)   
    ## ===============================================
    ## Note:               *p<0.1; **p<0.05; ***p<0.01

## Multiple regression

Multiple regression extends simple regression to multiple independent
variables. The only difference is the formula specification, which now
connects multiple independent variables with `+` signs. The formula
specification also allows for interactions between variables, which can
be specified with `*` (if the main effects should be included) or `:`
(for only the interaction term). The `DV ~ .~` syntax includes all
variables in the data except the dependent variable as independent
variables.

``` r
lm(grade ~ age + scholarship, data = data) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = grade ~ age + scholarship, data = data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.8845 -1.0609  0.3735  1.1478  1.5025 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)  
    ## (Intercept) 0.048581   1.292374   0.038    0.970  
    ## age         0.129004   0.056549   2.281    0.024 *
    ## scholarship 0.002536   0.005788   0.438    0.662  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.271 on 141 degrees of freedom
    ##   (1 observation deleted due to missingness)
    ## Multiple R-squared:  0.03636,    Adjusted R-squared:  0.0227 
    ## F-statistic:  2.66 on 2 and 141 DF,  p-value: 0.07343

``` r
# all variables in data
lm(grade ~ ., data = data) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = grade ~ ., data = data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -3.5165 -0.9030  0.2786  0.9096  2.1809 
    ## 
    ## Coefficients:
    ##                      Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept)         45.780346  14.038274   3.261  0.00141 **
    ## id                  -0.009043   0.002749  -3.290  0.00128 **
    ## age                  0.128874   0.056319   2.288  0.02367 * 
    ## sexMale             -0.517980   0.230416  -2.248  0.02620 * 
    ## scholarship          0.005147   0.005825   0.883  0.37855   
    ## additional_workTRUE -0.041568   0.231178  -0.180  0.85757   
    ## readingTRUE          0.361383   0.206867   1.747  0.08292 . 
    ## notesTRUE            0.011816   0.210672   0.056  0.95536   
    ## listeningTRUE        0.225988   0.209844   1.077  0.28343   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.218 on 135 degrees of freedom
    ##   (1 observation deleted due to missingness)
    ## Multiple R-squared:  0.1525, Adjusted R-squared:  0.1023 
    ## F-statistic: 3.037 on 8 and 135 DF,  p-value: 0.003596

``` r
# interactions
lm(grade ~ age * scholarship, data = data) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = grade ~ age * scholarship, data = data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.8931 -1.0857  0.2747  1.1853  1.5539 
    ## 
    ## Coefficients:
    ##                  Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)     -2.554899   3.814695  -0.670    0.504
    ## age              0.262316   0.192274   1.364    0.175
    ## scholarship      0.046368   0.060690   0.764    0.446
    ## age:scholarship -0.002266   0.003123  -0.726    0.469
    ## 
    ## Residual standard error: 1.273 on 140 degrees of freedom
    ##   (1 observation deleted due to missingness)
    ## Multiple R-squared:  0.03997,    Adjusted R-squared:  0.0194 
    ## F-statistic: 1.943 on 3 and 140 DF,  p-value: 0.1254

## ANOVA

Analysis of variance (ANOVA) is a generalization of the t-test to
multiple groups. The `aov()` function fits an ANOVA model to the data,
with the formula syntax `y ~ x`, where `y` is the dependent variable and
`x` is the independent variable. The same helper functions as with
`lm()` can be used to summarize the results.

Note that ANOVA is a specific case of a linear regression model, so the
results are equivalent to those of a linear regression model with a
categorical independent variable.

``` r
anova_fit <- aov(grade ~ reading, data = data)

summary(anova_fit)
```

    ##              Df Sum Sq Mean Sq F value Pr(>F)  
    ## reading       1   7.43   7.431   4.638  0.033 *
    ## Residuals   143 229.13   1.602                 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
tidy(anova_fit)
```

    ## # A tibble: 2 × 6
    ##   term         df  sumsq meansq statistic p.value
    ##   <chr>     <dbl>  <dbl>  <dbl>     <dbl>   <dbl>
    ## 1 reading       1   7.43   7.43      4.64  0.0330
    ## 2 Residuals   143 229.     1.60     NA    NA

``` r
# equivalent regression
lm(grade ~ reading, data = data) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = grade ~ reading, data = data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.9928 -1.0395  0.4605  1.0072  1.4605 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   2.5395     0.1452  17.490   <2e-16 ***
    ## readingTRUE   0.4533     0.2105   2.153    0.033 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.266 on 143 degrees of freedom
    ## Multiple R-squared:  0.03141,    Adjusted R-squared:  0.02464 
    ## F-statistic: 4.638 on 1 and 143 DF,  p-value: 0.03296

## Chi-square test

The chi-square test is used to test the independence of two categorical
variables. The `chisq.test()` function calculates the chi-square
statistic and the p-value for the test. Unlike the previous functions,
the function does not allow for a `data` argument, and is therefore
difficult to implement in tidy workflows.

``` r
chisq.test(data$reading, data$notes)
```

    ## 
    ##  Pearson's Chi-squared test with Yates' continuity correction
    ## 
    ## data:  data$reading and data$notes
    ## X-squared = 0.14464, df = 1, p-value = 0.7037

Add contingency tables:

``` r
# with table()
table(data$reading, data$notes)
```

    ##        
    ##         FALSE TRUE
    ##   FALSE    34   42
    ##   TRUE     34   35

``` r
# with xtabs()
xtabs(~ reading + notes, data = data)
```

    ##        notes
    ## reading FALSE TRUE
    ##   FALSE    34   42
    ##   TRUE     34   35

``` r
# with count() and pivot_wider()
data |> 
  count(reading, notes) |>
  pivot_wider(names_from = notes, values_from = n, values_fill = 0)
```

    ## # A tibble: 2 × 3
    ##   reading `FALSE` `TRUE`
    ##   <lgl>     <int>  <int>
    ## 1 FALSE        34     42
    ## 2 TRUE         34     35

``` r
# proportions with table() and prop.table()
table(data$reading, data$notes) |> prop.table()
```

    ##        
    ##             FALSE      TRUE
    ##   FALSE 0.2344828 0.2896552
    ##   TRUE  0.2344828 0.2413793

``` r
# proportions with count() and pivot_wider()
data |> 
  count(reading, notes) |>
  mutate(prop = n / sum(n)) |>
  select(-n) |> 
  pivot_wider(names_from = notes, values_from = prop, values_fill = 0)
```

    ## # A tibble: 2 × 3
    ##   reading `FALSE` `TRUE`
    ##   <lgl>     <dbl>  <dbl>
    ## 1 FALSE     0.234  0.290
    ## 2 TRUE      0.234  0.241

## Logistic regression

When it comes to predicting binary outcomes, linear regression has some
problems, such as predicting values outside the 0-1 range. Therefore in
those cases, we often use logistic regression instead. The `glm()`
function fits a logistic regression model to the data. Other than the
`family` argument, which specifies the distribution of the dependent
variable, the function works in the same as `lm()`. A logistic
regression uses `family = "binomial"`.

``` r
logit_fit <- glm(reading ~ age, data = data, family = "binomial")

logit_fit |> summary()
```

    ## 
    ## Call:
    ## glm(formula = reading ~ age, family = "binomial", data = data)
    ## 
    ## Coefficients:
    ##              Estimate Std. Error z value Pr(>|z|)
    ## (Intercept) -0.219462   1.656757  -0.132    0.895
    ## age          0.006241   0.083743   0.075    0.941
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 200.67  on 144  degrees of freedom
    ## Residual deviance: 200.67  on 143  degrees of freedom
    ## AIC: 204.67
    ## 
    ## Number of Fisher Scoring iterations: 3

``` r
logit_fit |> tidy()
```

    ## # A tibble: 2 × 5
    ##   term        estimate std.error statistic p.value
    ##   <chr>          <dbl>     <dbl>     <dbl>   <dbl>
    ## 1 (Intercept) -0.219      1.66     -0.132    0.895
    ## 2 age          0.00624    0.0837    0.0745   0.941

``` r
# display-ready table
stargazer(logit_fit, type = "text", 
          title = "Relationship between doing the reading and age")
```

    ## 
    ## Relationship between doing the reading and age
    ## =============================================
    ##                       Dependent variable:    
    ##                   ---------------------------
    ##                             reading          
    ## ---------------------------------------------
    ## age                          0.006           
    ##                             (0.084)          
    ##                                              
    ## Constant                    -0.219           
    ##                             (1.657)          
    ##                                              
    ## ---------------------------------------------
    ## Observations                  145            
    ## Log Likelihood             -100.335          
    ## Akaike Inf. Crit.           204.669          
    ## =============================================
    ## Note:             *p<0.1; **p<0.05; ***p<0.01

## Non-parametric tests

Non-parametric tests are used when the assumptions of parametric tests
are violated. Running them in R follows the same structure as running
the parametric alternative, other than the function name itself and
potential alternative optional arguments.

``` r
# Wilcoxon signed-rank test
wilcox.test(data$grade, mu = 2)
```

    ## 
    ##  Wilcoxon signed rank test with continuity correction
    ## 
    ## data:  data$grade
    ## V = 6767.5, p-value = 1.238e-10
    ## alternative hypothesis: true location is not equal to 2

``` r
# Mann-Whitney U test
wilcox.test(grade ~ reading, data = data)
```

    ## 
    ##  Wilcoxon rank sum test with continuity correction
    ## 
    ## data:  grade by reading
    ## W = 2119, p-value = 0.03795
    ## alternative hypothesis: true location shift is not equal to 0

``` r
# Kruskal-Wallis test (scholarship is a number in the data so convert to factor for this example)
kruskal.test(grade ~ factor(scholarship), data = data)
```

    ## 
    ##  Kruskal-Wallis rank sum test
    ## 
    ## data:  grade by factor(scholarship)
    ## Kruskal-Wallis chi-squared = 20.368, df = 3, p-value = 0.0001424

## PCA and factor analysis

Principal component analysis (PCA) and factor analysis are used to
reduce the dimensionality of a dataset. The `prcomp()` function fits a
PCA model to the data, and the `factanal()` function fits a factor
analysis model. Both functions work with the formula syntax, and the
`data` argument can be used to specify the data frame.

``` r
# PCA
pca_fit <- prcomp(~ grade + age + scholarship, data = data)

# summary of PCA
summary(pca_fit)
```

    ## Importance of components:
    ##                            PC1     PC2     PC3
    ## Standard deviation     19.4919 1.90658 1.24411
    ## Proportion of Variance  0.9865 0.00944 0.00402
    ## Cumulative Proportion   0.9865 0.99598 1.00000

``` r
# component loadings for each observation
tidy(pca_fit)
```

    ## # A tibble: 432 × 3
    ##    row      PC    value
    ##    <chr> <dbl>    <dbl>
    ##  1 1         1 -14.8   
    ##  2 1         2   1.05  
    ##  3 1         3  -1.01  
    ##  4 2         1 -14.8   
    ##  5 2         2   0.0756
    ##  6 2         3  -1.23  
    ##  7 3         1 -14.8   
    ##  8 3         2   1.05  
    ##  9 3         3  -1.01  
    ## 10 4         1 -14.7   
    ## # ℹ 422 more rows

``` r
# Factor analysis
fa_fit <- factanal(~ grade + age + scholarship, factors = 1, data = data)

# summary of factor analysis
fa_fit
```

    ## 
    ## Call:
    ## factanal(x = ~grade + age + scholarship, factors = 1, data = data)
    ## 
    ## Uniquenesses:
    ##       grade         age scholarship 
    ##       0.965       0.005       0.888 
    ## 
    ## Loadings:
    ##             Factor1
    ## grade        0.188 
    ## age          0.997 
    ## scholarship -0.334 
    ## 
    ##                Factor1
    ## SS loadings      1.142
    ## Proportion Var   0.381
    ## 
    ## The degrees of freedom for the model is 0 and the fit was 0.0014

``` r
tidy(fa_fit)
```

    ## # A tibble: 3 × 3
    ##   variable    uniqueness    fl1
    ##   <chr>            <dbl>  <dbl>
    ## 1 grade            0.965  0.188
    ## 2 age              0.005  0.997
    ## 3 scholarship      0.888 -0.334
