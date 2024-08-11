---
layout: page
title: "Pivoting: data in wide and long format"
date: "Last updated: 2024-08-11"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

**This tutorial is not ready yet. Please come back later.**

## Introduction

Tabular data can be stored in different formats. In tidy data, every row
is an observation and every column is a variable. But depending on what
you define as your observations and variables, you might need to pivot
your data from wide to long format or vice versa.

This tutorial introduces the concepts of long and wide format, and shows
you how to use the `pivot_longer()` and `pivot_wider()` functions from
`tidyverse` on the `population` dataset (which comes pre-loaded with
`tidyverse` so you don’t need to import it).

Let’s load the `tidyverse` package and have a look at the `population`
dataset:

``` r
# load tidyverse
library(tidyverse)

# add diamonds to the environment
data(population)
```

## Long and wide format

Compare the following two simple datasets:

Data A:

| year | country  | value |
|-----:|:---------|------:|
| 2022 | countryA |    10 |
| 2022 | countryB |    12 |
| 2023 | countryA |    14 |
| 2023 | countryB |    15 |

Data B:

| year | countryA | countryB |
|-----:|---------:|---------:|
| 2022 |       10 |       12 |
| 2023 |       14 |       15 |

Data A is in long format, and Data B is in wide format. The tables
contain the same information, but sometimes one format is more
convenient than the other.

You can convert between these two forms using the `pivot_longer()` (wide
to long) and `pivot_wider()` (long to wide) functions. To use
`pivot_longer()` you need to specify which columns you’d like to turn
into a single column: e.g. to go from Data B to Data A, you’d use the
argument `cols = c(countryA, countryB)` (or equivalently,
`cols = -year`). To use `pivot_wider()`, you need to specify which
column to use for variable names, and which column for variable values:
going from Data A to Data B would use the arguments
`names_from = country, values_from = value`.

## Pivoting on the `population` data

The `population` dataset contains the population of different countries
over time. Currently it is in long format: the identifying variables are
`country` and `year`, and the variable of interest is `population`.

We can convert it to wide format where the unit of observation is the
country, and we have different variables for the population in every
year.

``` r
# convert the data to wide format with country as the unit of observation
pivot_wider(population, names_from = year, values_from = population)
```

    ## # A tibble: 219 × 20
    ##    country `1995` `1996` `1997` `1998` `1999` `2000` `2001` `2002` `2003` `2004`
    ##    <chr>    <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
    ##  1 Afghan… 1.76e7 1.84e7 1.90e7 1.95e7 2.00e7 2.06e7 2.13e7 2.22e7 2.31e7 2.40e7
    ##  2 Albania 3.36e6 3.34e6 3.33e6 3.33e6 3.32e6 3.30e6 3.29e6 3.26e6 3.24e6 3.22e6
    ##  3 Algeria 2.93e7 2.98e7 3.03e7 3.08e7 3.13e7 3.17e7 3.22e7 3.26e7 3.30e7 3.35e7
    ##  4 Americ… 5.29e4 5.39e4 5.49e4 5.59e4 5.68e4 5.75e4 5.82e4 5.87e4 5.91e4 5.93e4
    ##  5 Andorra 6.39e4 6.43e4 6.41e4 6.38e4 6.41e4 6.54e4 6.80e4 7.16e4 7.56e4 7.91e4
    ##  6 Angola  1.21e7 1.25e7 1.28e7 1.31e7 1.35e7 1.39e7 1.44e7 1.49e7 1.54e7 1.60e7
    ##  7 Anguil… 9.81e3 1.01e4 1.03e4 1.05e4 1.08e4 1.11e4 1.14e4 1.17e4 1.20e4 1.23e4
    ##  8 Antigu… 6.83e4 7.02e4 7.22e4 7.42e4 7.60e4 7.76e4 7.90e4 8.00e4 8.09e4 8.17e4
    ##  9 Argent… 3.48e7 3.53e7 3.57e7 3.61e7 3.65e7 3.69e7 3.73e7 3.76e7 3.80e7 3.83e7
    ## 10 Armenia 3.22e6 3.17e6 3.14e6 3.11e6 3.09e6 3.08e6 3.06e6 3.05e6 3.04e6 3.03e6
    ## # ℹ 209 more rows
    ## # ℹ 9 more variables: `2005` <dbl>, `2006` <dbl>, `2007` <dbl>, `2008` <dbl>,
    ## #   `2009` <dbl>, `2010` <dbl>, `2011` <dbl>, `2012` <dbl>, `2013` <dbl>

