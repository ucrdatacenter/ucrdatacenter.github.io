---
layout: page
title: "Getting started with R and RStudio"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

## First steps

This tutorial assumes that you have already installed R and RStudio. If
not, please follow [this installation tutorial](../r_install).

A good resource to get a basic familiarity with the setup of R and some
key definitions is [A (very) short introduction to
R](https://github.com/ClaudiaBrauer/A-very-short-introduction-to-R/blob/master/documents/A%20(very)%20short%20introduction%20to%20R.pdf).

Please watch [this video
(4:17)](https://vimeo.com/1011759671/826f89f9aa), then read and follow
along with the written tutorial below.

## Creating a project in RStudio

It is convenient to create an R project for each assignment that you are
working on. A project is basically a folder that stores all files
related to the assignment.

You can create a project as follows:

- Open RStudio and click on “Project: (None)” in the top right corner.
- Open the dropdown window and click on “New Project….”
- In the popup window select “New Directory”, then “New Project.”
- Choose a sensible name for your project and enter it as the Directory
  Name. You can either use the default file path or change it by
  clicking “Browse…” next to “Create project as a subdirectory of:.”
- Finally, click on “Create project.”

After a project is created, there are two easy ways of accessing it. You
can either use the same dropdown window in the top right corner of
RStudio that you used to create the project, and click on the name of
the project there, or you can find the project folder within your files
and click on the file with the .Rproj extension.

## The RStudio environment

When you open RStudio, your screen is most divided into 3 or 4 panels
(depending on whether you have any open scripts or data viewer tabs).
The most important tabs are the Console, the Environment, Files, Plots,
Help, Viewer, and the script/data viewer pane.

The Console runs any code just by pressing Enter. It runs code and
displays output. It is good practice to run small functions that don’t
need to be saved, such as installing packages or searching for help
files directly from the Console. When you run a script, your code
automatically gets sent to the Console.

The Environment tab lists all objects currently defined in your R
session. You can work with them within R, but they are not saved
anywhere outside of R, and therefore disappear when your session is
over. If you click on the name of a dataframe in the Environment tab, it
opens the full data in the data viewer.

The Files tab lists the files in your current working directory by
default, but allows you to look at the contents of the other folders on
your computer as well. It is equivalent to browsing your file explorer.

The Plots and Viewer tabs display the static and and interactive plots
you create, while the Help tab lets you browse help files.

## Working with scripts

Scripts are the basis of reproducible workflows: you save all your code
into a script (basically a text file), and you can use that file to
rerun your analysis and get the same result every time. It also makes
your code easier to edit and gives you a clear overview.

You can open a new script by File -\> New File -\> R Script or by the
keyboard shortcut Control/Command + Shift + N. You can run parts of you
script by selecting the relevant lines and clicking “Run” or using the
shortcut Control/Command + Enter. Control/Command + Shift + Enter runs
your whole script. Make sure to save your script regularly.

## Installing packages

Most of the time when you work with R, you’ll need to use functions or
data from packages next to the base R functions that are automatically
loaded when you open R.

One such package that you should almost always load when working with R
is `tidyverse`, which is a collection of packages that allow clean
workflows of data import, cleaning and manipulation in R. The `ggplot2`
package that you can use to create figures is one of these included
packages.

You need to install each package only once, but you need to load them
every time you open and use R. It is good practice to load the package
on the top of each script. This is how you would install and load
`tidyverse` (and any other package).

``` r
install.packages("tidyverse") # install the package
```

``` r
library(tidyverse) # load the package
```
