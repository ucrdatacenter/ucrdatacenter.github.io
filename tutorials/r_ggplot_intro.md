---
layout: page
title: "Data visualization: introduction to `ggplot`"
date: "Last updated: 2025-02-04"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
---

## Introduction

In this tutorial you won’t make any plots, but you will learn the logic
that builds up all plots in R. So please use this explanation together
with another data visualization tutorial that shows the types of plots
most relevant to your data.

## The logic of `ggplot`

`ggplot2` is a powerful R package for creating figures. It is a part of
the `tidyverse` collection, so to use `ggplot`, you need to load
`tidyverse`. Figures made with `ggplot` are built from several layers.
You always use the same basic code structure to create a wide range of
figures:

1.  The `ggplot()` function creates a blank canvas for you to work on.
2.  Geoms add the visual elements, such as points, lines, bars, or other
    shapes.
3.  Other specifications can include changing axis settings, setting the
    theme, adding labels, etc.
4.  You connect all these different specifications to each other using
    `+` signs.

The variables that you want to display on the graph must always be
wrapped in an `aes()` function, which stands for aesthetics. This
specification tells R to determine the value of the aesthetic (x and y
axes, colors, groups, line types, etc.) based on the value of the
variable. `aes()` can be specified both in the main `ggplot()` function
(in which case it will apply to all geoms) or within a `geom_...()`
function (then it only applies to that geom).

For line charts you use `geom_line()`, and for scatterplots
`geom_point()`. You can fit a line on your scatterplot with
`geom_smooth()`. If you want to fit a straight line, add `method = "lm"`
as an argument inside `geom_smooth()`.

If you want to change the color of a point/line based on the value of a
variable, specify `color = variable` **inside** the `aes()` function. If
you would like all points/lines to be a particular color, specify
`color = "blue"` **outside** the `aes()` function but still inside the
geom you’d like to modify. Each geom’s help file lists all
characteristics that you can modify.

## Saving plots

Once you make a plot, you can save `ggplot` objects to use outside of
the R environment with the `ggsave` function. You can specify an
existing `ggplot` object as the first argument, but by default
`ggsave()` saves the last plot displayed in your Plots panel. You always
need to specify the file path of the saved plot, including the preferred
file format (e.g. .png, .jpg, .pdf). You can adjust the plot size with
the `scale` argument or by specifying the height and width in your
preferred units (the default units are inches).

``` r
# Save last plot
ggsave("figures/plot1.png", scale = 1.5)
```