Alternatively, we can say that the unit of observation is the year, and
we have different variables for the population in every country in that
year.

``` r
# convert the data to wide format with year as the unit of observation
pivot_wider(population, names_from = country, values_from = population)
```

    ## # A tibble: 19 × 220
    ##     year Afghanistan Albania  Algeria `American Samoa` Andorra   Angola Anguilla
    ##    <dbl>       <dbl>   <dbl>    <dbl>            <dbl>   <dbl>    <dbl>    <dbl>
    ##  1  1995    17586073 3357858 29315463            52874   63854 12104952     9807
    ##  2  1996    18415307 3341043 29845208            53926   64274 12451945    10063
    ##  3  1997    19021226 3331317 30345466            54942   64090 12791388    10305
    ##  4  1998    19496836 3325456 30820435            55899   63799 13137542    10545
    ##  5  1999    19987071 3317941 31276295            56768   64084 13510616    10797
    ##  6  2000    20595360 3304948 31719449            57522   65399 13924930    11071
    ##  7  2001    21347782 3286084 32150198            58176   68000 14385283    11371
    ##  8  2002    22202806 3263596 32572977            58729   71639 14886574    11693
    ##  9  2003    23116142 3239385 33003442            59117   75643 15421075    12023
    ## 10  2004    24018682 3216197 33461345            59262   79060 15976715    12342
    ## 11  2005    24860855 3196130 33960903            59117   81223 16544376    12637
    ## 12  2006    25631282 3179573 34507214            58652   81877 17122409    12903
    ## 13  2007    26349243 3166222 35097043            57919   81292 17712824    13145
    ## 14  2008    27032197 3156608 35725377            57053   79969 18314441    13365
    ## 15  2009    27708187 3151185 36383302            56245   78659 18926650    13571
    ## 16  2010    28397812 3150143 37062820            55636   77907 19549124    13768
    ## 17  2011    29105480 3153883 37762962            55274   77865 20180490    13956
    ## 18  2012    29824536 3162083 38481705            55128   78360 20820525    14132
    ## 19  2013    30551674 3173271 39208194            55165   79218 21471618    14300
    ## # ℹ 212 more variables: `Antigua and Barbuda` <dbl>, Argentina <dbl>,
    ## #   Armenia <dbl>, Aruba <dbl>, Australia <dbl>, Austria <dbl>,
    ## #   Azerbaijan <dbl>, Bahamas <dbl>, Bahrain <dbl>, Bangladesh <dbl>,
    ## #   Barbados <dbl>, Belarus <dbl>, Belgium <dbl>, Belize <dbl>, Benin <dbl>,
    ## #   Bermuda <dbl>, Bhutan <dbl>, `Bolivia (Plurinational State of)` <dbl>,
    ## #   `Bonaire, Saint Eustatius and Saba` <dbl>, `Bosnia and Herzegovina` <dbl>,
    ## #   Botswana <dbl>, Brazil <dbl>, `British Virgin Islands` <dbl>, …

These different formats can be useful for different types of analysis.

To be able to work with this new dataset, you need to save it to a new
object. Let’s assign the result of the `pivot_wider()` function (with
countries as the unit) to a new object called `population_wide`:

``` r
# save the result to a new object
population_wide <- pivot_wider(population, names_from = year, values_from = population)
```

Pivoting is reversible, so we can convert `population_wide` back to long
format with the `pivot_longer()` function. We want to take the years as
the variable names, and the population as the values – instead of
listing all years as the columns we want to convert, we can say that we
want to pivot all columns except `country`.

``` r
# convert the data back to long format
pivot_longer(population_wide, cols = -country, names_to = "year", values_to = "population")
```

    ## # A tibble: 4,161 × 3
    ##    country     year  population
    ##    <chr>       <chr>      <dbl>
    ##  1 Afghanistan 1995    17586073
    ##  2 Afghanistan 1996    18415307
    ##  3 Afghanistan 1997    19021226
    ##  4 Afghanistan 1998    19496836
    ##  5 Afghanistan 1999    19987071
    ##  6 Afghanistan 2000    20595360
    ##  7 Afghanistan 2001    21347782
    ##  8 Afghanistan 2002    22202806
    ##  9 Afghanistan 2003    23116142
    ## 10 Afghanistan 2004    24018682
    ## # ℹ 4,151 more rows

The `names_to` and `values_to` arguments are used to specify the names
of the new columns that will be created; they are optional, but can be
useful for clarity.

## Video tutorial TBA
