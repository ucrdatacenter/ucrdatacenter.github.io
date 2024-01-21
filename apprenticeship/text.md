---
title: "Data Center Apprenticeship:\nText analysis in R"
subtitle: "Spring 2024" 
date: "Last updated: 2024-01-21"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Data](#data)

# Data

You can download the NASA metadata from
[GitHub](https://raw.githubusercontent.com/ucrdatacenter/projects/main/apprenticeship/10_text/NASAmetadata.zip).
After downloading, unzip the file in your working directory and import
`metadata.JSON` using the `fromJSON()` function from the `jsonlite`
package.
