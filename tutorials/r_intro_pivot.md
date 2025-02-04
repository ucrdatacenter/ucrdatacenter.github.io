---
layout: page
title: "Pivoting: data in wide and long format"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

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
    ##    country       `1995` `1996` `1997` `1998` `1999` `2000` `2001` `2002` `2003` `2004` `2005` `2006` `2007` `2008` `2009` `2010` `2011`
    ##    <chr>          <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
    ##  1 Afghanistan   1.76e7 1.84e7 1.90e7 1.95e7 2.00e7 2.06e7 2.13e7 2.22e7 2.31e7 2.40e7 2.49e7 2.56e7 2.63e7 2.70e7 2.77e7 2.84e7 2.91e7
    ##  2 Albania       3.36e6 3.34e6 3.33e6 3.33e6 3.32e6 3.30e6 3.29e6 3.26e6 3.24e6 3.22e6 3.20e6 3.18e6 3.17e6 3.16e6 3.15e6 3.15e6 3.15e6
    ##  3 Algeria       2.93e7 2.98e7 3.03e7 3.08e7 3.13e7 3.17e7 3.22e7 3.26e7 3.30e7 3.35e7 3.40e7 3.45e7 3.51e7 3.57e7 3.64e7 3.71e7 3.78e7
    ##  4 American Sam… 5.29e4 5.39e4 5.49e4 5.59e4 5.68e4 5.75e4 5.82e4 5.87e4 5.91e4 5.93e4 5.91e4 5.87e4 5.79e4 5.71e4 5.62e4 5.56e4 5.53e4
    ##  5 Andorra       6.39e4 6.43e4 6.41e4 6.38e4 6.41e4 6.54e4 6.80e4 7.16e4 7.56e4 7.91e4 8.12e4 8.19e4 8.13e4 8.00e4 7.87e4 7.79e4 7.79e4
    ##  6 Angola        1.21e7 1.25e7 1.28e7 1.31e7 1.35e7 1.39e7 1.44e7 1.49e7 1.54e7 1.60e7 1.65e7 1.71e7 1.77e7 1.83e7 1.89e7 1.95e7 2.02e7
    ##  7 Anguilla      9.81e3 1.01e4 1.03e4 1.05e4 1.08e4 1.11e4 1.14e4 1.17e4 1.20e4 1.23e4 1.26e4 1.29e4 1.31e4 1.34e4 1.36e4 1.38e4 1.40e4
    ##  8 Antigua and … 6.83e4 7.02e4 7.22e4 7.42e4 7.60e4 7.76e4 7.90e4 8.00e4 8.09e4 8.17e4 8.26e4 8.35e4 8.44e4 8.53e4 8.63e4 8.72e4 8.82e4
    ##  9 Argentina     3.48e7 3.53e7 3.57e7 3.61e7 3.65e7 3.69e7 3.73e7 3.76e7 3.80e7 3.83e7 3.86e7 3.90e7 3.93e7 3.97e7 4.00e7 4.04e7 4.07e7
    ## 10 Armenia       3.22e6 3.17e6 3.14e6 3.11e6 3.09e6 3.08e6 3.06e6 3.05e6 3.04e6 3.03e6 3.01e6 3.00e6 2.99e6 2.98e6 2.97e6 2.96e6 2.96e6
    ## # ℹ 209 more rows
    ## # ℹ 2 more variables: `2012` <dbl>, `2013` <dbl>

Alternatively, we can say that the unit of observation is the year, and
we have different variables for the population in every country in that
year.

