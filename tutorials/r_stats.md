---
layout: page
title: "Statistics in R"
date: "Last updated: 2025-02-07"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

# Introduction

This document provides an overview of the basic statistical functions in
R, including descriptive statistics and summary tables.

This tutorial shows you examples of using statistical methods on the
`diamonds` dataset, which comes pre-loaded with the `tidyverse` package.

Let’s load the `tidyverse` package and have a look at the `diamonds`
dataset:

``` r
library(tidyverse)
data(diamonds)
```

# Descriptive statistics

## Summaries in the R Console

To get a descriptive statistic of a single variable in a tibble, we can
use that variable as an argument to a relevant function (using `$` to
refer to a variable in a tibble).

``` r
mean(diamonds$price)
```

    ## [1] 3932.8

``` r
median(diamonds$price)
```

    ## [1] 2401

``` r
sd(diamonds$price)
```

    ## [1] 3989.44

To get the frequencies of a categorical variable, we can use the
`count()` function, with the `sort = TRUE` argument returning the values
in descending frequency. `count()` is a tidy function that works well
with pipe workflows and can count the joint frequencies of multiple
variables.

``` r
# frequencies of a single variable
count(diamonds, cut)
```

    ## # A tibble: 5 × 2
    ##   cut           n
    ##   <ord>     <int>
    ## 1 Fair       1610
    ## 2 Good       4906
    ## 3 Very Good 12082
    ## 4 Premium   13791
    ## 5 Ideal     21551

``` r
# joint frequency distribution
count(diamonds, cut, color)
```

    ## # A tibble: 35 × 3
    ##    cut   color     n
    ##    <ord> <ord> <int>
    ##  1 Fair  D       163
    ##  2 Fair  E       224
    ##  3 Fair  F       312
    ##  4 Fair  G       314
    ##  5 Fair  H       303
    ##  6 Fair  I       175
    ##  7 Fair  J       119
    ##  8 Good  D       662
    ##  9 Good  E       933
    ## 10 Good  F       909
    ## # ℹ 25 more rows

To get the correlation coefficient between two variables, we can use the
`cor()` function in the same way we used other descriptives such as
`mean()`.

``` r
cor(diamonds$price, diamonds$carat)
```

    ## [1] 0.9215913

The easiest way to get summary statistics of all variables in a tibble
is with the `summary()` function: this function shows the distribution
of numeric variables, the frequencies of categorical variables, and the
number of missing values for each variable.

``` r
summary(diamonds)
```

    ##      carat               cut        color        clarity          depth      
    ##  Min.   :0.2000   Fair     : 1610   D: 6775   SI1    :13065   Min.   :43.00  
    ##  1st Qu.:0.4000   Good     : 4906   E: 9797   VS2    :12258   1st Qu.:61.00  
    ##  Median :0.7000   Very Good:12082   F: 9542   SI2    : 9194   Median :61.80  
    ##  Mean   :0.7979   Premium  :13791   G:11292   VS1    : 8171   Mean   :61.75  
    ##  3rd Qu.:1.0400   Ideal    :21551   H: 8304   VVS2   : 5066   3rd Qu.:62.50  
    ##  Max.   :5.0100                     I: 5422   VVS1   : 3655   Max.   :79.00  
    ##                                     J: 2808   (Other): 2531                  
    ##      table           price             x                y         
    ##  Min.   :43.00   Min.   :  326   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.:56.00   1st Qu.:  950   1st Qu.: 4.710   1st Qu.: 4.720  
    ##  Median :57.00   Median : 2401   Median : 5.700   Median : 5.710  
    ##  Mean   :57.46   Mean   : 3933   Mean   : 5.731   Mean   : 5.735  
    ##  3rd Qu.:59.00   3rd Qu.: 5324   3rd Qu.: 6.540   3rd Qu.: 6.540  
    ##  Max.   :95.00   Max.   :18823   Max.   :10.740   Max.   :58.900  
    ##                                                                   
    ##        z         
    ##  Min.   : 0.000  
    ##  1st Qu.: 2.910  
    ##  Median : 3.530  
    ##  Mean   : 3.539  
    ##  3rd Qu.: 4.040  
    ##  Max.   :31.800  
    ## 

## Publication-ready summaries with `gtsummary`

The `summary()` function is useful for viewing the data in the Console,
but doesn’t export to outside of R nicely. There are a few packages
available for generating simple summary statistics tables that contain
information about the central tendencies and dispersion of the data that
also export nicely, such as `gtsummary`.

``` r
library(gtsummary)

# tbl_summary() creates a summary table of the data
tbl_summary(diamonds)
```

