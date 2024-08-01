---
layout: page
title: "AH-ANTQ103: Workshop 1"
subtitle: "Spring 2024"
date: "Last updated: 2024-08-01"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Arithmetic](#arithmetic)
- [Variables](#variables)
- [Characters](#characters)
- [Tidyverse](#tidyverse)
- [The pipe operator](#the-pipe-operator)
- [The filter function](#the-filter-function)
- [The select function](#the-select-function)
- [The mutate function](#the-mutate-function)
- [NA values](#na-values)
- [Loading external data](#loading-external-data)
- [Assignment 1](#assignment-1)
- [Assignment 2](#assignment-2)

## Arithmetic

We will start with some basic operations in R. For further information,
please refer to the R documentation.

R can be used as a basic calculator. Try the following operations. The
output will be printed in the console (below). Please note that adding
spaces between the numbers and the operators is not necessary, but it
makes the code more readable.

``` r
1 + 1
1 - 1
2 * 3
4 / 2
2 ^ 3
```

Here we also take some time to introduce the notation in this file. The
grey blocks are the code blocks. You can run this either in the console
or in a script.

## Variables

R can also be used to assign values to variables. Try the following
operations. These variables should be added to the environment pane (On
the right) Assign the value 1 to the variable x. Note that the arrow can
be typed by pressing Alt + -, as well as by typing \<- manually. Also
assign the value 2 to the variable y.

``` r
x <- 1
y <- 2
```

We can now use these variables in calculations. Try the following
operations.

``` r
x + y
x - y
x * y
x / y
x ^ y
```

Sometimes it is useful to find the sum of a vector. We can do this using
the `sum` function.

``` r
sum(x)
```

We can also find the mean and length of a vector, as well as the unique
values in a vector.

``` r
mean(x)
length(x)
unique(c(x, x))
```

If you want to learn more about a function, you can add a question mark
before the function name. This will open the documentation for the
function (In the bottom right). At the bottom of the documentation,
there are some examples of how to use the function.

``` r
?sum
```

## Characters

R can also assign characters or strings to variables or vectors. Assign
the string “Hello” to the variable x and the string “World” to the
variable y.

``` r
x <- "Hello"
y <- "World"
```

When we try adding these two words together, we get an error. This is
because R does not know how to add strings together.

``` r
x + y
```

We can concatenate strings together using the paste function. Note that
the default separator is a space.

``` r
paste(x, y)
```

We can also concatenate strings together using the c function. This,
however, creates a vector of characters instead of a single string.

``` r
c(x, y)
```

## Tidyverse

At UCR, we generally use the tidyverse package a lot to manipulate data.
This package contains a lot of useful functions for data manipulation.
We can install the tidyverse package using the `install.packages`
function. You should only need to run this once. If you rerun this
notebook, you can comment out this line using a hashtag.

``` r
install.packages("tidyverse")
```

We can load the tidyverse package using the `library` function. This
should be run every time you start a new R session.

``` r
library(tidyverse)
```

We can now use data structures from the tidyverse package. For example,
we can create a tibble, which is a type of data frame.

``` r
tibble <- tibble(
  name = c("John", "Jane", "Joe"),
  age = c(20, 21, 22)
)
```

Alternatively, we can first create a vector for each column, and then
use the `tibble` function to create the tibble.

``` r
name <- c("John", "Jane", "Joe")
age <- c(20, 21, 22)
tibble <- tibble(name, age)
```

We then have several ways to look at the data. Try them out!

``` r
tibble
print(tibble)
View(tibble)
glimpse(tibble)
```

If we have a dataset with a lot of numeric variables, we can use the
`summary` function to get a quick overview of the data.

``` r
summary(tibble)
```

If we want to look at a specific column, we can use the `$` operator.
This will return a vector.

``` r
tibble$name
```

## The pipe operator

The pipe operator is a very useful operator in R. It allows us to chain
together multiple operations. This makes our code more readable. It can
be typed by pressing Ctrl + Shift + M, as well as by typing `%>%`
manually.

``` r
tibble %>% 
  View()
```

## The filter function

The `filter` function allows us to filter rows in a tibble. This is
useful for selecting a subset of the data.

``` r
tibble %>% 
  filter(age > 20) %>% 
  print()
```

The inner workings of the filter function are as follows. The first
argument is the tibble. The second argument is the condition. The
condition is a logical vector. This means that it is a vector of `TRUE`
and `FALSE` values. `TRUE` means that the row is kept, and `FALSE` means
that the row is removed. This shows the logical vector that is actually
used by the filter function. Rows with `TRUE` are kept and rows with
`FALSE` are removed.

``` r
tibble$age > 20
```

It is also possible to filter on strings. This is done using the `==`
operator.

``` r
tibble %>% 
  filter(name == "John") %>% 
  print()
```

## The select function

The `select` function allows us to select columns in a tibble. This is
useful for selecting a subset of the data.

``` r
tibble %>% 
  select(name) %>% 
  print()
```

## The mutate function

The `mutate` function allows us to create new columns in a tibble. This
is useful for creating new variables.

``` r
tibble %>% 
  mutate(age_squared = age ^ 2) %>% 
  View()
```

## NA values

`NA` values are missing values. These are values that are not defined.
We can create `NA` values using the `NA` function.

``` r
NA
```

We can also create `NA` values in a tibble.

``` r
tibble <- tibble(
  name = c("John", "Jane", "Joe"),
  age = c(20, NA, 22)
)
```

We can then use the `print` function to print the tibble.

``` r
print(tibble)
```

If we want to know what rows contain `NA` values, we can use the `is.na`
function inside the `filter`. Note that this returns a vector of `TRUE`
and `FALSE` values. TRUE means that the value is `NA`, and `FALSE` means
that the value is not `NA`.

``` r
is.na(tibble$age)
```

This means we can use `is.na()` inside the `filter` function to filter
rows that contain NA values. Note that we define the column age in the
`is.na()` function as we want R to check for NA values in the age
column.

``` r
tibble %>% 
  filter(is.na(age)) %>% 
  View()
```

Then using the “not” operator (`!`) we can filter rows that do not
contain `NA` values.

``` r
tibble %>% 
  filter(!is.na(age)) %>% 
  View()
```

We can also use the `drop_na()` function to drop rows that contain `NA`
values.

``` r
tibble %>% 
  drop_na() %>% 
  View()
```

A difference between the `is.na()` function and the `drop_na()` function
is that in `is.na()` you must specify the column, but in `drop_na()` you
do not need to specify the column (Do keep in mind that this will thus
remove all rows that have an `NA` in any column).

``` r
tibble %>% 
  filter(!is.na()) %>% 
  View()

tibble %>%
  drop_na() %>%
  View()
```

## Loading external data

Data link
[here](https://www.carc.ox.ac.uk/XDB/ASP/searchOpen.asp?setResultCheckboxes=chkAlbum&chkAlbum=true&windowWidth=1535&windowHeight=689&search=%20%7BAND%7D%20%20%5BProvenance%5D%20GREECE%2C%20ATHENS%2C%20AGORA#aHeader).

We can then use the read_csv function to read the data from a URL and
save it as a variable. This will return a tibble. We assign it to the
variable `data`.

``` r
data <- read_csv("https://github.com/ucrdatacenter/projects/raw/main/AH-ANTQ103/2024h1/Beazley_Archive.csv")
```

## Assignment 1

Based on the tibble we created in step 5, create a new column called
age_in_20_years. This should contain the age in 20 years. Then, filter
the tibble to only contain rows where the age in 20 years is greater
than 40. Finally, print the tibble.

## Assignment 2

Assignment 2: Using the Beazley Archive data, create two new data sets.
Give them descriptive names. The first data set should only contain rows
using an Athenian fabric (So without any specifics).

The second data set should only contain rows using Geometric Athenian
fabrics. Which data set has more rows?
