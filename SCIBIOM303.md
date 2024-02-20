---
layout: page
title: "SCIBIOM303\nMechanisms of Disease"
subtitle: "Spring 2024"
date: "Last updated: 2024-02-20"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

``` r
library(tidyverse)
```

Load the data:

``` r
read_csv("https://github.com/ucrdatacenter/projects/raw/main/SCIBIOM303/2024h1/covid-containment-and-health-index.csv")

read_csv("https://github.com/ucrdatacenter/projects/raw/main/SCIBIOM303/2024h1/owid-covid-data.csv")
```

Links to original data:

- [Stringency
  index](https://ourworldindata.org/covid-stringency-index#:~:text=The%20stringency%20index%20is%20a,100%20(100%20%3Dstrictest))
- [COVID cases](https://ourworldindata.org/covid-cases)
