---
title: "Data Center Apprenticeship:\nWrangling and visualizing spatial data in R"
subtitle: "Spring 2024" 
date: "Last updated: 2024-01-20"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Data](#data)
- [Workshop code](#workshop-code)

# Data

Data on organism occurrences can be directly downloaded in a csv file
from [gbif.org](https://www.gbif.org/).  
Data on nitrogen deposition levels in the Netherlands can be downloaded
from RIVM website:
[rivm.nl](https://www.rivm.nl/gcn-gdn-kaarten/depositiekaarten/cijfers-achter-depositiekaarten/gdn-depositiebestanden-achterliggende-jaren).

``` r
library(tidyverse)
library(terra)

# import data data directly from GitHub

Nitrogen_2018 <- 
  rast("https://raw.githubusercontent.com/ucrdatacenter/projects/main/SCIENVI201/2022h1/Data/nitrogen/depo_ntot_2018.asc")

Chaffinch_2018 <- 
  read_delim("https://raw.githubusercontent.com/ucrdatacenter/projects/main/SCIENVI201/2022h1/Data/chaffinch/chaffinch_2018.csv",
             delim = "\t", escape_double = FALSE, trim_ws = TRUE)
```

# Workshop code

The code from the workshop is available
[here](https://github.com/ucrdatacenter/projects/blob/main/apprenticeship/7_spatial/spatial_code.R).
