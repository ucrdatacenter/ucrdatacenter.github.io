---
title: "Data Center Apprenticeship:\nR basics"
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
- [Introduction to R](#introduction-to-r)
  - [Objects](#objects)
  - [Functions in R](#functions-in-r)
- [Data analysis workflow](#data-analysis-workflow)
  - [Finding and importing data](#finding-and-importing-data)
  - [Data wrangling with `dplyr`](#data-wrangling-with-dplyr)
    - [Merging datasets](#merging-datasets)
    - [Filtering observations](#filtering-observations)
    - [Selecting variables](#selecting-variables)
    - [Creating new variables](#creating-new-variables)
    - [Sorting the data](#sorting-the-data)
    - [Renaming variables](#renaming-variables)
    - [Data cleaning as a single
      pipeline](#data-cleaning-as-a-single-pipeline)
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

In addition the [R basics tutorial](../../tutorial/r_basics) on the Data
Center website explains the RStudio interface, and shows how to create a
new project and how to install packages.

As a first step, please create a new project in your preferred working
directory for these workshops, install the `tidyverse` package, and load
it in your R session at the top of a new script. Note that packages need
to be installed only once, but need to be loaded at the beginning of
each new R session.

``` r
# install.packages("tidyverse")
library(tidyverse)
```

# Objectives

## Why R?

- Free and open-source
- Reproducible
- Widely used in academia and industry; up-to-date with the latest
  technological developments
- Very versatile: extensive package ecosystem for statistics and more
- Powerful data wrangling and visualization capabilities
- Extensive community support (open-access books, tutorials, forums, AI
  tools, etc.)

## Why `tidyverse`?

- Clean, consistent, intuitive, readable syntax for all steps of the
  data analysis process
- Limited set of functions that can be combined in many ways
- Many packages beyond core `tidyverse` with the same underlying design,
  grammar, and data structures, therefore easier to learn advanced
  techniques

# Introduction to R

## Objects

One of the most basic types of objects in R is a vector. A vector is a
collection of values of the same type, such as numbers, characters, or
logicals (TRUE/FALSE). You can create a vector with the `c()` function,
which stands for concatenate. If you assign a vector to an object with
the assignment operator `<-`, your vector will be saved in your
environment so you can work with it within your current R session. Some
examples of creating vectors are:

``` r
v1 <- c("A", "B", "C")  # character vector with 3 elements
v2 <- 25                # numeric vector with 1 element
v3 <- 1:10              # numeric vector with 10 elements - integers from 1 to 10
```

To subset or extract elements from a vector, you can use square brackets
`[ ]` with an index. For example, `v1[1]` returns the first element of
`v1`, `v3[2:5]` returns the 2nd to 5th elements of `v3`, and
`v3[-c(2, 4, 6)]` returns all but the 2nd, 4th and 6th elements of `v3`.

``` r
v1[1]
```

    ## [1] "A"

``` r
v3[2:5]
```

    ## [1] 2 3 4 5

``` r
v3[-c(2, 4, 6)]
```

    ## [1]  1  3  5  7  8  9 10

A dataframe (or “tibble” in `tidyverse`) is a special type of object
that combines vectors into a rectangular table. Each column of a
dataframe is a vector, and each row is an observation. usually you would
load data from an external source, but you can create a dataframe with
the `data.frame()` and a tibble with the `tibble()` function. You can
also convert other data types such as matrices to tibbles with the
`as_tibble()` function. Both functions take vectors as their arguments.
Tibbles are preferred because they are more modern and have some
convenient features that dataframes don’t, but for the most part,
differences are minor and for the most part it does not matter whether
you work with tibbles or dataframes.

A simple example of creating a tibble is (make sure to load
`tidyverse first`):

``` r
library(tidyverse)

# define vectors within the tibble() function
tibble(
  name = c("Alice", "Bob", "Chris"),
  height = c(165, 180, 175)
)
```

    ## # A tibble: 3 × 2
    ##   name  height
    ##   <chr>  <dbl>
    ## 1 Alice    165
    ## 2 Bob      180
    ## 3 Chris    175

``` r
# define the vectors first, then combine them into a tibble
name <- c("Alice", "Bob", "Chris")
height <- c(165, 180, 175)
tibble(name, height)
```

    ## # A tibble: 3 × 2
    ##   name  height
    ##   <chr>  <dbl>
    ## 1 Alice    165
    ## 2 Bob      180
    ## 3 Chris    175

## Functions in R

Functions are reusable pieces of code that perform a specific task. They
take arguments as inputs and return one or more pieces of output. You
will mostly work with functions loaded from various packages or from the
base R distribution, and in some cases you may write your own functions
to avoid repetition or improve the readability of your code. We will
cover writing your own functions later in the program.

As with vectors, the output of a function is saved to your environment
only if you assign the result to an object. For example, `sum(x)` will
display the sum of the elements of the vector `x`, but `sum <- sum(x)`
will save this result to an object.

``` r
x <- c(1, 5, 6, 2, 1, 8)

sum(x)
```

    ## [1] 23

``` r
sum <- sum(x)
```

Some important functions on vectors are

``` r
mean(x)   # return the mean; add the argument na.rm = TRUE if missing values should be excluded
```

    ## [1] 3.833333

``` r
length(x) # give the length of the vector (number of elements)
```

    ## [1] 6

``` r
unique(x) # list the unique elements of the vector
```

    ## [1] 1 5 6 2 8

To learn more about a function and its arguments, you can use the ?
operator or the help() function, for example by typing `?sum` (or
equivalently, `?sum()`). It is good practice to request help files from
your console and not you R script, since there is no need to save these
queries for the future.

# Data analysis workflow

## Finding and importing data

R can handle practically any type of data, from simple text files to
files used by other (not necessarily open-source) software and complex
databases. This gives users a lot of flexibility in terms of data
sources and formats.

In addition to using your own data (e.g. as exported from a survey), the
Data Center keeps a continuously updated list of useful datasets by
discipline, accessible [here](../../tutorial/data).

In the following, we’ll discuss how to import and export from and to
various file formats, and discuss a number of available packages to do
so.

Start by reading the introduction to importing the most common file
types (text files, Excel, SPSS, Stata) [here](../../tutorial/r_data). It
is good to be aware of the Import dataset button and use it when needed,
but in the long run it is easier to be aware of the available import
functions and use them directly. The rest of this section gives more
information and examples of importing data from different file formats
and different levels of tidiness.

We will use the following packages for importing different file formats:

``` r
library(tidyverse)
library(readxl) # for Excel
library(haven) # for SPSS, Stata, SAS
```

In addition, the `rio` package provides a generic import function,
however, it is simply a wrapper for many of the other import functions
shown below. While it may be easier to use the same import function for
many file formats, `rio` redirects you to the original functions if you
look for the possible function arguments, therefore for debugging it is
better practice to use the “original” functions. In some cases, `rio`
can read URL file paths that `readr` and `readxl` can’t.

In the following, we’ll work with some example data of student
characteristics and grades. First, download
[this](https://github.com/ucrdatacenter/projects/tree/main/apprenticeship2/data)
zip-file from GitHub, and extract it into a data folder within your
apprenticeship project directory. We now import each file, explaining
the packages, functions, and function arguments used. These files are
all different subsets of the same dataset on student characteristics and
grades at a university ([original
source](https://www.kaggle.com/datasets/jacksondivakarr/student-classification-dataset?resource=download)).

`student1.csv` is a comma-separated text file. Opening it in a notepad
or Excel shows that the column separators are commas (,). The
`read_csv()` function from the previous workshop expects commas as a
separator, while `read_csv2()` expects semicolons (common with
e.g. Dutch language settings). Since CSV files are a form of delimited
text files, we can also use the more versatile `read_delim()` function
specifying the delimiter as the argument.

In the following, we use the `read_csv()` (and `read_delim()`) function
to import the data, and assign the resulting object to an object called
`student1` with the assignment operator `<-`. The `student1` object is
now a tibble in the R environment: you can find the object in the
Environment tab in RStudio, and view the data by clicking on the object
name or running `View(student1)` in the Console.

``` r
student1 <- read_csv("data/student1.csv")
student1 <- read_delim("data/student1.csv", delim = ",")
```

In this case, we used `read_csv()` only specifying its one mandatory
argument: the file path. When using `read_delim()`, we also specified an
optional argument: we defined the delimiter as a comma, thereby
overriding the default function behavior. To learn more about the
mandatory and optional arguments of a function, and find out what the
default behaviors are, you can use the `?` operator followed by the
function name, e.g. `?read_delim` in the Console to open the help file
of a function (or use the search bar of the Help tab).

`student2.tab` is also a delimited text file. Opening it in a notepad
shows that the delimiter is a tab. The notation for tab whitespace is
`\t`, which we can specify in the `delim` argument. Like the “.tab” file
extension, “.tsv” is also a tab-separated text file, so the more
specialized `read_tsv()` function also works.

If you load this data without optional arguments, you’ll see that
instead of the correct number of columns, we get a single column, with
the variable name containing some metadata. To get the correct number of
columns, we need to skip the first row of the data that contains this
metadata, as the actual data starts from the second row. We can use the
`skip` argument to skip the first row. If we use one of the relevant
import functions, and assign the outcome to the `student2` object, we
can see that the data is now correctly imported and shows up in the
environment next to `student1` as another tibble.

``` r
student2 <- read_delim("data/student2.tab", delim = "\t", skip = 1)
student2 <- read_tsv("data/student2.tab", skip = 1)
```

`student3.xlsx` is an Excel file. To import Excel file we need the
`read_excel()` function from the `readxl` package (the `readxl` package
is one of packages that is not a part of core `tidyverse` but uses the
same principles). With the `read_excel()` function you can specify which
sheet to use in addition to similar arguments as for delimited text
files. Notice that by default R imports the “Metadata” sheet, so we can
use the `sheet` argument to specify which sheet to import. In addition,
the first two rows contain introductory text, not the data, so we can
use the `skip` argument to skip those rows. You may also notice that
previously the variable name for age was `Student_Age` and now it is
`Student_age`. You can rename the variable either by giving a full list
of column names in the import function, but often it is easier to use
the `rename()` function after importing.

``` r
student3 <- read_excel("data/student3.xlsx", sheet = "Data")
```

The `haven` package (also not core `tidyverse` but same principles)
reads files in the data formats of SPSS (.sav) and Stata (.dta). It can
also extract variable and value labels from these files; here we can use
the `read_spss()` to import `student4.sav`.

``` r
student4 <- read_spss("data/student4.sav")
```

RDS is an R-specific file format that saves all attributes of the
dataframe (e.g. grouping, factor levels). It is particularly useful for
saving intermediate data files, e.g. saving the cleaned data before
analysis to avoid needing to run the data cleaning script repeatedly. To
import an RDS file such as `student5.rds`, use the `read_rds()`
function.

``` r
student5 <- read_rds("data/student5.rds")
```

A few notes about importing (and exporting) data:

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
- You can import files directly from URLs, although you usually need the
  URL of a raw file. If a file downloads immediately instead of opening
  in a raw format, you can try to copy that download link by
  right-clicking and selecting “Copy link address”; the `import()`
  function from `rio` might be successful with those links.
- To export data from R, you can almost always use the `write_...()`
  function corresponding to the desired file format, e.g. `write_csv()`.
  For Excel files the preferred export function is `write_xlsx()`, and
  for SPSS’s .sav files it is `write_sav()`.
- For other file formats, the generic `write()` function is useful; you
  can specify any file format, and if your input data is readable in the
  chosen format, the file will export properly.
- In all `write_()` functions you need to specify the data you’d like to
  save and the output file path (absolute or relative) including chosen
  file extension.

``` r
# example export code
write_csv(student1, "data/new_csv_data.csv")
```

## Data wrangling with `dplyr`

Data wrangling is the process of cleaning, structuring, and enriching
raw data into a more usable format. The `dplyr` package is a part of the
`tidyverse` and provides a set of functions that can be combined to
perform the most common data wrangling tasks. The package is built
around the concept of the “grammar of data manipulation”, which is a
consistent set of verbs that can be combined in many ways to achieve the
desired result.

The main functions in `dplyr` are `filter()`, `select()`, `mutate()`,
`arrange()`, `group_by()`, `summarize()`, and `rename()`. `dplyr` also
provides a set of functions for combining datasets: `bind_rows()` and
`bind_cols()` for row-wise and column-wise binding, and `left_join()`,
`right_join()`, `inner_join()`, and `full_join()` for joining datasets
based on common variables. These functions can be combined using the
pipe operator `|>` (or `%>%`, they are mostly equivalent) to create a
data wrangling workflow. The pipe operator takes the output of the
function on its left and passes it as the first argument to the function
on its right. This allows you to chain multiple functions together in a
single line of code, making your code more readable and easier to
understand.

In the following, we’ll work with the `student` datasets and show how to
use the main `dplyr` functions to clean the data so it is suitable for
analysis. These steps are useful even if the input data is quite clean,
as we often need to work with only a subset of observations/variables,
define new variables, or aggregate the data.

### Merging datasets

In our current application, we have five datasets that contain different
observations of the same, larger dataset. So we can list all datasets in
a row-binding function to combine them into a single dataset called
`student`.

``` r
student <- bind_rows(student1, student2, student3, student4, student5)
```

In the following, we’ll demonstrate the key data cleaning functions on
this merged tibble.

### Filtering observations

If we want to keep only a subset of observations, we can use the
`filter()` function. We can specify a logical condition as the argument
to `filter()`, and only observations that meet that condition will be
kept. For example, to keep only students who are over 21 years old, we
can use the following code:

``` r
filter(student, Student_Age > 21)
```

    ## # A tibble: 27 × 15
    ##       Id Student_Age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl>       <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5005          22 Male   Private          50%         No             
    ##  2  5015          26 Male   State            75%         Yes            
    ##  3  5016          22 Male   State            50%         No             
    ##  4  5018          22 Male   State            50%         No             
    ##  5  5023          22 Male   State            50%         Yes            
    ##  6  5024          25 Male   State            25%         Yes            
    ##  7  5029          24 Male   State            50%         No             
    ##  8  5032          25 Male   State            50%         Yes            
    ##  9  5040          22 Female State            50%         No             
    ## 10  5042          24 Male   State            50%         Yes            
    ## # ℹ 17 more rows
    ## # ℹ 9 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   Weekly_Study_Hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

In a pipe workflow, the same code would look like this:

``` r
student |> 
  filter(Student_Age > 21)
```

    ## # A tibble: 27 × 15
    ##       Id Student_Age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl>       <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5005          22 Male   Private          50%         No             
    ##  2  5015          26 Male   State            75%         Yes            
    ##  3  5016          22 Male   State            50%         No             
    ##  4  5018          22 Male   State            50%         No             
    ##  5  5023          22 Male   State            50%         Yes            
    ##  6  5024          25 Male   State            25%         Yes            
    ##  7  5029          24 Male   State            50%         No             
    ##  8  5032          25 Male   State            50%         Yes            
    ##  9  5040          22 Female State            50%         No             
    ## 10  5042          24 Male   State            50%         Yes            
    ## # ℹ 17 more rows
    ## # ℹ 9 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   Weekly_Study_Hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

We can also apply logical conditions to character variables, e.g. to
keep only students who went to a private high school and who did not
receive a failing grade. Filters can be combined with AND (`,` or `&`)
and OR (`|`) operators into a single function. Note the use of quotation
marks around the character values in the logical condition and the
double equal sign `==` to denote equality.

``` r
student |> 
  filter(High_School_Type == "Private", Grade != "Fail") 
```

    ## # A tibble: 23 × 15
    ##       Id Student_Age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl>       <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5004          18 Female Private          50%         Yes            
    ##  2  5005          22 Male   Private          50%         No             
    ##  3  5011          18 Female Private          50%         No             
    ##  4  5020          18 Male   Private          50%         No             
    ##  5  5028          18 Male   Private          50%         Yes            
    ##  6  5035          18 Male   Private          50%         No             
    ##  7  5036          18 Male   Private          75%         No             
    ##  8  5041          18 Male   Private          50%         No             
    ##  9  5050          18 Male   Private          75%         No             
    ## 10  5077          20 Male   Private          25%         No             
    ## # ℹ 13 more rows
    ## # ℹ 9 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   Weekly_Study_Hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

Another useful logical operator is `%in%`, which allows you to filter
observations based on a list of values. For example, to keep only
students who receive either 75% or 100% scholarships, we can use the
following code:

``` r
student |> 
  filter(Scholarship %in% c("75%", "100%"))
```

    ## # A tibble: 65 × 15
    ##       Id Student_Age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl>       <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5007          18 Male   State            75%         No             
    ##  2  5012          18 Female Private          75%         Yes            
    ##  3  5013          18 Female Private          75%         No             
    ##  4  5014          19 Female State            100%        No             
    ##  5  5015          26 Male   State            75%         Yes            
    ##  6  5017          18 Female State            100%        No             
    ##  7  5019          18 Female State            75%         No             
    ##  8  5021          18 Male   State            100%        Yes            
    ##  9  5022          18 Male   State            100%        No             
    ## 10  5030          19 Male   Other            75%         No             
    ## # ℹ 55 more rows
    ## # ℹ 9 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   Weekly_Study_Hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

### Selecting variables

If we want to keep only a subset of variables, we can use the `select()`
function. We can specify the variables we want to keep (or exclude, with
`-` signs) as the arguments to `select()`, and only those variables will
be kept. For example, to keep only the `Id` and `Student_Age` variables,
we can use the following code:

``` r
select(student, Id, Student_Age)
```

    ## # A tibble: 145 × 2
    ##       Id Student_Age
    ##    <dbl>       <dbl>
    ##  1  5001          21
    ##  2  5002          20
    ##  3  5003          21
    ##  4  5004          18
    ##  5  5005          22
    ##  6  5006          20
    ##  7  5007          18
    ##  8  5008          18
    ##  9  5009          19
    ## 10  5010          21
    ## # ℹ 135 more rows

We can also select columns based on their location in the dataframe or
by looking for patterns in the column names:

``` r
select(student, 1:3) # select the first three columns
```

    ## # A tibble: 145 × 3
    ##       Id Student_Age Sex   
    ##    <dbl>       <dbl> <chr> 
    ##  1  5001          21 Male  
    ##  2  5002          20 Male  
    ##  3  5003          21 Male  
    ##  4  5004          18 Female
    ##  5  5005          22 Male  
    ##  6  5006          20 Male  
    ##  7  5007          18 Male  
    ##  8  5008          18 Female
    ##  9  5009          19 Female
    ## 10  5010          21 Female
    ## # ℹ 135 more rows

``` r
select(student, starts_with("Student")) # select columns that start with "Student"
```

    ## # A tibble: 145 × 1
    ##    Student_Age
    ##          <dbl>
    ##  1          21
    ##  2          20
    ##  3          21
    ##  4          18
    ##  5          22
    ##  6          20
    ##  7          18
    ##  8          18
    ##  9          19
    ## 10          21
    ## # ℹ 135 more rows

``` r
select(student, -Grade) # keep everything but "Grade"
```

    ## # A tibble: 145 × 14
    ##       Id Student_Age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl>       <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5001          21 Male   Other            50%         Yes            
    ##  2  5002          20 Male   Other            50%         Yes            
    ##  3  5003          21 Male   State            50%         No             
    ##  4  5004          18 Female Private          50%         Yes            
    ##  5  5005          22 Male   Private          50%         No             
    ##  6  5006          20 Male   State            50%         No             
    ##  7  5007          18 Male   State            75%         No             
    ##  8  5008          18 Female State            50%         Yes            
    ##  9  5009          19 Female Other            50%         No             
    ## 10  5010          21 Female State            50%         No             
    ## # ℹ 135 more rows
    ## # ℹ 8 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   Weekly_Study_Hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>

``` r
select(student, -c(2, 6, 10)) # keep everything but the 2nd, 6th, and 10th columns
```

    ## # A tibble: 145 × 12
    ##       Id Sex    High_School_Type Scholarship Sports_activity Transportation
    ##    <dbl> <chr>  <chr>            <chr>       <chr>           <chr>         
    ##  1  5001 Male   Other            50%         No              Private       
    ##  2  5002 Male   Other            50%         No              Private       
    ##  3  5003 Male   State            50%         No              Private       
    ##  4  5004 Female Private          50%         No              Bus           
    ##  5  5005 Male   Private          50%         No              Bus           
    ##  6  5006 Male   State            50%         No              Private       
    ##  7  5007 Male   State            75%         No              Private       
    ##  8  5008 Female State            50%         Yes             Bus           
    ##  9  5009 Female Other            50%         Yes             Bus           
    ## 10  5010 Female State            50%         No              Bus           
    ## # ℹ 135 more rows
    ## # ℹ 6 more variables: Weekly_Study_Hours <dbl>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

A pipe workflow allows us to combine the filtering and selecting
operations into a single, step-by-step workflow:

``` r
student |> 
  filter(Student_Age > 21) |> 
  select(Id, Student_Age)
```

    ## # A tibble: 27 × 2
    ##       Id Student_Age
    ##    <dbl>       <dbl>
    ##  1  5005          22
    ##  2  5015          26
    ##  3  5016          22
    ##  4  5018          22
    ##  5  5023          22
    ##  6  5024          25
    ##  7  5029          24
    ##  8  5032          25
    ##  9  5040          22
    ## 10  5042          24
    ## # ℹ 17 more rows

### Creating new variables

If we want to create a new variable based on existing variables, we can
use the `mutate()` function. We can specify the new variable name and
the calculation for the new variable as the arguments to `mutate()`, and
the new variable will be added to the dataset. For example, we can
create a new variable `Daily_Study_Hours` that divides
`Weekly_Study_Hours` by 5, a new variable `Class_Participation` that is
a logical variable indicating whether the student has at least one “Yes”
answer for reading, listening, and taking notes, and a new variable
`Scholarship_num` that extracts the numeric value of `Scholarship` if
the string contains a number.

``` r
student |> 
  # create new variables
  mutate(Daily_Study_Hours = Weekly_Study_Hours / 5,
         Class_Participation = Reading == "Yes" | Listening_in_Class == "Yes" | Notes == "Yes",
         Scholarship_num = parse_number(Scholarship)) |> 
  # show only ID and the new variables
  select(Id, Daily_Study_Hours, Class_Participation, Scholarship_num)
```

    ## # A tibble: 145 × 4
    ##       Id Daily_Study_Hours Class_Participation Scholarship_num
    ##    <dbl>             <dbl> <lgl>                         <dbl>
    ##  1  5001               0   TRUE                             50
    ##  2  5002               0   TRUE                             50
    ##  3  5003               0.4 FALSE                            50
    ##  4  5004               0.4 TRUE                             50
    ##  5  5005               2.4 TRUE                             50
    ##  6  5006               0.4 TRUE                             50
    ##  7  5007               0   TRUE                             75
    ##  8  5008               0.4 TRUE                             50
    ##  9  5009               0   FALSE                            50
    ## 10  5010               2.4 TRUE                             50
    ## # ℹ 135 more rows

### Sorting the data

If we want to sort the data based on one or more variables, we can use
the `arrange()` function, taking the tibble and a variable list as its
arguments. By default, `arrange()` sorts in ascending order, but you can
specify descending order by using the `desc()` function. For example, to
sort the data by `Student_Age` in descending order, and
`Weekly_Study_Hours` in ascending order, we can use the following code:

``` r
student |> 
  arrange(desc(Student_Age), Weekly_Study_Hours)
```

    ## # A tibble: 145 × 15
    ##       Id Student_Age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl>       <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5118          26 Female Private          50%         No             
    ##  2  5015          26 Male   State            75%         Yes            
    ##  3  5032          25 Male   State            50%         Yes            
    ##  4  5056          25 Male   State            50%         Yes            
    ##  5  5024          25 Male   State            25%         Yes            
    ##  6  5029          24 Male   State            50%         No             
    ##  7  5082          24 Male   State            50%         Yes            
    ##  8  5042          24 Male   State            50%         Yes            
    ##  9  5085          24 Male   Other            50%         Yes            
    ## 10  5059          23 Male   State            50%         No             
    ## # ℹ 135 more rows
    ## # ℹ 9 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   Weekly_Study_Hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

### Renaming variables

If we want to rename variables, we can use the `rename()` function with
the argument structure `new name = old name`. For example, we can rename
the `Student_Age` variable to `age` and the `Weekly_Study_Hours`
variable to `weekly_hours`, we can use the following code:

``` r
student |> 
  rename(age = Student_Age, weekly_hours = Weekly_Study_Hours)
```

    ## # A tibble: 145 × 15
    ##       Id   age Sex    High_School_Type Scholarship Additional_Work
    ##    <dbl> <dbl> <chr>  <chr>            <chr>       <chr>          
    ##  1  5001    21 Male   Other            50%         Yes            
    ##  2  5002    20 Male   Other            50%         Yes            
    ##  3  5003    21 Male   State            50%         No             
    ##  4  5004    18 Female Private          50%         Yes            
    ##  5  5005    22 Male   Private          50%         No             
    ##  6  5006    20 Male   State            50%         No             
    ##  7  5007    18 Male   State            75%         No             
    ##  8  5008    18 Female State            50%         Yes            
    ##  9  5009    19 Female Other            50%         No             
    ## 10  5010    21 Female State            50%         No             
    ## # ℹ 135 more rows
    ## # ℹ 9 more variables: Sports_activity <chr>, Transportation <chr>,
    ## #   weekly_hours <dbl>, Attendance <chr>, Reading <chr>, Notes <chr>,
    ## #   Listening_in_Class <chr>, Project_work <chr>, Grade <chr>

### Data cleaning as a single pipeline

Until now we didn’t save any of our data wrangling steps as new objects,
so the original `student1` object is still unchanged. If we want to save
the cleaned data as a new object, we can assign the result of the pipe
workflow to a new object.

``` r
student_subset <- student1 |> 
  filter(Student_Age > 21) |> 
  select(Id, Student_Age) |> 
  arrange(desc(Student_Age)) |> 
  rename(age = Student_Age)
```

## Exploratory analysis

### Summary statistics

<!-- central tendencies, dispersion, correlation -->

### Data visualization with `ggplot2`

<!-- univariate, bivariate, densities -->

## Hypothesis testing / modelling

<!-- t-tests, simple regression, ANOVA, chi-square, correlation test  -->
<!-- multiple regression (+ extra specification), nonparametric tests, power and effect size, logistic regression, PCA, factor analysis, repeated measures ANOVA, mixed design ANOVA -->

# Other resources

- [Data Center collection of resources](../../tutorial/links/)
