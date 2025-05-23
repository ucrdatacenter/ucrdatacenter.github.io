---
layout: page
title: "The tidy workflow"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

## Video tutorial

Please watch [this](https://vimeo.com/1011770499/c07ee2b5c7) video
(4:03), then read and follow along with the written tutorial below.
Compare your own output to what you see printed below to make sure all
of your code runs as expected.

## Introduction

In this tutorial, we introduce you to the tidy workflow, a set of
principles and tools that help you work with data in a structured and
efficient way. This workflow allows you to combine different steps of
the data cleaning process into a single pipeline, making your code more
readable and easier to maintain.

This tutorial shows you an example of how to use the tidy workflow on
the `diamonds` dataset, which comes pre-loaded with the `tidyverse`
package.

Let’s load the `tidyverse` package and have a look at the `diamonds`
dataset:

``` r
# load tidyverse
library(tidyverse)

# add diamonds to the environment
data(diamonds)
```

## Establishing the data cleaning steps

Data files downloaded from online sources are not always in a convenient
format for analysis in R: variable names are not always intuitive or
consistent, you might need to make some additional calculations, recode
variables, or remove some variables/observations. You can make a list of
steps you need to take by observing the structure of your data.

You can view your data with the function `View(dataname)`, while the
output of `summary(dataname)` shows you all the variables you have,
including their type and some details about their content. In case you
need more information about what your variables mean, you can look at
the documentation on website of your data source (or the help file if
you use data from a package).

Our goal now is to look at only Ideal cut diamonds and compare the carat
and price of these diamonds.

## The tidy workflow

A useful tool to keep in mind when planning your data cleaning is the
pipe operator `|>` (keyboard shortcut: Ctrl/Command+Shift+M). The base
pipe operator `|>` does the same as the tidyverse pipe operator `%>%` –
you can use either one.

Using it in a workflow means that the next function uses the previous
result as an input, and helps you work in a linear fashion. E.g. “first
I need to take the diamonds tibble and filter observations for which the
cut is Ideal, then I need to select only the carat and price variables,
and then I need to calculate the price per carat”.

To work with the result of the pipeline, you can assign it to a new
object, e.g. `diamonds_new`. The `filter()`, `select()`, and `mutate()`
functions are data cleaning functions from `tidyverse`, introduced in
other tutorials ([`filter` and `select`](../r_intro_subset);
[`mutate`](../r_intro_mutate)), which all take the input dataset as the
first argument that we replace with the pipe here.

``` r
# start with the diamonds tibble
diamonds_new <- diamonds |>
  # filter the data for Ideal cut diamonds
  filter(cut == "Ideal") |>
  # select only the carat and price variables
  select(carat, price) |>
  # calculate the price per carat
  mutate(price_per_carat = price / carat)
```

This code is equivalent to the following:

``` r
# create a new object with the filtered data
diamonds_ideal <- filter(diamonds, cut == "Ideal")

# select only the carat and price variables
diamonds_selected <- select(diamonds_ideal, carat, price)

# calculate the price per carat
diamonds_new <- mutate(diamonds_selected, price_per_carat = price / carat)
```

The tidy workflow allows you to combine these steps into a single
pipeline, so you don’t need to create intermediate objects.

<!-- ## Video tutorial TBA -->
