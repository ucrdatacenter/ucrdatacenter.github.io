---
title: "Data Center Apprenticeship:\nR basics: Data wrangling with `dplyr`"
subtitle: "June 2024" 
date: "Last updated: 2024-06-15"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Merging datasets](#merging-datasets)
- [Filtering observations](#filtering-observations)
- [Selecting variables](#selecting-variables)
- [Creating new variables](#creating-new-variables)
- [Sorting the data](#sorting-the-data)
- [Renaming variables](#renaming-variables)
- [Categorical variables as factors](#categorical-variables-as-factors)
- [Data cleaning as a single
  pipeline](#data-cleaning-as-a-single-pipeline)
- [Go to](#go-to)

Data wrangling is the process of cleaning, structuring, and enriching
raw data into a more usable format. The `dplyr` package is a part of the
`tidyverse` and provides a set of functions that can be combined to
perform the most common data wrangling tasks. The package is built
around the concept of the “grammar of data manipulation”, which is a
consistent set of verbs that can be combined in many ways to achieve the
desired result.

If `tidyverse` is not yet loaded in this R session, please do so now:

``` r
library(tidyverse)
```

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

In the following, we’ll work with the `student` datasets imported in the
previous section and show how to use the main `dplyr` functions to clean
the data so it is suitable for analysis. These steps are useful even if
the input data is quite clean, as we often need to work with only a
subset of observations/variables, define new variables, or aggregate the
data.

# Merging datasets

In our current application, we have five datasets that contain different
observations of the same, larger dataset. So we can list all datasets in
a row-binding function to combine them into a single dataset called
`student`.

``` r
student <- bind_rows(student1, student2, student3, student4, student5)
```

In the following, we’ll demonstrate the key data cleaning functions on
this merged tibble.

# Filtering observations

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

# Selecting variables

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

# Creating new variables

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
         Scholarship_num =parse_number(Scholarship)) |> 
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

# Sorting the data

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

# Renaming variables

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

# Categorical variables as factors

It is often useful to clearly define the levels of a categorical
variable, especially if these levels have a meaningful ordering. For
unordered categories, R provides the data type `factor`, while for
ordered variables the relevant data type is `ordered`. Factor and
ordered values appear as character strings when viewed, but are treated
as numbers with labels internally, which makes it easier to show
descriptives of the variable and include it in models. For example, we
can define `High_School_Type` as a factor with three levels and
`Attendance` as ordered with the `factor()` and `ordered()` functions.
If we don’t specify the levels of the factor explicitly, then the levels
will be sorted alphabetically.

``` r
student |> 
  mutate(High_School_Type = factor(High_School_Type),
         Attendance = ordered(Attendance, levels = c("Never", "Sometimes", "Always"))) |> 
  select(High_School_Type, Attendance) |> 
  # view variable types and levels by looking at the structure of the data
  str()
```

    ## tibble [145 × 2] (S3: tbl_df/tbl/data.frame)
    ##  $ High_School_Type: Factor w/ 3 levels "Other","Private",..: 1 1 3 2 2 3 3 3 1 3 ...
    ##  $ Attendance      : Ord.factor w/ 3 levels "Never"<"Sometimes"<..: 3 3 1 3 3 3 3 2 3 1 ...

# Data cleaning as a single pipeline

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

To prepare for the rest of the analysis, let’s create a new `data`
object that keeps all observations, and converts some of the indicators
to numeric and logical, and rename the relevant variables to convenient
“snake case”:

``` r
data <- student |> 
  mutate(scholarship = parse_number(Scholarship),
         # ifelse contains a logical condition, a value if TRUE, and a value if FALSE
         additional_work = ifelse(Additional_Work == "Yes", TRUE, FALSE),
         reading = ifelse(Reading == "Yes", TRUE, FALSE),
         notes = ifelse(Notes == "Yes", TRUE, FALSE),
         listening = ifelse(Listening_in_Class == "Yes", TRUE, FALSE),
         # case_when is an expansion of ifelse: it allows multiple conditions
         # the value after the tilde (~) is the value if the condition is TRUE
         grade = case_when(
           Grade == "Fail" ~ 0,
           Grade == "DD" ~ 1,
           Grade == "DC" ~ 1.5,
           Grade == "CC" ~ 2,
           Grade == "CB" ~ 3,
           Grade == "BB" ~ 3,
           Grade == "BA" ~ 4,
           Grade == "AA" ~ 4
         )) |> 
  rename(id = Id, age = Student_Age) |> 
  select(id, age, scholarship, additional_work, reading, notes, listening, grade)
```

# Go to

- [Introduction to R](intro)
- [Finding and importing data](import)
- [Summary statistics](summary)
- [Data visualization with `ggplot2`](ggplot)
- [Hypothesis testing / modelling](tests)
