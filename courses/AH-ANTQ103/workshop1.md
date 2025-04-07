---
layout: page
title: "AH-ANTQ103: Workshop 1"
subtitle: "Spring 2025"
date: "Last updated: 2025-04-07"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [1. Navigating R studio](#navigating-r-studio)
- [2. Data types](#data-types)
- [3. Variables](#variables)
- [4. Vectors](#vectors)
- [5. Functions](#functions)
- [6. Packages](#packages)
- [7. The pipe operator](#the-pipe-operator)
- [8. The `filter()` function](#the-filter-function)
- [9. Comparison operators](#comparison-operators)
- [10. The `select()` function](#the-select-function)
- [11. The `mutate()` function](#the-mutate-function)
- [12. NA values](#na-values)
- [13. Loading external data](#loading-external-data)
- [Assignment 1](#assignment-1)
- [Assignment 2](#assignment-2)

## 1. Navigating R studio

At this point, we have installed R and RStudio, opened this file, and
are now met by four panels within the R interface.

The panel in the upper left corner, in which you read this file, is the
source panel, where you write your code. Beneath that is the console,
where the code is executed. We can also write code in this panel, but
for convenience we will always use R Scripts placed in the source panel.
At the top right, there is the environment panel. It contains an
overview of all data that R has stored in its memory. While we work,
this panel will fill up will all kinds of data that you create and save.
Lastly, in the lower right corner we have a panel for navigating files,
plots, help documents, and more useful things.

R can be used for many things, for example as a basic calculator. Try
the following operations. The output will be printed in the console
(below). Please note that adding spaces between the numbers and the
operators is not necessary, but it makes the code more readable.

``` r
1 + 1
1 - 1
2 * 3
4 / 2
2 ^ 3
```

## 2. Data types

In R we have different types of data that we should be aware of. The
most important ones for archaeologists are numbers, typically called a
double (`dbl`) and text data, represented by a so called string of
characters (`chr`). Whilst there are others, we will only focus on these
two for now.

## 3. Variables

Oftentimes we want to use a specific value of data later on again, in
which case you can easily assign variables to values in R. Try the
following operations. After running the code by clicking the green
triangle in the upper right corner of the grey box below, these
variables should be added to the environment panel. Now try assigning a
value to a variable yourself.

Note that the arrow can be typed by pressing Alt + -, as well as by
typing \<- manually.

``` r
a <- 1
b <- 5
c <- "Word"
d <- "This is a whole sentence"
```

We can now also use our variables in the calculations instead of the
numbers. Try the following operations.

``` r
a + b
a - b
a * b
a / b
a ^ b
```

However, we should not be able to add these two variables together,
since we defined them as text data, and R does not know how to add two
strings together

``` r
c + d
```

## 4. Vectors

Next to variables, we will often encounter vectors, which can be
described as groups of values and which can be created with `c()`.

``` r
x <- c(21, 23, 20, 23)
y <- c("Paul", "John", "George", "Ringo")
```

## 5. Functions

We can now do small calculations with numbers in R, but there is a lot
more to do once we make use of functions! Functions can automate
standard tasks such as calculating an average and thus spare you a lot
of time, but they can also take over more complex tasks that we can only
do thanks to the function and someone who took the time to code them.

Try out the functions below on the vector we have just defined and find
out what they do.

``` r
mean(x)
length(x)
unique(c(x, x))
```

These are examples of very easy functions in R. Oftentimes functions
require more input and information about their `arguments`, which are
parts of a function that further change and define the output.

## 6. Packages

Up to this point, we have only installed base R, which includes basic
features and functions but remains fairly limited. R packages, which are
collections of elaborate functions and datasets, expand the capabilities
of base R. There are packages for all kinds of disciplines, also for
specific archaeological purposes like the `tesselle` packages that aim
at making archaeological data science more accessible. For most purposes
however, the `tidyverse` package stands out as the most essential, as it
equips R for modern data science tasks. Execute the code below to
install the `tidyverse` and `rio` packages, but do not worry about the
latter one for now, we will come back to it later.

``` r
install.packages("tidyverse")
install.packages("rio")
```

We only have to install packages once, but we have to load them using
the `library` function each time we start a new R session.

``` r
library(tidyverse)
library(rio)
```

With new packages come new functions and we can easily loose track of
what each function can do. Luckily we have some guidance built right
into R in the form of `helpfiles`. By typing in a question mark followed
by a function, a window with useful information opens up.

``` r
?sum
```

We can now use data structures from the tidyverse package. For example,
we can create a tibble, a specialized type of data frame that you can
view as a table for simplicity.

``` r
data <- tibble(
  name = c("Paul", "John", "George", "Ringo"),
  age = c(21, 23, 20, 23)
)
```

We then have several ways to look at the data. Try them out!

``` r
print(data)
View(data)
glimpse(data)
```

If we have a data set focussed on numeric variables, we can use the
`summary` function to get a quick overview of the data.

``` r
summary(data)
```

Alternatively, we can also try and click on our `data` in the
environment panel, which automatically opens the tibble up and spares
you from writing some additional code.

## 7. The pipe operator

The pipe operator is a very useful operator in R. It allows us to chain
together multiple operations. This makes our code more readable. It can
be typed by pressing Ctrl + Shift + M, as well as by typing `|>`
manually.

``` r
data |> 
  View()
```

Using the pipe may seem a bit counter-intuitive at the beginning. If you
want to better understand how it works, think of it as putting what is
in front of the pipe as the first argument into whatever follows the
pipe.

## 8. The `filter()` function

The `filter()` function allows us to filter rows in a tibble. This is
useful for selecting a subset of the data.

``` r
data |> 
  filter(age > 20)
```

The `filter()` function is a typical example of a function that has more
than just one input:

The first argument is the tibble (Inserted here by the pipe operator).
The second argument is the condition (the age must be larger than 20).
The condition is a logical vector, which means that it is a vector of
`TRUE` and `FALSE` values. `TRUE` means that the row is kept, and
`FALSE` means that the row is removed.

## 9. Comparison operators

We just used the comparison operator `>`, but there are also `<`, `==`,
`<=`, `>=`, `!=` which can be used in any logical condition. Try the
different operators out to see what they do!

``` r
x <  21
x == 21
x <= 21
x >= 21
x != 21
```

## 10. The `select()` function

The `select()` function allows us to select columns in a tibble. This is
useful for selecting a subset of the data.

``` r
data |> 
  select(name)
```

## 11. The `mutate()` function

The `mutate()` function allows us to create new columns in a tibble.
This is very useful for creating new variables.

``` r
data |>
  mutate(age_squared = age ^ 2)
```

Note that we have seen the use of both `==` and `=` in different
contexts. The simple `=` is often used to assign or define, like here in
the mutate function, whereas `==` is strictly used as a comparison
operator.

## 12. NA values

`NA` values are missing values. These are values that are not defined.
When you want to enter a `NA` value into your data, simply type it like
any other value.

``` r
data <- tibble(
  name = c("Paul", "John", "George", "Ringo"),
  age = c(21, NA, 20, 23)
)
```

If we want to know what rows contain `NA` values, we can use the
`is.na()` function inside the `filter()`.

``` r
data |>
  filter(is.na(age))
```

This is a good example how we can combine different functions. The
`is.na()` function checks the `age` column (a vector) for NA values and
returns TRUE if it finds one, and FALSE if not. The `filter()` function
automatically filters out all values that are not TRUE, in our example
every row that does not contain a NA value for `age`.

We do not require an operational operator here, because the `is.na()`
function already returns the logical condition the `filter()` requires.

Using the “not” operator (`!`) we could also filter rows that do not
contain `NA` values.

``` r
data |> 
  filter(!is.na(age))
```

## 13. Loading external data

Data link [here](https://bit.ly/j_data_xlsx).

There are two main ways of loading in external data in R, either
directly through a link from the internet or by storing the data in the
same place as your code and then specifying the path to the file.

In both cases, we need to use the appropriate R function for the type of
file that we will be loading in, so that R knows how the rows and
columns are separated in the original file. In our case we want to load
an Excel file from a link, so we will use the `import` function from the
`rio` package we installed earlier. Try loading the data from the link
using the code below. We assign our new data set the name `data`.

``` r
data <- import("https://bit.ly/j_data_xlsx", setclass = "tbl_df")
```

This first workshop has been quite heavy on basic Data Science in order
for you to understand the basics, but you should always keep in mind how
you as researchers in archaeology can use these tools to derive
information useful to you.

## Assignment 1

1)  Based on the tibble we created in step 6, create a new column called
    age_in_20_years. This should contain the age in 20 years.

2)  Filter the tibble to only contain rows where the age in 20 years is
    greater than 40.

3)  Display the resulting tibble.

## Assignment 2

1)  Using the data we loaded in in step 13 about lithics from the
    Jerimalai rockshelter in East-Timor, create a data set that only
    contains rows where the `Material` is `Chert` and give it a
    descriptive name.

2)  Create a second data set that should only contain rows using
    `Chalcedony` as a `Material`, also give this data set a descriptive
    name.

3)  Which data set has more rows?
