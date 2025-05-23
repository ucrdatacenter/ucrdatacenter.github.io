---
layout: page
title: "Importing data"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

## Loading data from a package

There are many datasets that are loaded into R by default or when you
load different packages. If you start typing the name of a dataset that
is loaded into R, RStudio will suggest the full name of the dataset; to
see all options from loaded packages, type `data()` in the console. You
can work with these datasets without any additional steps, as they are
already loaded into R; just refer to the name of the dataset.

You can use a pre-defined dataset by calling its name directy, but if
you would like the dataset to be stored as an object in your
environment, you can use the `data()` function or assign the dataset to
a new object.

``` r
data(iris)
mydata <- iris
```

These default datasets are useful for learning and practicing, but they
are not always relevant to your research. Most of the time, you will be
working with your own data, which you need to import into R from a file
on your computer or the internet. In the following sections, you will
learn how to import data from a few different file formats.

## Importing a CSV file

CSV files are a convenient and simple way of storing data. CSV stands
for “comma-separated values”: the raw data is text file where each line
of text is a row of data, and values within a row are separated by
commas. In most cases your computer will automatically open CSV files in
Excel, where they are displayed as a table. CSV files are the most
common and also one of the easiest to import to R.

In order to import a CSV file to R, open your project in RStudio. In the
top left corner of RStudio find and click *File*$\to$*Import
dataset*$\to$*From Text (readr)*. In the data import pop-up window click
“Browse” and find the GDP data that you moved to your project folder.
RStudio will try to automatically detect the format of your data: the
result of that is shown in the Data preview window. If something looks
wrong, try changing some of the settings below the data preview until
the preview looks correct. Additionally, you should change the name of
the data to something sensible, e.g. `GDP`. Once all the settings are
ready, you can copy the contents of the “Code preview” window into your
script, and use it to import your data. As long as you start your script
by loading the `tidyverse` library, you don’t need to copy
`library(readr)`, as `readr` is a part of `tidyverse`.

## Importing an Excel file

Importing an Excel file follows the same process as a CSV file, but you
should choose *From Excel* instead of *From Text (readr)* when importing
the dataset. Every following step is the same, except that you do need
to load the `readxl` package separately, as it is not a part of
`tidyverse`.

## Other data formats

You can also use the *Import dataset* button for importing SPSS, Stata,
and SAV data files. If you are interested in importing even more file
types and writing your own import code, consider following [this
tutorial](https://ucrdatacenter.github.io/tutorial/r_adv_import.html).

## Video tutorial TBA