``` r
# convert the data to wide format with year as the unit of observation
pivot_wider(population, names_from = country, values_from = population)
```

    ## # A tibble: 19 × 220
    ##     year Afghanistan Albania  Algeria `American Samoa` Andorra Angola Anguilla `Antigua and Barbuda` Argentina Armenia  Aruba Australia
    ##    <dbl>       <dbl>   <dbl>    <dbl>            <dbl>   <dbl>  <dbl>    <dbl>                 <dbl>     <dbl>   <dbl>  <dbl>     <dbl>
    ##  1  1995    17586073 3357858 29315463            52874   63854 1.21e7     9807                 68349  34833168 3223173  80326  18124234
    ##  2  1996    18415307 3341043 29845208            53926   64274 1.25e7    10063                 70245  35264070 3173425  83195  18339037
    ##  3  1997    19021226 3331317 30345466            54942   64090 1.28e7    10305                 72232  35690778 3137652  85447  18563442
    ##  4  1998    19496836 3325456 30820435            55899   63799 1.31e7    10545                 74206  36109342 3112958  87276  18794552
    ##  5  1999    19987071 3317941 31276295            56768   64084 1.35e7    10797                 76041  36514558 3093820  89004  19027438
    ##  6  2000    20595360 3304948 31719449            57522   65399 1.39e7    11071                 77648  36903067 3076098  90858  19259377
    ##  7  2001    21347782 3286084 32150198            58176   68000 1.44e7    11371                 78972  37273361 3059960  92894  19487257
    ##  8  2002    22202806 3263596 32572977            58729   71639 1.49e7    11693                 80030  37627545 3047002  94995  19714625
    ##  9  2003    23116142 3239385 33003442            59117   75643 1.54e7    12023                 80904  37970411 3036032  97015  19953121
    ## 10  2004    24018682 3216197 33461345            59262   79060 1.60e7    12342                 81718  38308779 3025652  98742  20218481
    ## 11  2005    24860855 3196130 33960903            59117   81223 1.65e7    12637                 82565  38647854 3014917 100031  20520736
    ## 12  2006    25631282 3179573 34507214            58652   81877 1.71e7    12903                 83467  38988923 3002911 100830  20865583
    ## 13  2007    26349243 3166222 35097043            57919   81292 1.77e7    13145                 84397  39331357 2989882 101219  21246274
    ## 14  2008    27032197 3156608 35725377            57053   79969 1.83e7    13365                 85349  39676083 2977488 101344  21645095
    ## 15  2009    27708187 3151185 36383302            56245   78659 1.89e7    13571                 86300  40023641 2968154 101418  22037143
    ## 16  2010    28397812 3150143 37062820            55636   77907 1.95e7    13768                 87233  40374224 2963496 101597  22404488
    ## 17  2011    29105480 3153883 37762962            55274   77865 2.02e7    13956                 88152  40728738 2964120 101932  22740536
    ## 18  2012    29824536 3162083 38481705            55128   78360 2.08e7    14132                 89069  41086927 2969081 102384  23050471
    ## 19  2013    30551674 3173271 39208194            55165   79218 2.15e7    14300                 89985  41446246 2976566 102911  23342553
    ## # ℹ 207 more variables: Austria <dbl>, Azerbaijan <dbl>, Bahamas <dbl>, Bahrain <dbl>, Bangladesh <dbl>, Barbados <dbl>,
    ## #   Belarus <dbl>, Belgium <dbl>, Belize <dbl>, Benin <dbl>, Bermuda <dbl>, Bhutan <dbl>, `Bolivia (Plurinational State of)` <dbl>,
    ## #   `Bonaire, Saint Eustatius and Saba` <dbl>, `Bosnia and Herzegovina` <dbl>, Botswana <dbl>, Brazil <dbl>,
    ## #   `British Virgin Islands` <dbl>, `Brunei Darussalam` <dbl>, Bulgaria <dbl>, `Burkina Faso` <dbl>, Burundi <dbl>,
    ## #   `Cabo Verde` <dbl>, Cambodia <dbl>, Cameroon <dbl>, Canada <dbl>, `Cayman Islands` <dbl>, `Central African Republic` <dbl>,
    ## #   Chad <dbl>, Chile <dbl>, China <dbl>, `China, Hong Kong SAR` <dbl>, `China, Macao SAR` <dbl>, Colombia <dbl>, Comoros <dbl>,
    ## #   Congo <dbl>, `Cook Islands` <dbl>, `Costa Rica` <dbl>, `Côte d'Ivoire` <dbl>, Croatia <dbl>, Cuba <dbl>, Curaçao <dbl>, …

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
