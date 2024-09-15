---
layout: page
title: "SCICOGN302 workshop I: <br>Additional material"
subtitle: "Fall 2024"
date: "Last updated: 2024-09-15"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Introduction](#introduction)
- [Computing new variables](#computing-new-variables)
  - [`mutate()`](#mutate)
  - [`group_by()` and `summarize()`](#group_by-and-summarize)
- [More materials](#more-materials)

# Introduction

This tutorial shows you how you can use the `mutate()`, `group_by()` and
`summarize()` functions to compute more complex measures of linguistic
development than what was shown in the Data Center workshop.

If you have not done so yet, load the three packages we used in the
workshop, and load Amy’s transcript data in the token-based format.

``` r
library(tidyverse)
library(tidytext)
library(childesr)

tok <- get_tokens(token = "*", collection = "Eng-NA", target_child = "Amy",
                  corpus = "VanKleeck", role = "target_child")
```

# Computing new variables

## `mutate()`

The `mutate()` function is useful when you want to create new variables
or overwrite existing ones. For instance, you can calculate the length
of each word Amy used by using the `str_length()` function, and assign
the result to a new variable called `length`. I use the `select()`
function to display only a subset of the variables, and the `head()`
function to display only the first 10 observations.

``` r
tok_length <- tok %>% 
  mutate(length = str_length(gloss)) 

tok_length %>% 
  select(id, gloss, stem, length) %>% 
  head(10)
```

    ## # A tibble: 10 × 4
    ##         id gloss  stem    length
    ##      <int> <chr>  <chr>    <int>
    ##  1 3499800 white  "white"      5
    ##  2 3499804 xxx    ""           3
    ##  3 3499886 hi     "hi"         2
    ##  4 3499906 xxx    ""           3
    ##  5 3499911 okay   "okay"       4
    ##  6 3499912 bye    "bye"        3
    ##  7 3499938 the    "the"        3
    ##  8 3499939 farmer "farm"       6
    ##  9 3499961 okay   "okay"       4
    ## 10 3499976 yeah   "yeah"       4

You can also use mutate to create indicator variables: the next example
uses a logical expression that is true if the word is a noun.

``` r
tok %>% 
  mutate(noun = part_of_speech == "n") %>% 
  select(id, gloss, stem, noun) %>% 
  head(10)
```

    ## # A tibble: 10 × 4
    ##         id gloss  stem    noun 
    ##      <int> <chr>  <chr>   <lgl>
    ##  1 3499800 white  "white" FALSE
    ##  2 3499804 xxx    ""      FALSE
    ##  3 3499886 hi     "hi"    FALSE
    ##  4 3499906 xxx    ""      FALSE
    ##  5 3499911 okay   "okay"  FALSE
    ##  6 3499912 bye    "bye"   FALSE
    ##  7 3499938 the    "the"   FALSE
    ##  8 3499939 farmer "farm"  TRUE 
    ##  9 3499961 okay   "okay"  FALSE
    ## 10 3499976 yeah   "yeah"  FALSE

## `group_by()` and `summarize()`

While `mutate()` creates a new variable for each row of the data,
`summarize()` collapses the data to a smaller dimension by applying a
function (e.g. `sum()` or `mean()`) to the data.

For instance, if you use `mutate()` to get the length of each word, you
can use `summarize()` to find the mean word length of all observations:

``` r
tok_length %>% 
  summarize(length = mean(length))
```

    ## # A tibble: 1 × 1
    ##   length
    ##    <dbl>
    ## 1   3.76

Amy’s average word in this data is almost 4 letters long.

You can also do more complex operations rather than averages. The
following example uses the indicator variable for nouns to calculate the
total fraction of nouns in Amy’s speech: we can use the `sum()` function
to get the total number of nouns (the TRUE/FALSE indicator is treated as
1/0), and divide that by the total number of observations `n()`.

``` r
tok %>% 
  mutate(noun = part_of_speech == "n") %>% 
  summarize(noun_prop = sum(noun)/n())
```

    ## # A tibble: 1 × 1
    ##   noun_prop
    ##       <dbl>
    ## 1     0.123

So approximately 12% of Amy’s words were nouns.

Sometimes you don’t want to aggregate the full dataset, but get
summaries per group, e.g. the average word length per transcript. You
can accomplish that by specifying one or more grouping variables with
the `group_by()` function before calling `summarize()`.

``` r
tok_length %>% 
  group_by(transcript_id) %>% 
  summarize(length = mean(length))
```

    ## # A tibble: 2 × 2
    ##   transcript_id length
    ##           <int>  <dbl>
    ## 1          4258   3.66
    ## 2          4259   3.81

# More materials

You can find a list of helpful links for data manipulation and
visualization
[here](https://ucrdatacenter.github.io/tutorials/links.html).
