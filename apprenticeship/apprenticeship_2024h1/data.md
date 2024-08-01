---
title: "Data Center Apprenticeship:\nWorking with external data"
subtitle: "Spring 2024" 
date: "Last updated: 2024-08-01"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Introduction](#introduction)
- [Importing and exporting different file
  formats](#importing-and-exporting-different-file-formats)
  - [Packages](#packages)
  - [Examples](#examples)
  - [Exporting data](#exporting-data)
- [Combining dataframes](#combining-dataframes)
  - [Row and column binding](#row-and-column-binding)
  - [Joins](#joins)

<!-- Note: to compile this file, download the data zip file from projects and extract the contents to ucrdatacenter.github.io/apprenticeship/data (that folder is in the gitignore to prevent uploading data files to the website repo). -->

*Schedule:*

- any unfinished data wrangling
- ~1 hour: import/export
- ~1 hour: combining data
- anything else to add here?

# Introduction

This tutorial covers how to work with various types of external data in
R. First we cover how to import and export from and to various file
formats, and discuss a number of available packages to do so. Then
you’ll also learn multiple ways of combining data from different sources
into a single tibble.

# Importing and exporting different file formats

Start by reading the introduction to importing the most common file
types (text files, Excel, SPSS, Stata)
[here](../../../tutorials/r_data). It is good to be aware of the Import
dataset button and use it when needed, but in the long run it is easier
to be aware of the available import functions and use them directly.

The following gives more information and examples of importing data from
different file formats and different levels of tidiness.

## Packages

We will use the following packages for importing different file formats:

``` r
library(tidyverse)
library(readxl) # for Excel
library(haven) # for SPSS, Stata, SAS
library(jsonlite) # for JSON
library(arrow) # for parquet, big data
```

The `rio` package provides a generic import function, however, it is
simply a wrapper for many of the other import functions shown below.
While it may be easier to use the same import function for many file
formats, `rio` redirects you to the original functions if you look for
the possible function arguments. In some cases, `rio` can read URL file
paths that `readr` (the `tidyverse` package for data import) can’t.

## Examples

First, download
[this](https://github.com/ucrdatacenter/projects/tree/main/apprenticeship/2024h1/2_data)
zip-file from GitHub, and extract it into a data folder within your
apprenticeship project directory. We now import each file, explaining
the packages, functions, and function arguments used. These files are
all versions of the same dataset on student characteristics and grades
at a university ([original
source](https://www.kaggle.com/datasets/jacksondivakarr/student-classification-dataset?resource=download)),
each with only a subset of the observations and/or variables.

`student_male.csv` is a comma-separated text file. Opening it in a
notepad or Excel shows that the column separators are semicolons (;).
The `read_csv()` function from the previous workshop expects commas as a
separator, while `read_csv()` expects semicolons. Since CSV files are a
form of delimited text files, we can also use the more versatile
`read_delim()` function specifying the delimiter as the argument.

If you open the file in Excel or load it in R without additional
arguments, you will see that the file does not contain variable names,
so R treats the first observation as the variable names. To disable this
behavior, you can use the `col_names` argument to either specify that
the data does not contain variable names (`col_names = FALSE`), in which
case R will assign automatic names, or you can specify a vector of names
to use.

``` r
student_male <- read_csv2("data/student_male.csv", col_names = FALSE)
student_male <- read_delim("data/student_male.csv", delim = ";", col_names = FALSE)
```

`student_female.tab` is also a delimited text file. Opening it in a
notepad shows that the delimiter is a tab. The notation for tab
whitespace is `\t`, which we can specify in the `delim` argument. Like
the “.tab” file extension, “.tsv” is also a tab-separated text file, so
the more specialized `read_tsv()` function also works.

If you load this data without arguments, you’ll see that the
`Scholarship` variable is treated as a number, while in the previous
data it was a character due to the percentage signs and the occurrence
of “None” as a value. You can use the `col_types` argument to specify a
string that shows the type of each variable. In the example below “i”
stands for integer columns and “c” for character columns. Often,
especially if there are many columns but only a few have an incorrect
type, it is easier to change the variable type with a `mutate()`
function after importing.

``` r
student_female <- read_delim("data/student_female.tab", delim = "\t",
                             col_types = c("iicccccciccccc"))
student_female <- read_tsv("data/student_female.tab", col_types = c("iicccccciccccc"))
```

To import Excel file we need the `read_excel()` function from the
`read_excel()` package. With this function you can specify which sheet
to use in addition to similar arguments as for delimited text files.
Notice that by default R imports the “Metadata” sheet, so we can use the
`sheet` argument to specify which sheet to import. In addition, the
first two rows contain introductory text, not the data, so we can use
the `skip` argument to skip those rows. You may also notice that
previously the variable name for age was `Student_Age` and now it is
`Student_age`. You can rename the variable either by giving a full list
of column names in the import function, but often it is easier to use
the `rename()` function after importing.

``` r
student_char_sports <- read_excel("data/student_char_sports.xlsx", 
                                  sheet = "Data", skip = 2) |> 
  rename("Student_Age" = Student_age)
```

The `haven` package reads files in the data formats of Stata (.dta) and
SPSS (.sav).

``` r
student_char1_A <- read_dta("data/student_char1_A.dta")
student_char2_A <- read_sav("data/student_char2_A.sav")
```

RDS is an R-specific file format that saves all attributes of the
dataframe as well (e.g. grouping, factor levels). It is particularly
useful for saving intermediate data files, e.g. saving the cleaned data
before analysis to avoid needing to run the data cleaning script
repeatedly.

``` r
student_age_grades_working <- read_rds("data/student_age_grades_working.rds")
```

JSON files (.json extension) are highly structured text files, and
therefore often used to store data. If you open a JSON file as a simple
text file, it can look quite confusing because it does not have the
standard table structure as the file formats we looked at so far.
Nevertheless, if you encounter a JSON file, you can import it to R as a
dataframe using the `jsonlite` package by specifying the file path as
the argument to the `fromJSON()` function:

``` r
student_grades <- fromJSON("data/student_grades.json")
```

parquet

When you work with very large datasets, even loading the data can take
considerable time. Therefore some file formats have been developed with
the aim of working with big data more efficient. One example of such a
file format is parquet, which is a tabular format like CSV, but with
some key differences that make it easier to work with big data (see [R
for Data Science (2e)](https://r4ds.hadley.nz/arrow#sec-parquet) for
more details). Parquet files are partitioned along some dimension of the
data, and all files corresponding to a datafile are stored in the same
folder. The `open_dataset()` function from the `arrow` package can load
all parquet files from one folder if you specify the host folder as the
function argument. In our case the student data (including some
simulated observations) is partitioned by student age:

``` r
student_more_id_age <- open_dataset("data/student_more_id_age")
```

## Exporting data

To export data from R, you can almost always use the `write_...()`
function corresponding to the desired file format, e.g. `write_csv()` or
`write_dta`. For Excel files the preferred export function is
`read_xlsx()`. For other file format the generic `write()` function is
useful; you can specify any file format, and if your input data is
readable in the chosen format, the file will export properly. In all
these `write_()` functions you need to specify the data you’d like to
save (often in a pipe workflow) and the output file path including
chosen file extension, with the exception of `write_dataset()` from
`arrow`, where you specify the path to be a (new) folder, and you add a
separate `format = "parquet"` argument. For example:

``` r
write_xlsx(student_male, "data/new_csv_data.csv")

student_female |> 
  write_dataset("data/new_parquet_data", format = "parquet")
```

A few notes regarding importing and exporting data:

- Always make sure you know your current working directory and the
  relative path to your data directory. It is better to use relative
  rather than absolute file paths (i.e. `data/data.csv` instead of
  `C:/User/Project/data/data.csv`).
- Note that if you are using Windows, you may need to replace the
  backslashes (\\ in the file path with forward slashes (/) to avoid
  errors.
- You can import files directly from URLs, although you often need the
  URL of a raw file. On Github you can access the URL of raw CSV files
  by clicking on the “Raw” button on the data preview page (see
  e.g. [here](https://github.com/ucrdatacenter/projects/blob/main/apprenticeship/1_intro/surveys.csv)).
  If a file downloads immediately instead of opening in a raw format,
  you can try to copy that download link by right-clicking and selecting
  “Copy link address”; the `import()` function from `rio` might be
  successful with those links.

# Combining dataframes

Now we use the previously imported dataframes to learn the different
ways of combining mutliple dataframes into one. Since many functions
take a single dataframe as an input (think plots, summary statistics,
statistical models), it is important to make sure that you choose the
right function to combine dataframes in a way that makes sure that each
row contains observations for the same case throughout the row.

## Row and column binding

The simplest way to combine dataframes is to use row or column binding.
These functions append new data to your old data by adding it as new
rows or new columns.

An example where row binding is useful is to combine the `student_male`
and `student_female` datasets. They both contain the same information on
students (same variables), but they include different subsets of the
data (one is only males, the other only females). So we can recreate the
full data by row-binding the two dataframes.

Notice that since the variable names don’t match, R can’t match up the
variables. Therefore we should start by fixing the variable names of
`student_male`: in this case we know it contains the exact same
variables as `student_female` does, so we can extract the names from
`student_female` and set that vector as the names of `student_male`:

``` r
data_full <- student_male |> 
  setNames(names(student_female)) |> 
  bind_rows(student_female)
```

Column binding, on the other hand, is useful if the two dataframes
include the (exact) same observations, but different variables. Use
column binding only if you are completely sure that both dataframes
contain information on the same cases in the same order. In our case,
the `student_char1_A` and `student_char2_A` contain characteristics of
all students who got an “AA” final grade, and the ordering of the
students is the same. The first dataframe contains numerical variables
and the second characters, so to get a full data of the characteristics
of all students who got “AA” grades, we can column-bind the dataframes:

``` r
data_char_A <- bind_cols(student_char1_A, student_char2_A)
```

Note that the `bind_rows()` and `bind_cols()` functions are the tidy
versions of the base functions `rbind()` and `cbind()`. For dataframes
the tidy versions have many advantages over the base functions, e.g. in
preserving variable names. For matrices, `rbind()` and `cbind()` are
still the preferred options, and the `c()` function also works for
combining vectors and lists.

## Joins

Mutating joins are a type of join operation that add columns from one
data frame to another, based on matching values of some variables. They
are useful when you want to combine information from different sources
without mismatching observations or duplicating rows. In most cases your
data contains an observation identifier that is consistent across data
sources and therefore can be used for matching observations (think
e.g. country codes or student numbers).

There are four types of mutating joins: inner join, left join, right
join, and full join. Their behavior differs only if there are unmatched
rows in either dataframe. In addition, anti-join uses the same syntax as
the other joins, but it is used to remove observations from a dataframe.

Full join retains all observations that appear in at least one of the
dataframes. In this case, `student_char_sports` contains the
characteristics of students who play sports, and
`student_age_grades_working` contains the age and grades of students who
work. Some students both play sports and work, but not all. The
resulting `data_work_or_sport_1` contains the data of all students who
play sports or work, however, for those who don’t work, we grades are
missing, and for those who don’t play sports, characteristics are
missing.

Also note that if you run the function without specifying the variable
on which to join, the function automatically joins by all variables with
the same name. In this case that is `Id` and `Student_Age`. If you
instead specify the `by` argument to join only by `Id`, then R will
retain both `Student_Age` variables as separate columns. You can also
specify a vector of variables to join by (see `data_work_or_sport_2`),
or, especially if the names of the identifier variable are different in
the two dataframes, you can connect the two variable names by an
equality sign (see `data_work_or_sport_3`). In our case the matching is
not affected by whether we only match on ID or also on age, but if there
are disparities between the dataframes or if a variable with the same
name has a different meaning, then not specifying the `by` argument
correctly can lead to mismatched observations. For now
`data_work_or_sport_2` and `data_work_or_sport_3` are equivalent, and
the only difference between them and `data_work_or_sport_1` is whether
the age variable gets duplicated.

``` r
data_work_or_sport_1 <- full_join(student_char_sports, student_age_grades_working) |> 
  arrange(Id)

data_work_or_sport_2 <- full_join(student_char_sports, student_age_grades_working, 
                                  by = "Id") |> 
  arrange(Id)

data_work_or_sport_3 <- full_join(student_char_sports, student_age_grades_working, 
                                  by = c("Id" = "Id")) |> 
  arrange(Id)
```

Inner join works the same way as full join, except it drops any
observations that do not appear in both datasets. In our case that means
that the resulting `data_work_and_sport` only contains the data of
students who both play a sport and work.

``` r
data_work_and_sport <- inner_join(student_char_sports, student_age_grades_working) |> 
  arrange(Id)
```

Left and right joins keep all observations from the first or second
dataframes respectively, and drop unmatched observations from the other
input dataframe. `left_join(data1, data2)` is equivalent to
`right_join(data2, data1)` (if needed also switching the order of the
`by` argument). The reason why one function might be preferred over the
other is if one of the inputs is the result of a longer pipe workflow,
because in that case you can simply append the join to the workflow
(like `data1 |> ... |> left_join(data2)`). In this example both joins
retain the characteristics of all students who play sports, and add the
grades of only the same students.

``` r
data_sport <- left_join(student_char_sports, student_grades)
data_sport <- right_join(student_grades, student_char_sports)
```

Unlike the other join functions, `anti_join()` is more similar to the
`filter()` function: it retains the observations of the first dataset
only if those identifiers don’t appear in the second dataset. Anti-joins
are particularly useful when observations are identified by the
combination of multiple variables (e.g. location and time). In this
example we retain the grades of students who don’t play sports by
anti-joining the dataframe of all grades with the characteristics of
students who play sports.

``` r
data_grades_nosport <- anti_join(student_grades, student_char_sports)
```

In addition to the examples above, you can find animations of which
observations are retained and dropped by each join function
[here](https://www.garrickadenbuie.com/project/tidyexplain/#mutating-joins).
