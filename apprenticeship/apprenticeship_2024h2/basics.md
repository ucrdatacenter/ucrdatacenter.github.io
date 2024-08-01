---
title: "Data Center Apprenticeship:\nR basics"
subtitle: "June 2024" 
date: "Last updated: 2024-08-01"
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
- [Data analysis workflow](#data-analysis-workflow)
  - [Finding and importing data](#finding-and-importing-data)
  - [Data wrangling with `dplyr`](#data-wrangling-with-dplyr)
  - [Exploratory analysis](#exploratory-analysis)
    - [Summary statistics](#summary-statistics)
    - [Data visualization with
      `ggplot2`](#data-visualization-with-ggplot2)
  - [Hypothesis testing / modelling](#hypothesis-testing-modelling)
- [Other resources](#other-resources)

# Installation and setup: prepare in advance

Before the first workshop, please make sure that you have access to R
and RStudio on your laptop. If you need help with the installation,
please follow [this tutorial](../../../tutorial/r_install).

In addition the [R basics tutorial](../../../tutorial/r_basics) on the
Data Center website explains the RStudio interface, and shows how to
create a new project and how to install packages.

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

**Please use the links in the headers to access the individual
sections.**

# [Introduction to R](intro)

# Data analysis workflow

## [Finding and importing data](import)

## [Data wrangling with `dplyr`](clean)

## Exploratory analysis

### [Summary statistics](summary)

### [Data visualization with `ggplot2`](ggplot)

## [Hypothesis testing / modelling](stats)

# Other resources

- [Data Center collection of resources](../../../tutorial/links/)

Note: a major advantage of using R is the ability to define custom
functions. So if some of the methods and workflows used in RMET classes
do not have sufficiently straightforward R implementations, UCR can
develop its own functions (and even package) to streamline the process
and give students quick and easy functions. See for example the `qacr`
package developed for a similar purpose at Wesleyan University: [qacr
website](https://rkabacoff.github.io/qacr/articles/qacr.html).