<div id="pshmubpiri" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#pshmubpiri table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#pshmubpiri thead, #pshmubpiri tbody, #pshmubpiri tfoot, #pshmubpiri tr, #pshmubpiri td, #pshmubpiri th {
  border-style: none;
}
&#10;#pshmubpiri p {
  margin: 0;
  padding: 0;
}
&#10;#pshmubpiri .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#pshmubpiri .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#pshmubpiri .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#pshmubpiri .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#pshmubpiri .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#pshmubpiri .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#pshmubpiri .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#pshmubpiri .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#pshmubpiri .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#pshmubpiri .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#pshmubpiri .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#pshmubpiri .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#pshmubpiri .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#pshmubpiri .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#pshmubpiri .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#pshmubpiri .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#pshmubpiri .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#pshmubpiri .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#pshmubpiri .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#pshmubpiri .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#pshmubpiri .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#pshmubpiri .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#pshmubpiri .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#pshmubpiri .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#pshmubpiri .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#pshmubpiri .gt_left {
  text-align: left;
}
&#10;#pshmubpiri .gt_center {
  text-align: center;
}
&#10;#pshmubpiri .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#pshmubpiri .gt_font_normal {
  font-weight: normal;
}
&#10;#pshmubpiri .gt_font_bold {
  font-weight: bold;
}
&#10;#pshmubpiri .gt_font_italic {
  font-style: italic;
}
&#10;#pshmubpiri .gt_super {
  font-size: 65%;
}
&#10;#pshmubpiri .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#pshmubpiri .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#pshmubpiri .gt_indent_1 {
  text-indent: 5px;
}
&#10;#pshmubpiri .gt_indent_2 {
  text-indent: 10px;
}
&#10;#pshmubpiri .gt_indent_3 {
  text-indent: 15px;
}
&#10;#pshmubpiri .gt_indent_4 {
  text-indent: 20px;
}
&#10;#pshmubpiri .gt_indent_5 {
  text-indent: 25px;
}
&#10;#pshmubpiri .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#pshmubpiri div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="label"><span class='gt_from_md'><strong>Characteristic</strong></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_0"><span class='gt_from_md'><strong>N = 53,940</strong></span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">carat</td>
<td headers="stat_0" class="gt_row gt_center">0.70 (0.40, 1.04)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">cut</td>
<td headers="stat_0" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Fair</td>
<td headers="stat_0" class="gt_row gt_center">1,610 (3.0%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Good</td>
<td headers="stat_0" class="gt_row gt_center">4,906 (9.1%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Very Good</td>
<td headers="stat_0" class="gt_row gt_center">12,082 (22%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Premium</td>
<td headers="stat_0" class="gt_row gt_center">13,791 (26%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Ideal</td>
<td headers="stat_0" class="gt_row gt_center">21,551 (40%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">color</td>
<td headers="stat_0" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    D</td>
<td headers="stat_0" class="gt_row gt_center">6,775 (13%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    E</td>
<td headers="stat_0" class="gt_row gt_center">9,797 (18%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    F</td>
<td headers="stat_0" class="gt_row gt_center">9,542 (18%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    G</td>
<td headers="stat_0" class="gt_row gt_center">11,292 (21%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    H</td>
<td headers="stat_0" class="gt_row gt_center">8,304 (15%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I</td>
<td headers="stat_0" class="gt_row gt_center">5,422 (10%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    J</td>
<td headers="stat_0" class="gt_row gt_center">2,808 (5.2%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">clarity</td>
<td headers="stat_0" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I1</td>
<td headers="stat_0" class="gt_row gt_center">741 (1.4%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI2</td>
<td headers="stat_0" class="gt_row gt_center">9,194 (17%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI1</td>
<td headers="stat_0" class="gt_row gt_center">13,065 (24%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS2</td>
<td headers="stat_0" class="gt_row gt_center">12,258 (23%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS1</td>
<td headers="stat_0" class="gt_row gt_center">8,171 (15%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS2</td>
<td headers="stat_0" class="gt_row gt_center">5,066 (9.4%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS1</td>
<td headers="stat_0" class="gt_row gt_center">3,655 (6.8%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    IF</td>
<td headers="stat_0" class="gt_row gt_center">1,790 (3.3%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">depth</td>
<td headers="stat_0" class="gt_row gt_center">61.80 (61.00, 62.50)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">table</td>
<td headers="stat_0" class="gt_row gt_center">57.00 (56.00, 59.00)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">price</td>
<td headers="stat_0" class="gt_row gt_center">2,401 (950, 5,325)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">x</td>
<td headers="stat_0" class="gt_row gt_center">5.70 (4.71, 6.54)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">y</td>
<td headers="stat_0" class="gt_row gt_center">5.71 (4.72, 6.54)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">z</td>
<td headers="stat_0" class="gt_row gt_center">3.53 (2.91, 4.04)</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="2"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span> <span class='gt_from_md'>Median (Q1, Q3); n (%)</span></td>
    </tr>
  </tfoot>
</table>
</div>

You can stratify your summary by a grouping variable using the `by`
argument:

``` r
tbl_summary(diamonds, by = cut)
```

<div id="waywwjryyh" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#waywwjryyh table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#waywwjryyh thead, #waywwjryyh tbody, #waywwjryyh tfoot, #waywwjryyh tr, #waywwjryyh td, #waywwjryyh th {
  border-style: none;
}
&#10;#waywwjryyh p {
  margin: 0;
  padding: 0;
}
&#10;#waywwjryyh .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#waywwjryyh .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#waywwjryyh .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#waywwjryyh .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#waywwjryyh .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#waywwjryyh .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#waywwjryyh .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#waywwjryyh .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#waywwjryyh .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#waywwjryyh .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#waywwjryyh .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#waywwjryyh .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#waywwjryyh .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#waywwjryyh .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#waywwjryyh .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#waywwjryyh .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#waywwjryyh .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#waywwjryyh .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#waywwjryyh .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#waywwjryyh .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#waywwjryyh .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#waywwjryyh .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#waywwjryyh .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#waywwjryyh .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#waywwjryyh .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#waywwjryyh .gt_left {
  text-align: left;
}
&#10;#waywwjryyh .gt_center {
  text-align: center;
}
&#10;#waywwjryyh .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#waywwjryyh .gt_font_normal {
  font-weight: normal;
}
&#10;#waywwjryyh .gt_font_bold {
  font-weight: bold;
}
&#10;#waywwjryyh .gt_font_italic {
  font-style: italic;
}
&#10;#waywwjryyh .gt_super {
  font-size: 65%;
}
&#10;#waywwjryyh .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#waywwjryyh .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#waywwjryyh .gt_indent_1 {
  text-indent: 5px;
}
&#10;#waywwjryyh .gt_indent_2 {
  text-indent: 10px;
}
&#10;#waywwjryyh .gt_indent_3 {
  text-indent: 15px;
}
&#10;#waywwjryyh .gt_indent_4 {
  text-indent: 20px;
}
&#10;#waywwjryyh .gt_indent_5 {
  text-indent: 25px;
}
&#10;#waywwjryyh .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#waywwjryyh div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="label"><span class='gt_from_md'><strong>Characteristic</strong></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_1"><span class='gt_from_md'><strong>Fair</strong><br />
N = 1,610</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_2"><span class='gt_from_md'><strong>Good</strong><br />
N = 4,906</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_3"><span class='gt_from_md'><strong>Very Good</strong><br />
N = 12,082</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_4"><span class='gt_from_md'><strong>Premium</strong><br />
N = 13,791</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_5"><span class='gt_from_md'><strong>Ideal</strong><br />
N = 21,551</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">carat</td>
<td headers="stat_1" class="gt_row gt_center">1.00 (0.70, 1.20)</td>
<td headers="stat_2" class="gt_row gt_center">0.82 (0.50, 1.01)</td>
<td headers="stat_3" class="gt_row gt_center">0.71 (0.41, 1.02)</td>
<td headers="stat_4" class="gt_row gt_center">0.86 (0.41, 1.20)</td>
<td headers="stat_5" class="gt_row gt_center">0.54 (0.35, 1.01)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">color</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td>
<td headers="stat_3" class="gt_row gt_center"><br /></td>
<td headers="stat_4" class="gt_row gt_center"><br /></td>
<td headers="stat_5" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    D</td>
<td headers="stat_1" class="gt_row gt_center">163 (10%)</td>
<td headers="stat_2" class="gt_row gt_center">662 (13%)</td>
<td headers="stat_3" class="gt_row gt_center">1,513 (13%)</td>
<td headers="stat_4" class="gt_row gt_center">1,603 (12%)</td>
<td headers="stat_5" class="gt_row gt_center">2,834 (13%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    E</td>
<td headers="stat_1" class="gt_row gt_center">224 (14%)</td>
<td headers="stat_2" class="gt_row gt_center">933 (19%)</td>
<td headers="stat_3" class="gt_row gt_center">2,400 (20%)</td>
<td headers="stat_4" class="gt_row gt_center">2,337 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,903 (18%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    F</td>
<td headers="stat_1" class="gt_row gt_center">312 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">909 (19%)</td>
<td headers="stat_3" class="gt_row gt_center">2,164 (18%)</td>
<td headers="stat_4" class="gt_row gt_center">2,331 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,826 (18%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    G</td>
<td headers="stat_1" class="gt_row gt_center">314 (20%)</td>
<td headers="stat_2" class="gt_row gt_center">871 (18%)</td>
<td headers="stat_3" class="gt_row gt_center">2,299 (19%)</td>
<td headers="stat_4" class="gt_row gt_center">2,924 (21%)</td>
<td headers="stat_5" class="gt_row gt_center">4,884 (23%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    H</td>
<td headers="stat_1" class="gt_row gt_center">303 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">702 (14%)</td>
<td headers="stat_3" class="gt_row gt_center">1,824 (15%)</td>
<td headers="stat_4" class="gt_row gt_center">2,360 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,115 (14%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I</td>
<td headers="stat_1" class="gt_row gt_center">175 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">522 (11%)</td>
<td headers="stat_3" class="gt_row gt_center">1,204 (10.0%)</td>
<td headers="stat_4" class="gt_row gt_center">1,428 (10%)</td>
<td headers="stat_5" class="gt_row gt_center">2,093 (9.7%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    J</td>
<td headers="stat_1" class="gt_row gt_center">119 (7.4%)</td>
<td headers="stat_2" class="gt_row gt_center">307 (6.3%)</td>
<td headers="stat_3" class="gt_row gt_center">678 (5.6%)</td>
<td headers="stat_4" class="gt_row gt_center">808 (5.9%)</td>
<td headers="stat_5" class="gt_row gt_center">896 (4.2%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">clarity</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td>
<td headers="stat_3" class="gt_row gt_center"><br /></td>
<td headers="stat_4" class="gt_row gt_center"><br /></td>
<td headers="stat_5" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I1</td>
<td headers="stat_1" class="gt_row gt_center">210 (13%)</td>
<td headers="stat_2" class="gt_row gt_center">96 (2.0%)</td>
<td headers="stat_3" class="gt_row gt_center">84 (0.7%)</td>
<td headers="stat_4" class="gt_row gt_center">205 (1.5%)</td>
<td headers="stat_5" class="gt_row gt_center">146 (0.7%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI2</td>
<td headers="stat_1" class="gt_row gt_center">466 (29%)</td>
<td headers="stat_2" class="gt_row gt_center">1,081 (22%)</td>
<td headers="stat_3" class="gt_row gt_center">2,100 (17%)</td>
<td headers="stat_4" class="gt_row gt_center">2,949 (21%)</td>
<td headers="stat_5" class="gt_row gt_center">2,598 (12%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI1</td>
<td headers="stat_1" class="gt_row gt_center">408 (25%)</td>
<td headers="stat_2" class="gt_row gt_center">1,560 (32%)</td>
<td headers="stat_3" class="gt_row gt_center">3,240 (27%)</td>
<td headers="stat_4" class="gt_row gt_center">3,575 (26%)</td>
<td headers="stat_5" class="gt_row gt_center">4,282 (20%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS2</td>
<td headers="stat_1" class="gt_row gt_center">261 (16%)</td>
<td headers="stat_2" class="gt_row gt_center">978 (20%)</td>
<td headers="stat_3" class="gt_row gt_center">2,591 (21%)</td>
<td headers="stat_4" class="gt_row gt_center">3,357 (24%)</td>
<td headers="stat_5" class="gt_row gt_center">5,071 (24%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS1</td>
<td headers="stat_1" class="gt_row gt_center">170 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">648 (13%)</td>
<td headers="stat_3" class="gt_row gt_center">1,775 (15%)</td>
<td headers="stat_4" class="gt_row gt_center">1,989 (14%)</td>
<td headers="stat_5" class="gt_row gt_center">3,589 (17%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS2</td>
<td headers="stat_1" class="gt_row gt_center">69 (4.3%)</td>
<td headers="stat_2" class="gt_row gt_center">286 (5.8%)</td>
<td headers="stat_3" class="gt_row gt_center">1,235 (10%)</td>
<td headers="stat_4" class="gt_row gt_center">870 (6.3%)</td>
<td headers="stat_5" class="gt_row gt_center">2,606 (12%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS1</td>
<td headers="stat_1" class="gt_row gt_center">17 (1.1%)</td>
<td headers="stat_2" class="gt_row gt_center">186 (3.8%)</td>
<td headers="stat_3" class="gt_row gt_center">789 (6.5%)</td>
<td headers="stat_4" class="gt_row gt_center">616 (4.5%)</td>
<td headers="stat_5" class="gt_row gt_center">2,047 (9.5%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    IF</td>
<td headers="stat_1" class="gt_row gt_center">9 (0.6%)</td>
<td headers="stat_2" class="gt_row gt_center">71 (1.4%)</td>
<td headers="stat_3" class="gt_row gt_center">268 (2.2%)</td>
<td headers="stat_4" class="gt_row gt_center">230 (1.7%)</td>
<td headers="stat_5" class="gt_row gt_center">1,212 (5.6%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">depth</td>
<td headers="stat_1" class="gt_row gt_center">65.00 (64.40, 65.90)</td>
<td headers="stat_2" class="gt_row gt_center">63.40 (61.30, 63.80)</td>
<td headers="stat_3" class="gt_row gt_center">62.10 (60.90, 62.90)</td>
<td headers="stat_4" class="gt_row gt_center">61.40 (60.50, 62.20)</td>
<td headers="stat_5" class="gt_row gt_center">61.80 (61.30, 62.20)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">table</td>
<td headers="stat_1" class="gt_row gt_center">58.00 (56.00, 61.00)</td>
<td headers="stat_2" class="gt_row gt_center">58.00 (56.00, 61.00)</td>
<td headers="stat_3" class="gt_row gt_center">58.00 (56.00, 59.00)</td>
<td headers="stat_4" class="gt_row gt_center">59.00 (58.00, 60.00)</td>
<td headers="stat_5" class="gt_row gt_center">56.00 (55.00, 57.00)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">price</td>
<td headers="stat_1" class="gt_row gt_center">3,282 (2,050, 5,208)</td>
<td headers="stat_2" class="gt_row gt_center">3,051 (1,145, 5,028)</td>
<td headers="stat_3" class="gt_row gt_center">2,648 (912, 5,373)</td>
<td headers="stat_4" class="gt_row gt_center">3,185 (1,046, 6,296)</td>
<td headers="stat_5" class="gt_row gt_center">1,810 (878, 4,679)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">x</td>
<td headers="stat_1" class="gt_row gt_center">6.18 (5.63, 6.70)</td>
<td headers="stat_2" class="gt_row gt_center">5.98 (5.02, 6.42)</td>
<td headers="stat_3" class="gt_row gt_center">5.74 (4.75, 6.47)</td>
<td headers="stat_4" class="gt_row gt_center">6.11 (4.80, 6.80)</td>
<td headers="stat_5" class="gt_row gt_center">5.25 (4.54, 6.44)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">y</td>
<td headers="stat_1" class="gt_row gt_center">6.10 (5.57, 6.64)</td>
<td headers="stat_2" class="gt_row gt_center">5.99 (5.02, 6.44)</td>
<td headers="stat_3" class="gt_row gt_center">5.77 (4.77, 6.51)</td>
<td headers="stat_4" class="gt_row gt_center">6.06 (4.79, 6.76)</td>
<td headers="stat_5" class="gt_row gt_center">5.26 (4.55, 6.45)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">z</td>
<td headers="stat_1" class="gt_row gt_center">3.97 (3.61, 4.28)</td>
<td headers="stat_2" class="gt_row gt_center">3.70 (3.07, 4.03)</td>
<td headers="stat_3" class="gt_row gt_center">3.56 (2.95, 4.02)</td>
<td headers="stat_4" class="gt_row gt_center">3.72 (2.94, 4.16)</td>
<td headers="stat_5" class="gt_row gt_center">3.23 (2.80, 3.98)</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="6"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span> <span class='gt_from_md'>Median (Q1, Q3); n (%)</span></td>
    </tr>
  </tfoot>
</table>
</div>

``` r
# add p-value of difference between groups
tbl_summary(diamonds, by = cut) |> 
  add_p()
```

<div id="fqbkybkycm" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#fqbkybkycm table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#fqbkybkycm thead, #fqbkybkycm tbody, #fqbkybkycm tfoot, #fqbkybkycm tr, #fqbkybkycm td, #fqbkybkycm th {
  border-style: none;
}
&#10;#fqbkybkycm p {
  margin: 0;
  padding: 0;
}
&#10;#fqbkybkycm .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#fqbkybkycm .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#fqbkybkycm .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#fqbkybkycm .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#fqbkybkycm .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#fqbkybkycm .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#fqbkybkycm .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#fqbkybkycm .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#fqbkybkycm .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#fqbkybkycm .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#fqbkybkycm .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#fqbkybkycm .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#fqbkybkycm .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#fqbkybkycm .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#fqbkybkycm .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#fqbkybkycm .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#fqbkybkycm .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#fqbkybkycm .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#fqbkybkycm .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#fqbkybkycm .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#fqbkybkycm .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#fqbkybkycm .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#fqbkybkycm .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#fqbkybkycm .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#fqbkybkycm .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#fqbkybkycm .gt_left {
  text-align: left;
}
&#10;#fqbkybkycm .gt_center {
  text-align: center;
}
&#10;#fqbkybkycm .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#fqbkybkycm .gt_font_normal {
  font-weight: normal;
}
&#10;#fqbkybkycm .gt_font_bold {
  font-weight: bold;
}
&#10;#fqbkybkycm .gt_font_italic {
  font-style: italic;
}
&#10;#fqbkybkycm .gt_super {
  font-size: 65%;
}
&#10;#fqbkybkycm .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#fqbkybkycm .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#fqbkybkycm .gt_indent_1 {
  text-indent: 5px;
}
&#10;#fqbkybkycm .gt_indent_2 {
  text-indent: 10px;
}
&#10;#fqbkybkycm .gt_indent_3 {
  text-indent: 15px;
}
&#10;#fqbkybkycm .gt_indent_4 {
  text-indent: 20px;
}
&#10;#fqbkybkycm .gt_indent_5 {
  text-indent: 25px;
}
&#10;#fqbkybkycm .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#fqbkybkycm div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="label"><span class='gt_from_md'><strong>Characteristic</strong></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_1"><span class='gt_from_md'><strong>Fair</strong><br />
N = 1,610</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_2"><span class='gt_from_md'><strong>Good</strong><br />
N = 4,906</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_3"><span class='gt_from_md'><strong>Very Good</strong><br />
N = 12,082</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_4"><span class='gt_from_md'><strong>Premium</strong><br />
N = 13,791</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_5"><span class='gt_from_md'><strong>Ideal</strong><br />
N = 21,551</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="p.value"><span class='gt_from_md'><strong>p-value</strong></span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>2</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">carat</td>
<td headers="stat_1" class="gt_row gt_center">1.00 (0.70, 1.20)</td>
<td headers="stat_2" class="gt_row gt_center">0.82 (0.50, 1.01)</td>
<td headers="stat_3" class="gt_row gt_center">0.71 (0.41, 1.02)</td>
<td headers="stat_4" class="gt_row gt_center">0.86 (0.41, 1.20)</td>
<td headers="stat_5" class="gt_row gt_center">0.54 (0.35, 1.01)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">color</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td>
<td headers="stat_3" class="gt_row gt_center"><br /></td>
<td headers="stat_4" class="gt_row gt_center"><br /></td>
<td headers="stat_5" class="gt_row gt_center"><br /></td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    D</td>
<td headers="stat_1" class="gt_row gt_center">163 (10%)</td>
<td headers="stat_2" class="gt_row gt_center">662 (13%)</td>
<td headers="stat_3" class="gt_row gt_center">1,513 (13%)</td>
<td headers="stat_4" class="gt_row gt_center">1,603 (12%)</td>
<td headers="stat_5" class="gt_row gt_center">2,834 (13%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    E</td>
<td headers="stat_1" class="gt_row gt_center">224 (14%)</td>
<td headers="stat_2" class="gt_row gt_center">933 (19%)</td>
<td headers="stat_3" class="gt_row gt_center">2,400 (20%)</td>
<td headers="stat_4" class="gt_row gt_center">2,337 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,903 (18%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    F</td>
<td headers="stat_1" class="gt_row gt_center">312 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">909 (19%)</td>
<td headers="stat_3" class="gt_row gt_center">2,164 (18%)</td>
<td headers="stat_4" class="gt_row gt_center">2,331 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,826 (18%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    G</td>
<td headers="stat_1" class="gt_row gt_center">314 (20%)</td>
<td headers="stat_2" class="gt_row gt_center">871 (18%)</td>
<td headers="stat_3" class="gt_row gt_center">2,299 (19%)</td>
<td headers="stat_4" class="gt_row gt_center">2,924 (21%)</td>
<td headers="stat_5" class="gt_row gt_center">4,884 (23%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    H</td>
<td headers="stat_1" class="gt_row gt_center">303 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">702 (14%)</td>
<td headers="stat_3" class="gt_row gt_center">1,824 (15%)</td>
<td headers="stat_4" class="gt_row gt_center">2,360 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,115 (14%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I</td>
<td headers="stat_1" class="gt_row gt_center">175 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">522 (11%)</td>
<td headers="stat_3" class="gt_row gt_center">1,204 (10.0%)</td>
<td headers="stat_4" class="gt_row gt_center">1,428 (10%)</td>
<td headers="stat_5" class="gt_row gt_center">2,093 (9.7%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    J</td>
<td headers="stat_1" class="gt_row gt_center">119 (7.4%)</td>
<td headers="stat_2" class="gt_row gt_center">307 (6.3%)</td>
<td headers="stat_3" class="gt_row gt_center">678 (5.6%)</td>
<td headers="stat_4" class="gt_row gt_center">808 (5.9%)</td>
<td headers="stat_5" class="gt_row gt_center">896 (4.2%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">clarity</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td>
<td headers="stat_3" class="gt_row gt_center"><br /></td>
<td headers="stat_4" class="gt_row gt_center"><br /></td>
<td headers="stat_5" class="gt_row gt_center"><br /></td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I1</td>
<td headers="stat_1" class="gt_row gt_center">210 (13%)</td>
<td headers="stat_2" class="gt_row gt_center">96 (2.0%)</td>
<td headers="stat_3" class="gt_row gt_center">84 (0.7%)</td>
<td headers="stat_4" class="gt_row gt_center">205 (1.5%)</td>
<td headers="stat_5" class="gt_row gt_center">146 (0.7%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI2</td>
<td headers="stat_1" class="gt_row gt_center">466 (29%)</td>
<td headers="stat_2" class="gt_row gt_center">1,081 (22%)</td>
<td headers="stat_3" class="gt_row gt_center">2,100 (17%)</td>
<td headers="stat_4" class="gt_row gt_center">2,949 (21%)</td>
<td headers="stat_5" class="gt_row gt_center">2,598 (12%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI1</td>
<td headers="stat_1" class="gt_row gt_center">408 (25%)</td>
<td headers="stat_2" class="gt_row gt_center">1,560 (32%)</td>
<td headers="stat_3" class="gt_row gt_center">3,240 (27%)</td>
<td headers="stat_4" class="gt_row gt_center">3,575 (26%)</td>
<td headers="stat_5" class="gt_row gt_center">4,282 (20%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS2</td>
<td headers="stat_1" class="gt_row gt_center">261 (16%)</td>
<td headers="stat_2" class="gt_row gt_center">978 (20%)</td>
<td headers="stat_3" class="gt_row gt_center">2,591 (21%)</td>
<td headers="stat_4" class="gt_row gt_center">3,357 (24%)</td>
<td headers="stat_5" class="gt_row gt_center">5,071 (24%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS1</td>
<td headers="stat_1" class="gt_row gt_center">170 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">648 (13%)</td>
<td headers="stat_3" class="gt_row gt_center">1,775 (15%)</td>
<td headers="stat_4" class="gt_row gt_center">1,989 (14%)</td>
<td headers="stat_5" class="gt_row gt_center">3,589 (17%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS2</td>
<td headers="stat_1" class="gt_row gt_center">69 (4.3%)</td>
<td headers="stat_2" class="gt_row gt_center">286 (5.8%)</td>
<td headers="stat_3" class="gt_row gt_center">1,235 (10%)</td>
<td headers="stat_4" class="gt_row gt_center">870 (6.3%)</td>
<td headers="stat_5" class="gt_row gt_center">2,606 (12%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS1</td>
<td headers="stat_1" class="gt_row gt_center">17 (1.1%)</td>
<td headers="stat_2" class="gt_row gt_center">186 (3.8%)</td>
<td headers="stat_3" class="gt_row gt_center">789 (6.5%)</td>
<td headers="stat_4" class="gt_row gt_center">616 (4.5%)</td>
<td headers="stat_5" class="gt_row gt_center">2,047 (9.5%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    IF</td>
<td headers="stat_1" class="gt_row gt_center">9 (0.6%)</td>
<td headers="stat_2" class="gt_row gt_center">71 (1.4%)</td>
<td headers="stat_3" class="gt_row gt_center">268 (2.2%)</td>
<td headers="stat_4" class="gt_row gt_center">230 (1.7%)</td>
<td headers="stat_5" class="gt_row gt_center">1,212 (5.6%)</td>
<td headers="p.value" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">depth</td>
<td headers="stat_1" class="gt_row gt_center">65.00 (64.40, 65.90)</td>
<td headers="stat_2" class="gt_row gt_center">63.40 (61.30, 63.80)</td>
<td headers="stat_3" class="gt_row gt_center">62.10 (60.90, 62.90)</td>
<td headers="stat_4" class="gt_row gt_center">61.40 (60.50, 62.20)</td>
<td headers="stat_5" class="gt_row gt_center">61.80 (61.30, 62.20)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">table</td>
<td headers="stat_1" class="gt_row gt_center">58.00 (56.00, 61.00)</td>
<td headers="stat_2" class="gt_row gt_center">58.00 (56.00, 61.00)</td>
<td headers="stat_3" class="gt_row gt_center">58.00 (56.00, 59.00)</td>
<td headers="stat_4" class="gt_row gt_center">59.00 (58.00, 60.00)</td>
<td headers="stat_5" class="gt_row gt_center">56.00 (55.00, 57.00)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">price</td>
<td headers="stat_1" class="gt_row gt_center">3,282 (2,050, 5,208)</td>
<td headers="stat_2" class="gt_row gt_center">3,051 (1,145, 5,028)</td>
<td headers="stat_3" class="gt_row gt_center">2,648 (912, 5,373)</td>
<td headers="stat_4" class="gt_row gt_center">3,185 (1,046, 6,296)</td>
<td headers="stat_5" class="gt_row gt_center">1,810 (878, 4,679)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">x</td>
<td headers="stat_1" class="gt_row gt_center">6.18 (5.63, 6.70)</td>
<td headers="stat_2" class="gt_row gt_center">5.98 (5.02, 6.42)</td>
<td headers="stat_3" class="gt_row gt_center">5.74 (4.75, 6.47)</td>
<td headers="stat_4" class="gt_row gt_center">6.11 (4.80, 6.80)</td>
<td headers="stat_5" class="gt_row gt_center">5.25 (4.54, 6.44)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">y</td>
<td headers="stat_1" class="gt_row gt_center">6.10 (5.57, 6.64)</td>
<td headers="stat_2" class="gt_row gt_center">5.99 (5.02, 6.44)</td>
<td headers="stat_3" class="gt_row gt_center">5.77 (4.77, 6.51)</td>
<td headers="stat_4" class="gt_row gt_center">6.06 (4.79, 6.76)</td>
<td headers="stat_5" class="gt_row gt_center">5.26 (4.55, 6.45)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
    <tr><td headers="label" class="gt_row gt_left">z</td>
<td headers="stat_1" class="gt_row gt_center">3.97 (3.61, 4.28)</td>
<td headers="stat_2" class="gt_row gt_center">3.70 (3.07, 4.03)</td>
<td headers="stat_3" class="gt_row gt_center">3.56 (2.95, 4.02)</td>
<td headers="stat_4" class="gt_row gt_center">3.72 (2.94, 4.16)</td>
<td headers="stat_5" class="gt_row gt_center">3.23 (2.80, 3.98)</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="7"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span> <span class='gt_from_md'>Median (Q1, Q3); n (%)</span></td>
    </tr>
    <tr>
      <td class="gt_footnote" colspan="7"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>2</sup></span> <span class='gt_from_md'>Kruskal-Wallis rank sum test; Pearson’s Chi-squared test</span></td>
    </tr>
  </tfoot>
</table>
</div>

You can also customize the appearance of the table.

``` r
tbl_summary(diamonds, by = cut) |> 
  modify_header(label ~ "Variable") |> 
  modify_caption("Summary Table by Cut")
```

<div id="nrkzypvdgt" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#nrkzypvdgt table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#nrkzypvdgt thead, #nrkzypvdgt tbody, #nrkzypvdgt tfoot, #nrkzypvdgt tr, #nrkzypvdgt td, #nrkzypvdgt th {
  border-style: none;
}
&#10;#nrkzypvdgt p {
  margin: 0;
  padding: 0;
}
&#10;#nrkzypvdgt .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#nrkzypvdgt .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#nrkzypvdgt .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#nrkzypvdgt .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#nrkzypvdgt .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#nrkzypvdgt .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#nrkzypvdgt .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#nrkzypvdgt .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#nrkzypvdgt .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#nrkzypvdgt .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#nrkzypvdgt .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#nrkzypvdgt .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#nrkzypvdgt .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#nrkzypvdgt .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#nrkzypvdgt .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#nrkzypvdgt .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#nrkzypvdgt .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#nrkzypvdgt .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#nrkzypvdgt .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#nrkzypvdgt .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#nrkzypvdgt .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#nrkzypvdgt .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#nrkzypvdgt .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#nrkzypvdgt .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#nrkzypvdgt .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#nrkzypvdgt .gt_left {
  text-align: left;
}
&#10;#nrkzypvdgt .gt_center {
  text-align: center;
}
&#10;#nrkzypvdgt .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#nrkzypvdgt .gt_font_normal {
  font-weight: normal;
}
&#10;#nrkzypvdgt .gt_font_bold {
  font-weight: bold;
}
&#10;#nrkzypvdgt .gt_font_italic {
  font-style: italic;
}
&#10;#nrkzypvdgt .gt_super {
  font-size: 65%;
}
&#10;#nrkzypvdgt .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#nrkzypvdgt .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#nrkzypvdgt .gt_indent_1 {
  text-indent: 5px;
}
&#10;#nrkzypvdgt .gt_indent_2 {
  text-indent: 10px;
}
&#10;#nrkzypvdgt .gt_indent_3 {
  text-indent: 15px;
}
&#10;#nrkzypvdgt .gt_indent_4 {
  text-indent: 20px;
}
&#10;#nrkzypvdgt .gt_indent_5 {
  text-indent: 25px;
}
&#10;#nrkzypvdgt .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#nrkzypvdgt div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <caption><span class='gt_from_md'>Summary Table by Cut</span></caption>
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="label"><span class='gt_from_md'>Variable</span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_1"><span class='gt_from_md'><strong>Fair</strong><br />
N = 1,610</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_2"><span class='gt_from_md'><strong>Good</strong><br />
N = 4,906</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_3"><span class='gt_from_md'><strong>Very Good</strong><br />
N = 12,082</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_4"><span class='gt_from_md'><strong>Premium</strong><br />
N = 13,791</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_5"><span class='gt_from_md'><strong>Ideal</strong><br />
N = 21,551</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">carat</td>
<td headers="stat_1" class="gt_row gt_center">1.00 (0.70, 1.20)</td>
<td headers="stat_2" class="gt_row gt_center">0.82 (0.50, 1.01)</td>
<td headers="stat_3" class="gt_row gt_center">0.71 (0.41, 1.02)</td>
<td headers="stat_4" class="gt_row gt_center">0.86 (0.41, 1.20)</td>
<td headers="stat_5" class="gt_row gt_center">0.54 (0.35, 1.01)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">color</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td>
<td headers="stat_3" class="gt_row gt_center"><br /></td>
<td headers="stat_4" class="gt_row gt_center"><br /></td>
<td headers="stat_5" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    D</td>
<td headers="stat_1" class="gt_row gt_center">163 (10%)</td>
<td headers="stat_2" class="gt_row gt_center">662 (13%)</td>
<td headers="stat_3" class="gt_row gt_center">1,513 (13%)</td>
<td headers="stat_4" class="gt_row gt_center">1,603 (12%)</td>
<td headers="stat_5" class="gt_row gt_center">2,834 (13%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    E</td>
<td headers="stat_1" class="gt_row gt_center">224 (14%)</td>
<td headers="stat_2" class="gt_row gt_center">933 (19%)</td>
<td headers="stat_3" class="gt_row gt_center">2,400 (20%)</td>
<td headers="stat_4" class="gt_row gt_center">2,337 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,903 (18%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    F</td>
<td headers="stat_1" class="gt_row gt_center">312 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">909 (19%)</td>
<td headers="stat_3" class="gt_row gt_center">2,164 (18%)</td>
<td headers="stat_4" class="gt_row gt_center">2,331 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,826 (18%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    G</td>
<td headers="stat_1" class="gt_row gt_center">314 (20%)</td>
<td headers="stat_2" class="gt_row gt_center">871 (18%)</td>
<td headers="stat_3" class="gt_row gt_center">2,299 (19%)</td>
<td headers="stat_4" class="gt_row gt_center">2,924 (21%)</td>
<td headers="stat_5" class="gt_row gt_center">4,884 (23%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    H</td>
<td headers="stat_1" class="gt_row gt_center">303 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">702 (14%)</td>
<td headers="stat_3" class="gt_row gt_center">1,824 (15%)</td>
<td headers="stat_4" class="gt_row gt_center">2,360 (17%)</td>
<td headers="stat_5" class="gt_row gt_center">3,115 (14%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I</td>
<td headers="stat_1" class="gt_row gt_center">175 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">522 (11%)</td>
<td headers="stat_3" class="gt_row gt_center">1,204 (10.0%)</td>
<td headers="stat_4" class="gt_row gt_center">1,428 (10%)</td>
<td headers="stat_5" class="gt_row gt_center">2,093 (9.7%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    J</td>
<td headers="stat_1" class="gt_row gt_center">119 (7.4%)</td>
<td headers="stat_2" class="gt_row gt_center">307 (6.3%)</td>
<td headers="stat_3" class="gt_row gt_center">678 (5.6%)</td>
<td headers="stat_4" class="gt_row gt_center">808 (5.9%)</td>
<td headers="stat_5" class="gt_row gt_center">896 (4.2%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">clarity</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td>
<td headers="stat_3" class="gt_row gt_center"><br /></td>
<td headers="stat_4" class="gt_row gt_center"><br /></td>
<td headers="stat_5" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    I1</td>
<td headers="stat_1" class="gt_row gt_center">210 (13%)</td>
<td headers="stat_2" class="gt_row gt_center">96 (2.0%)</td>
<td headers="stat_3" class="gt_row gt_center">84 (0.7%)</td>
<td headers="stat_4" class="gt_row gt_center">205 (1.5%)</td>
<td headers="stat_5" class="gt_row gt_center">146 (0.7%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI2</td>
<td headers="stat_1" class="gt_row gt_center">466 (29%)</td>
<td headers="stat_2" class="gt_row gt_center">1,081 (22%)</td>
<td headers="stat_3" class="gt_row gt_center">2,100 (17%)</td>
<td headers="stat_4" class="gt_row gt_center">2,949 (21%)</td>
<td headers="stat_5" class="gt_row gt_center">2,598 (12%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    SI1</td>
<td headers="stat_1" class="gt_row gt_center">408 (25%)</td>
<td headers="stat_2" class="gt_row gt_center">1,560 (32%)</td>
<td headers="stat_3" class="gt_row gt_center">3,240 (27%)</td>
<td headers="stat_4" class="gt_row gt_center">3,575 (26%)</td>
<td headers="stat_5" class="gt_row gt_center">4,282 (20%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS2</td>
<td headers="stat_1" class="gt_row gt_center">261 (16%)</td>
<td headers="stat_2" class="gt_row gt_center">978 (20%)</td>
<td headers="stat_3" class="gt_row gt_center">2,591 (21%)</td>
<td headers="stat_4" class="gt_row gt_center">3,357 (24%)</td>
<td headers="stat_5" class="gt_row gt_center">5,071 (24%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VS1</td>
<td headers="stat_1" class="gt_row gt_center">170 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">648 (13%)</td>
<td headers="stat_3" class="gt_row gt_center">1,775 (15%)</td>
<td headers="stat_4" class="gt_row gt_center">1,989 (14%)</td>
<td headers="stat_5" class="gt_row gt_center">3,589 (17%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS2</td>
<td headers="stat_1" class="gt_row gt_center">69 (4.3%)</td>
<td headers="stat_2" class="gt_row gt_center">286 (5.8%)</td>
<td headers="stat_3" class="gt_row gt_center">1,235 (10%)</td>
<td headers="stat_4" class="gt_row gt_center">870 (6.3%)</td>
<td headers="stat_5" class="gt_row gt_center">2,606 (12%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    VVS1</td>
<td headers="stat_1" class="gt_row gt_center">17 (1.1%)</td>
<td headers="stat_2" class="gt_row gt_center">186 (3.8%)</td>
<td headers="stat_3" class="gt_row gt_center">789 (6.5%)</td>
<td headers="stat_4" class="gt_row gt_center">616 (4.5%)</td>
<td headers="stat_5" class="gt_row gt_center">2,047 (9.5%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    IF</td>
<td headers="stat_1" class="gt_row gt_center">9 (0.6%)</td>
<td headers="stat_2" class="gt_row gt_center">71 (1.4%)</td>
<td headers="stat_3" class="gt_row gt_center">268 (2.2%)</td>
<td headers="stat_4" class="gt_row gt_center">230 (1.7%)</td>
<td headers="stat_5" class="gt_row gt_center">1,212 (5.6%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">depth</td>
<td headers="stat_1" class="gt_row gt_center">65.00 (64.40, 65.90)</td>
<td headers="stat_2" class="gt_row gt_center">63.40 (61.30, 63.80)</td>
<td headers="stat_3" class="gt_row gt_center">62.10 (60.90, 62.90)</td>
<td headers="stat_4" class="gt_row gt_center">61.40 (60.50, 62.20)</td>
<td headers="stat_5" class="gt_row gt_center">61.80 (61.30, 62.20)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">table</td>
<td headers="stat_1" class="gt_row gt_center">58.00 (56.00, 61.00)</td>
<td headers="stat_2" class="gt_row gt_center">58.00 (56.00, 61.00)</td>
<td headers="stat_3" class="gt_row gt_center">58.00 (56.00, 59.00)</td>
<td headers="stat_4" class="gt_row gt_center">59.00 (58.00, 60.00)</td>
<td headers="stat_5" class="gt_row gt_center">56.00 (55.00, 57.00)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">price</td>
<td headers="stat_1" class="gt_row gt_center">3,282 (2,050, 5,208)</td>
<td headers="stat_2" class="gt_row gt_center">3,051 (1,145, 5,028)</td>
<td headers="stat_3" class="gt_row gt_center">2,648 (912, 5,373)</td>
<td headers="stat_4" class="gt_row gt_center">3,185 (1,046, 6,296)</td>
<td headers="stat_5" class="gt_row gt_center">1,810 (878, 4,679)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">x</td>
<td headers="stat_1" class="gt_row gt_center">6.18 (5.63, 6.70)</td>
<td headers="stat_2" class="gt_row gt_center">5.98 (5.02, 6.42)</td>
<td headers="stat_3" class="gt_row gt_center">5.74 (4.75, 6.47)</td>
<td headers="stat_4" class="gt_row gt_center">6.11 (4.80, 6.80)</td>
<td headers="stat_5" class="gt_row gt_center">5.25 (4.54, 6.44)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">y</td>
<td headers="stat_1" class="gt_row gt_center">6.10 (5.57, 6.64)</td>
<td headers="stat_2" class="gt_row gt_center">5.99 (5.02, 6.44)</td>
<td headers="stat_3" class="gt_row gt_center">5.77 (4.77, 6.51)</td>
<td headers="stat_4" class="gt_row gt_center">6.06 (4.79, 6.76)</td>
<td headers="stat_5" class="gt_row gt_center">5.26 (4.55, 6.45)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">z</td>
<td headers="stat_1" class="gt_row gt_center">3.97 (3.61, 4.28)</td>
<td headers="stat_2" class="gt_row gt_center">3.70 (3.07, 4.03)</td>
<td headers="stat_3" class="gt_row gt_center">3.56 (2.95, 4.02)</td>
<td headers="stat_4" class="gt_row gt_center">3.72 (2.94, 4.16)</td>
<td headers="stat_5" class="gt_row gt_center">3.23 (2.80, 3.98)</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="6"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span> <span class='gt_from_md'>Median (Q1, Q3); n (%)</span></td>
    </tr>
  </tfoot>
</table>
</div>

To export the table as a Word document, use the `gtsave()` function.
Note that we first use the `as_gt()` function to convert the
`tbl_summary()` output to a `gt` object, and load the `gt` package in
order to use the Word export function defined for the `gt` package.

``` r
library(gt)

tbl_summary(diamonds, by = cut) |> 
  as_gt() |> 
  gtsave("summary_table.docx")
```

If you use LaTeX, you can also export as a LaTeX table, also relying on
the `gt` package.

``` r
tbl_summary(diamonds, by = cut) |> 
  as_gt() |> 
  gtsave("summary_table.tex")
```

# Hypothesis testing

Most of the simple statistical tests are from base R, so they don’t rely
on tidy principles, but many are compatible with tidy workflows to at
least some extent. In the following we’ll cover some of the key methods
that show up in methods and statistics courses at UCR. In addition, the
`tidy()` function from the `broom` package converts most text output
into simple tibbles, which are useful for exporting and visualizing
results.

``` r
library(broom)
```

## t-tests

We can run one and two samples t-tests to evaluate group means with the
`t.test()` function. The function supports various options and model
specifications: a simple one-sample t-test only requires specifying the
variable of interest, either with `x = data$variable` or
`x = variable, data = data` syntax. For two-sample t-tests, we can use
the formula syntax `y ~ x` to specify the dependent and independent
variables or the `x` and `y` (and optionally `data`) arguments.
Additional options include specifying the alternative hypothesis, the
confidence level, the value of $\mu$, and whether we want a paired
t-test and assume equal variances. Helper functions such as `tidy()`
convert the Console output to an easy-to-export tibble of results.

To demonstrate two-sample t-tests, we define a subset of the data that
contains only two possible values of `cut`.

``` r
# simple t-test (H0: mean=mu)
t.test(diamonds$carat, mu = 1)
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  diamonds$carat
    ## t = -99.003, df = 53939, p-value < 2.2e-16
    ## alternative hypothesis: true mean is not equal to 1
    ## 95 percent confidence interval:
    ##  0.7939395 0.8019400
    ## sample estimates:
    ## mean of x 
    ## 0.7979397

``` r
# define data subsample of fair and good diamonds to have only two groups of cut
diamonds_sub <- diamonds |> 
  filter(cut %in% c("Fair", "Good"))

# can also use data argument instead of data$...
# price ~ cut is formula specification: variable ~ group
# H0: fair and good diamonds have the same average price
t.test(price ~ cut, alternative = "greater", data = diamonds_sub)
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  price by cut
    ## t = 4.1684, df = 2822.3, p-value = 1.58e-05
    ## alternative hypothesis: true difference in means between group Fair and group Good is greater than 0
    ## 95 percent confidence interval:
    ##  260.2001      Inf
    ## sample estimates:
    ## mean in group Fair mean in group Good 
    ##           4358.758           3928.864

``` r
# tidy() function turns results into a tibble
t.test(price ~ cut, alternative = "greater", data = diamonds_sub) |> tidy()
```

    ## # A tibble: 1 × 10
    ##   estimate estimate1 estimate2 statistic   p.value parameter conf.low conf.high
    ##      <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>    <dbl>     <dbl>
    ## 1     430.     4359.     3929.      4.17 0.0000158     2822.     260.       Inf
    ## # ℹ 2 more variables: method <chr>, alternative <chr>

## Correlation test

The `cor.test()` function calculates the correlation between two
variables. Again, the function supports various specifications: `x` and
`y` arguments, formula syntax (see below for an example), and the `data`
argument.

``` r
cor.test( ~ price + carat, data = diamonds)
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  price and carat
    ## t = 551.41, df = 53938, p-value < 2.2e-16
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.9203098 0.9228530
    ## sample estimates:
    ##       cor 
    ## 0.9215913

``` r
# tidy() function turns results into a tibble
cor.test( ~ price + carat, data = diamonds) |> tidy()
```

    ## # A tibble: 1 × 8
    ##   estimate statistic p.value parameter conf.low conf.high method     alternative
    ##      <dbl>     <dbl>   <dbl>     <int>    <dbl>     <dbl> <chr>      <chr>      
    ## 1    0.922      551.       0     53938    0.920     0.923 Pearson's… two.sided

## Simple regression

A key building block of statistical analysis is linear regression. The
`lm()` function fits a linear model to the data, with a wide range of
specifications, passed as the formula argument (first argument if
unnamed). The formula syntax is `y ~ x`, where `y` is the dependent
variable and `x` is the independent variable. Again, optional function
arguments allow for a lot of customization, but the default settings are
sufficient for most applications. Helper functions such as `tidy()` and
`summary()` provide extensive summaries of the model fit and
coefficients, and `tbl_regression()` from the `gtsummary` package
creates neat tables of the results. Assigning the result of a model to
an object saves computational time, as then we can work with the results
without having to re-run the analysis every time.

``` r
# assign outcome to object
fit <- lm(price ~ carat, data = diamonds)

# extensive result summary
fit |> summary()
```

    ## 
    ## Call:
    ## lm(formula = price ~ carat, data = diamonds)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -18585.3   -804.8    -18.9    537.4  12731.7 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -2256.36      13.06  -172.8   <2e-16 ***
    ## carat        7756.43      14.07   551.4   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1549 on 53938 degrees of freedom
    ## Multiple R-squared:  0.8493, Adjusted R-squared:  0.8493 
    ## F-statistic: 3.041e+05 on 1 and 53938 DF,  p-value: < 2.2e-16

``` r
# tidy coefficients
fit |> tidy()
```

    ## # A tibble: 2 × 5
    ##   term        estimate std.error statistic p.value
    ##   <chr>          <dbl>     <dbl>     <dbl>   <dbl>
    ## 1 (Intercept)   -2256.      13.1     -173.       0
    ## 2 carat          7756.      14.1      551.       0

``` r
# display-ready table with gtsummary
tbl_regression(fit)
```

<div id="ktpzlmxbtu" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#ktpzlmxbtu table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#ktpzlmxbtu thead, #ktpzlmxbtu tbody, #ktpzlmxbtu tfoot, #ktpzlmxbtu tr, #ktpzlmxbtu td, #ktpzlmxbtu th {
  border-style: none;
}
&#10;#ktpzlmxbtu p {
  margin: 0;
  padding: 0;
}
&#10;#ktpzlmxbtu .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#ktpzlmxbtu .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#ktpzlmxbtu .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#ktpzlmxbtu .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#ktpzlmxbtu .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#ktpzlmxbtu .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#ktpzlmxbtu .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#ktpzlmxbtu .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#ktpzlmxbtu .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#ktpzlmxbtu .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#ktpzlmxbtu .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#ktpzlmxbtu .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#ktpzlmxbtu .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#ktpzlmxbtu .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#ktpzlmxbtu .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ktpzlmxbtu .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#ktpzlmxbtu .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#ktpzlmxbtu .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#ktpzlmxbtu .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ktpzlmxbtu .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#ktpzlmxbtu .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ktpzlmxbtu .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#ktpzlmxbtu .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ktpzlmxbtu .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#ktpzlmxbtu .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#ktpzlmxbtu .gt_left {
  text-align: left;
}
&#10;#ktpzlmxbtu .gt_center {
  text-align: center;
}
&#10;#ktpzlmxbtu .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#ktpzlmxbtu .gt_font_normal {
  font-weight: normal;
}
&#10;#ktpzlmxbtu .gt_font_bold {
  font-weight: bold;
}
&#10;#ktpzlmxbtu .gt_font_italic {
  font-style: italic;
}
&#10;#ktpzlmxbtu .gt_super {
  font-size: 65%;
}
&#10;#ktpzlmxbtu .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#ktpzlmxbtu .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#ktpzlmxbtu .gt_indent_1 {
  text-indent: 5px;
}
&#10;#ktpzlmxbtu .gt_indent_2 {
  text-indent: 10px;
}
&#10;#ktpzlmxbtu .gt_indent_3 {
  text-indent: 15px;
}
&#10;#ktpzlmxbtu .gt_indent_4 {
  text-indent: 20px;
}
&#10;#ktpzlmxbtu .gt_indent_5 {
  text-indent: 25px;
}
&#10;#ktpzlmxbtu .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#ktpzlmxbtu div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="label"><span class='gt_from_md'><strong>Characteristic</strong></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="estimate"><span class='gt_from_md'><strong>Beta</strong></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="conf.low"><span class='gt_from_md'><strong>95% CI</strong></span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="p.value"><span class='gt_from_md'><strong>p-value</strong></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left">carat</td>
<td headers="estimate" class="gt_row gt_center">7,756</td>
<td headers="conf.low" class="gt_row gt_center">7,729, 7,784</td>
<td headers="p.value" class="gt_row gt_center"><0.001</td></tr>
  </tbody>
  &#10;  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="4"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span> <span class='gt_from_md'>CI = Confidence Interval</span></td>
    </tr>
  </tfoot>
</table>
</div>

## Multiple regression

Multiple regression extends simple regression to multiple independent
variables. The only difference is the formula specification, which now
connects multiple independent variables with `+` signs. The formula
specification also allows for interactions between variables, which can
be specified with `*` (if the main effects should be included) or `:`
(for only the interaction term). The `DV ~ .~` syntax includes all
variables in the data except the dependent variable as independent
variables.

``` r
lm(price ~ x + y + z + table + depth, data = diamonds) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = price ~ x + y + z + table + depth, data = diamonds)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ##  -9994  -1256   -197    945  32470 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -8770.608    509.448 -17.216  < 2e-16 ***
    ## x            2918.492     43.346  67.330  < 2e-16 ***
    ## y             205.086     31.555   6.499 8.13e-11 ***
    ## z              91.814     54.802   1.675   0.0939 .  
    ## table         -84.855      3.813 -22.255  < 2e-16 ***
    ## depth         -10.501      6.661  -1.576   0.1149    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1852 on 53934 degrees of freedom
    ## Multiple R-squared:  0.7846, Adjusted R-squared:  0.7846 
    ## F-statistic: 3.929e+04 on 5 and 53934 DF,  p-value: < 2.2e-16

``` r
# all variables in data
lm(price ~ ., data = diamonds) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = price ~ ., data = diamonds)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -21376.0   -592.4   -183.5    376.4  10694.2 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error  t value Pr(>|t|)    
    ## (Intercept)  5753.762    396.630   14.507  < 2e-16 ***
    ## carat       11256.978     48.628  231.494  < 2e-16 ***
    ## cut.L         584.457     22.478   26.001  < 2e-16 ***
    ## cut.Q        -301.908     17.994  -16.778  < 2e-16 ***
    ## cut.C         148.035     15.483    9.561  < 2e-16 ***
    ## cut^4         -20.794     12.377   -1.680  0.09294 .  
    ## color.L     -1952.160     17.342 -112.570  < 2e-16 ***
    ## color.Q      -672.054     15.777  -42.597  < 2e-16 ***
    ## color.C      -165.283     14.725  -11.225  < 2e-16 ***
    ## color^4        38.195     13.527    2.824  0.00475 ** 
    ## color^5       -95.793     12.776   -7.498 6.59e-14 ***
    ## color^6       -48.466     11.614   -4.173 3.01e-05 ***
    ## clarity.L    4097.431     30.259  135.414  < 2e-16 ***
    ## clarity.Q   -1925.004     28.227  -68.197  < 2e-16 ***
    ## clarity.C     982.205     24.152   40.668  < 2e-16 ***
    ## clarity^4    -364.918     19.285  -18.922  < 2e-16 ***
    ## clarity^5     233.563     15.752   14.828  < 2e-16 ***
    ## clarity^6       6.883     13.715    0.502  0.61575    
    ## clarity^7      90.640     12.103    7.489 7.06e-14 ***
    ## depth         -63.806      4.535  -14.071  < 2e-16 ***
    ## table         -26.474      2.912   -9.092  < 2e-16 ***
    ## x           -1008.261     32.898  -30.648  < 2e-16 ***
    ## y               9.609     19.333    0.497  0.61918    
    ## z             -50.119     33.486   -1.497  0.13448    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1130 on 53916 degrees of freedom
    ## Multiple R-squared:  0.9198, Adjusted R-squared:  0.9198 
    ## F-statistic: 2.688e+04 on 23 and 53916 DF,  p-value: < 2.2e-16

``` r
# interactions
lm(price ~ x * y, data = diamonds) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = price ~ x * y, data = diamonds)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -41574   -575    -16    286  38459 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 11572.154    161.256   71.76   <2e-16 ***
    ## x            -507.815     33.540  -15.14   <2e-16 ***
    ## y           -5300.961     42.598 -124.44   <2e-16 ***
    ## x:y           752.458      4.618  162.93   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1523 on 53936 degrees of freedom
    ## Multiple R-squared:  0.8542, Adjusted R-squared:  0.8542 
    ## F-statistic: 1.053e+05 on 3 and 53936 DF,  p-value: < 2.2e-16

## ANOVA

Analysis of variance (ANOVA) is a generalization of the t-test to
multiple groups. The `aov()` function fits an ANOVA model to the data,
with the formula syntax `y ~ x`, where `y` is the dependent variable and
`x` is the independent variable. The same helper functions as with
`lm()` can be used to summarize the results.

Note that ANOVA is a specific case of a linear regression model, so the
results are equivalent to those of a linear regression model with a
categorical independent variable.

``` r
anova_fit <- aov(price ~ cut, data = diamonds)

summary(anova_fit)
```

    ##                Df    Sum Sq   Mean Sq F value Pr(>F)    
    ## cut             4 1.104e+10 2.760e+09   175.7 <2e-16 ***
    ## Residuals   53935 8.474e+11 1.571e+07                   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
tidy(anova_fit)
```

    ## # A tibble: 2 × 6
    ##   term         df         sumsq      meansq statistic    p.value
    ##   <chr>     <dbl>         <dbl>       <dbl>     <dbl>      <dbl>
    ## 1 cut           4  11041745359. 2760436340.      176.  8.43e-150
    ## 2 Residuals 53935 847431390159.   15712087.       NA  NA

``` r
# equivalent regression
lm(price ~ cut, data = diamonds) |> summary()
```

    ## 
    ## Call:
    ## lm(formula = price ~ cut, data = diamonds)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ##  -4258  -2741  -1494   1360  15348 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  4062.24      25.40 159.923  < 2e-16 ***
    ## cut.L        -362.73      68.04  -5.331  9.8e-08 ***
    ## cut.Q        -225.58      60.65  -3.719    2e-04 ***
    ## cut.C        -699.50      52.78 -13.253  < 2e-16 ***
    ## cut^4        -280.36      42.56  -6.588  4.5e-11 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3964 on 53935 degrees of freedom
    ## Multiple R-squared:  0.01286,    Adjusted R-squared:  0.01279 
    ## F-statistic: 175.7 on 4 and 53935 DF,  p-value: < 2.2e-16
