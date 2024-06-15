---
title: "Data Center Apprenticeship:\nR basics: Hypothesis testing / modelling"
subtitle: "June 2024" 
date: "Last updated: 2024-06-15"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [t-tests](#t-tests)
- [Correlation test](#correlation-test)
- [Simple regression](#simple-regression)
- [Multiple regression](#multiple-regression)
- [ANOVA](#anova)
- [Chi-square test](#chi-square-test)
- [Logistic regression](#logistic-regression)
- [Non-parametric tests](#non-parametric-tests)
- [PCA and factor analysis](#pca-and-factor-analysis)
- [Repeated measures ANOVA](#repeated-measures-anova)
- [Go to](#go-to)

Most of the simple statistical tests are from base R, so they don’t rely
on tidy principles, but many are compatible with tidy workflows to at
least some extent. In the following we’ll cover some of the key methods
that show up in methods and statistics courses at UCR. In addition, the
`tidy()` function from the `broom` package converts most text output
into simple tibblesy, which are useful for exporting and visualizing
results.

# t-tests

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

# Correlation test

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

# Simple regression

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

# Multiple regression

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

# ANOVA

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

# Chi-square test

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

# Logistic regression

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

# Non-parametric tests

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

# PCA and factor analysis

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

# Repeated measures ANOVA

Repeated measures ANOVA is used when the same subjects are measured
multiple times. The `aov()` function can be used to fit these models,
with the formula syntax `y ~ x + Error(subject)`, where `y` is the
dependent variable, `x` is the independent variable, and `subject` is
the repeated measure. The `Error()` function specifies the repeated
measure, and the `data` argument can be used to specify the data frame.

``` r
# repeated measures ANOVA
# redefine data to have two observations per person, the second with random noise added to the grade
bind_rows(data,
          data %>% mutate(grade = grade + rnorm(n()))) |>
  aov(grade ~ reading + Error(id), data = _)
```

    ## 
    ## Call:
    ## aov(formula = grade ~ reading + Error(id), data = bind_rows(data, 
    ##     data %>% mutate(grade = grade + rnorm(n()))))
    ## 
    ## Grand Mean: 2.826623
    ## 
    ## Stratum 1: id
    ## 
    ## Terms:
    ##                  reading
    ## Sum of Squares  53.42658
    ## Deg. of Freedom        1
    ## 
    ## Estimated effects are balanced
    ## 
    ## Stratum 2: Within
    ## 
    ## Terms:
    ##                  reading Residuals
    ## Sum of Squares    7.8886  573.2928
    ## Deg. of Freedom        1       287
    ## 
    ## Residual standard error: 1.413342
    ## Estimated effects are balanced

# Go to

- [Introduction to R](../intro)
- [Finding and importing data](../import)
- [Data cleaning with `dplyr`](../clean)
- [Summary statistics](../summary)
- [Data visualization with `ggplot2`](../ggplot)
