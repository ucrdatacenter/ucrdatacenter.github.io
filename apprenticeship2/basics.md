---
title: "Data Center Apprenticeship:\nR basics (1)"
subtitle: "June 2024" 
date: "Last updated: 2024-06-12"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Installation and setup: prepare in
  advance](#installation-and-setup-prepare-in-advance)
- [Objectives](#objectives)
  - [Why R?](#why-r)
  - [Why `tidyverse`?](#why-tidyverse)
- [Data analysis workflow](#data-analysis-workflow)
  - [Finding and importing data](#finding-and-importing-data)
  - [Data wrangling with `dplyr`](#data-wrangling-with-dplyr)
  - [Exploratory analysis](#exploratory-analysis)
    - [Summary statistics](#summary-statistics)
    - [Data visualization with
      `ggplot2`](#data-visualization-with-ggplot2)
  - [Hypothesis testing / modelling](#hypothesis-testing-modelling)
- [Other resources](#other-resources)

# Installation and setup: prepare in advance

Before the first workshop, please make sure that you have access to R
and RStudio on your laptop. If you need help with the installation,
please follow [this tutorial](../../tutorial/r_install).

The [R basics tutorial](../../tutorial/r_basics) on the Data Center
website explains the RStudio interface, and shows how to create a new
project and how to install packages. Please make sure that you are
familiar with these features before the workshop, and complete the
following:

- create a new project for your apprenticeship work;
- install the `tidyverse` package.

# Objectives

## Why R?

## Why `tidyverse`?

# Data analysis workflow

## Finding and importing data

## Data wrangling with `dplyr`

## Exploratory analysis

### Summary statistics

<!-- central tendencies, dispersion, correlation -->

### Data visualization with `ggplot2`

<!-- univariate, bivariate, densities -->

## Hypothesis testing / modelling

<!-- t-tests, simple regression, ANOVA, chi-square, correlation test  -->
<!-- multiple regression (+ extra specification), nonparametric tests, power and effect size, logistic regression, PCA, factor analysis, repeated measures ANOVA, mixed design ANOVA -->

# Other resources

<!-- ## Objects in R -->
<!-- One of the most basic types of objects in R is a vector.  -->
<!-- A vector is a collection of values of the same type, such as numbers, characters, or logicals (TRUE/FALSE).  -->
<!-- You can create a vector with the `c()` function, which stands for concatenate.  -->
<!-- If you assign a vector to an object with the assignment operator `<-`, your vector will be saved in your environment so you can work with it within your current R session. -->
<!-- Some examples of creating vectors are: -->
<!-- ```{r} -->
<!-- v1 <- c("A", "B", "C") -->
<!-- v2 <- 25 -->
<!-- v3 <- 1:10 -->
<!-- ``` -->
<!-- To subset or extract elements from a vector, you can use square brackets `[ ]` with an index. For example, `v1[1]` returns the first element of `v1`, `v3[2:5]` returns the 2nd to 5th elements of `v3`, and `v3[-c(2, 4, 6)]` returns all but the 2nd, 4th and 6th elements of `v3`. -->
<!-- ```{r} -->
<!-- v1[1] -->
<!-- v3[2:5] -->
<!-- v3[-c(2, 4, 6)] -->
<!-- ``` -->
<!-- A dataframe (or tibble in `tidyverse`) is a special type of object that combines vectors into a rectangular table.  -->
<!-- Each column of a dataframe is a vector, and each row is an observation.  -->
<!-- usually you would load data from an external source, but you can create a dataframe with the `data.frame()` and a tibble with the `tibble()` function. -->
<!-- You can also convert other data types such as matrices to tibbles with the `as_tibble()` function. -->
<!-- Both functions take vectors as their arguments. -->
<!-- Tibbles are preferred because they are more modern and have some convenient features that dataframes don't, but for the most part, differences are minor and for the most part it does not matter whether you work with tibbles or dataframes. -->
<!-- A simple example of creating a tibble is (make sure to load `tidyverse first`): -->
<!-- ```{r} -->
<!-- library(tidyverse) -->
<!-- # define vectors within the tibble() function -->
<!-- tibble( -->
<!--   name = c("Alice", "Bob", "Chris"), -->
<!--   height = c(165, 180, 175) -->
<!-- ) -->
<!-- # define the vectors first, then combine them into a tibble -->
<!-- name <- c("Alice", "Bob", "Chris") -->
<!-- height <- c(165, 180, 175) -->
<!-- tibble(name, height) -->
<!-- ``` -->
<!-- ## Functions in R -->
<!-- Functions are reusable pieces of code that perform a specific task.  -->
<!-- They take arguments as inputs and return one or more pieces of output.  -->
<!-- You will mostly work with functions loaded from various packages or from the base R distribution, and in some cases you may write your own functions to avoid repetition or improve the readability of your code. -->
<!-- We will cover writing your own functions later in the program. -->
<!-- As with vectors, the output of a function is saved to your environment only if you assign the result to an object. -->
<!-- For example, `sum(x)` will display the sum of the elements of the vector `x`, but `sum <- sum(x)` will save this result to an object. -->
<!-- ```{r} -->
<!-- x <- c(1, 5, 6, 2, 1, 8) -->
<!-- sum(x) -->
<!-- sum <- sum(x) -->
<!-- ``` -->
<!-- Some important functions on vectors are -->
<!-- ```{r} -->
<!-- mean(x) # return the mean; add the argument na.rm = TRUE if missing values should be excluded -->
<!-- length(x) # give the length of the vector (number of elements) -->
<!-- unique(x) # list the unique elements of the vector -->
<!-- ``` -->
<!-- To learn more about a function and its arguments, you can use the ? operator or the help() function, for example by typing `?sum` (or equivalently, `?sum()`). -->
<!-- It is good practice to request help files from your console and not you R script, since there is no need to save these queries for the future. -->
<!-- # Data wrangling in R -->
<!-- ## Importing data -->
<!-- In the following we will be working with a dataset on animal species diversity and weights. -->
<!-- You can load this data directly from [this link](https://raw.githubusercontent.com/ucrdatacenter/projects/main/apprenticeship/1_intro/surveys.csv) by pasting the URL as the argument of the `read_csv()` function (make sure you loaded `tidyverse` in your current R session). -->
<!-- Pay attention to the quotation marks around the URL so R treats the URL as a character string to parse, and not an object defined in the R environment. -->
<!-- ```{r} -->
<!-- surveys <- read_csv("https://raw.githubusercontent.com/ucrdatacenter/projects/main/apprenticeship/1_intro/surveys.csv") -->
<!-- ``` -->
<!-- After importing the data, the `surveys` object will show up in your Environment tab. -->
<!-- If you click on the object name, the full dataset will be displayed in your data viewer. -->
<!-- Looking at your console, you can see that clicking on the object name automatically runs the `View(surveys)` function. -->
<!-- If you would like to get an overview of what variables are in your data, you can use the summary function that gives you information about each variable: -->
<!-- ```{r} -->
<!-- summary(surveys) -->
<!-- ``` -->
<!-- ## Data wrangling -->
<!-- To learn the basics of data wrangling using the `tidyverse` (in particular, the `dplyer` package within tidyverse), we follow Section 4 of the Data Carpentry course "Data Analysis and Visualization in R for Ecologists". -->
<!-- It uses the species data we imported in the previous section. -->
<!-- The tutorial covers  -->
<!-- * how to select a subset of the variables in a dataframe; -->
<!-- * how to filter observations based on logical conditions (e.g. only keep observations from a particular area or removing missing values); -->
<!-- * how to create new variables or transform existing ones; -->
<!-- * how to analyze and summarize data within groups; -->
<!-- * how to convert data from wide to long format and vice versa; -->
<!-- * how to organize the data wrangling process into a tidy workflow using pipes (`%>%` or `|>`). -->
<!-- Please use [this link](https://datacarpentry.org/R-ecology-lesson/03-dplyr.html) to follow the relevant part of the tutorial. -->
<!-- A few notes on the contents of the tutorial: -->
<!-- * The Data Carpentry workshop uses the pipe from the `magrittr` package (`%>%`). Now there is also an alternative, the base pipe (`|>`). For the most part, they are equivalent. In the workshops we will primarily use the base pipe. You can change your default pipe setting in RStudio -> Tools -> Global options -> Code -> Use native pipe operator. -->
<!-- * The tutorial shows you how to filter out missing variables by combining the `filter()` and `is.na()` functions. An alternative is to use the `drop_na()` function, especially if you would like to drop missing values from multiple or all variables. An example is below, showing how many observations remain in each case using the `nrow()` function. Note the character vector of variable names when using the `all_of()` selection helper function. -->
<!-- ```{r} -->
<!-- # drop all observations where at least one variable is missing -->
<!-- surveys |>  -->
<!--   drop_na() |>  -->
<!--   nrow() -->
<!-- # drop all observations where at least one of the listed variables is missing -->
<!-- surveys |>  -->
<!--   drop_na(weight, hindfoot_length) |>  -->
<!--   nrow() -->
<!-- ``` -->
