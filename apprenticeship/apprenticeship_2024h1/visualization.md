---
title: "Data Center Apprenticeship:\nData visualization"
subtitle: "Spring 2024" 
date: "Last updated: 2024-08-01"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
always_allow_html: true
---

- [Introduction](#introduction)
- [Visualization with ggplot2](#visualization-with-ggplot2)
  - [Packages and data](#packages-and-data)
  - [Basic plots](#basic-plots)
  - [Additional aesthetics and
    legends](#additional-aesthetics-and-legends)
  - [Other geoms](#other-geoms)
  - [Scales](#scales)
  - [Coordinate systems](#coordinate-systems)
  - [Multiple plots](#multiple-plots)
    - [Using facets](#using-facets)
    - [Combining independent plots with
      `patchwork`](#combining-independent-plots-with-patchwork)
  - [Customizing plot elements](#customizing-plot-elements)
  - [Saving plots](#saving-plots)
- [Other plotting packages and plot
  types](#other-plotting-packages-and-plot-types)
  - [Correlation scatterplot matrix with
    `GGally`](#correlation-scatterplot-matrix-with-ggally)
  - [Pie chart](#pie-chart)
  - [3-D scatterplot](#d-scatterplot)
  - [Alluvial/Sankey diagrams with `ggalluvial` and
    `networkD3`](#alluvialsankey-diagrams-with-ggalluvial-and-networkd3)
  - [Interactive charts with `plotly`](#interactive-charts-with-plotly)

*Schedule:*

- day 1: finish coordinate systems, maybe start with facets
- day 2: (facets), patchwork, theme, other packages

# Introduction

This tutorial introduces data visualization in R, primarily using the
`ggplot2` package (included in `tidyverse`). The tutorial is based on
[*A ggplot2 Tutorial for Beautiful Plotting in
R*](https://cedricscherer.netlify.app/2019/08/05/a-ggplot2-tutorial-for-beautiful-plotting-in-r/)
by Cédric Scherer and [*Modern Data Visualization with
R*](https://rkabacoff.github.io/datavis/index.html) by Robert Kabacoff.

# Visualization with ggplot2

## Packages and data

You will use the following packages and dataset to practice data
visualization. The data contains information about Chicago’s weather on
a daily basis between 1997–2000.

``` r
library(tidyverse)
library(patchwork) # multiple plots
library(GGally) # correlation scatterplot matrix
library(scatterplot3d) # 3-D scatterplots
library(ggalluvial) # static alluvial diagram
library(networkD3) # interactive alluvial diagram
library(plotly) # interactive plots

# Load the data from GitHub
chic <- read_csv("https://raw.githubusercontent.com/ucrdatacenter/projects/main/apprenticeship/2024h1/3_visualization/chicago.csv")
```

## Basic plots

The `ggplot2` package builds up figures in layers, by adding elements
one at a time. You always start with a base `ggplot` where you specify
the data used by the plot and possibly the variables to place on each
axis. These variables are specified within an `aes()` function, which
stands for aesthetics.

The `ggplot()` function in itself only creates a blank canvas; we need
to add so-called geoms to actually plot the data. You can choose from a
wide range of geoms, and also use multiple geoms in one plot. You can
add elements to a `ggplot` objects with the `+` sign. You should think
of the `+` sign in `ggplot` workflows in the same way you think of the
pipe operators in data wrangling workflows.

``` r
# Create the base of a plot with date on the x-axis and temperature on the y-axis
ggplot(chic, aes(x = date, y = temp))
```

![](visualization_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
# Create a scatterplot
ggplot(chic, aes(x = date, y = temp)) + 
  geom_point()
```

![](visualization_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

``` r
# Create a line plot
ggplot(chic, aes(x = date, y = temp)) + 
  geom_line()
```

![](visualization_files/figure-gfm/unnamed-chunk-2-3.png)<!-- -->

``` r
# Combine both points and lines in the plot
ggplot(chic, aes(x = date, y = temp)) +
  geom_point() +
  geom_line()
```

![](visualization_files/figure-gfm/unnamed-chunk-2-4.png)<!-- -->

``` r
# Customize the appearance of points and lines
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick", shape = "diamond", size = 2) + 
  geom_line(color = "firebrick", linetype = "dotted", size = .3) + 
  theme_light() # Apply a light theme
```

![](visualization_files/figure-gfm/unnamed-chunk-2-5.png)<!-- -->

The previous example used the `theme_light()` function to change the
design of the plot. Instead of specifying it per individual plot, you
can change the default setting for all future plots with the
`theme_set()` function.

``` r
# Set the default theme for all following plots
theme_set(theme_light())
```

There are multiple ways to make axis titles more informative, such as
the `xlab()` and `ylab()` functions or the `x` and `y` arguments in the
`labs()` function. These elements can be added to a `ggplot` object just
like any geom, theme, or other customization option.

``` r
# Add axis labels with xlab() and ylab()
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  xlab("Year") +
  ylab("Temperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
# Add axis labels with labs(), use math expressions
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  labs(x = "Year", y = expression(paste("Temperature (", degree ~ F, ")")))
```

![](visualization_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

## Additional aesthetics and legends

In addition to using the x and y axes to show variable values, you can
use other characteristics of geoms to vary based on variables. You can
add these additional characteristics – such as color, fill, size, shape
– to the `aes()` function.

Notice that R treats characters as factors, and arranges them in
alphabetical order. If you would like to change this default behavior
because the variable has another meaningful order (e.g. for seasons
Spring should come after Winter), you can convert the variable to a
factor with the levels defined in the correct order. The code below is
also an example of how you can transition from a pipe workflow of data
wrangling to immediately visualizing the data without needing to save
the intermediate dataset as a separate object.

``` r
# Color the scatterplot points by season
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
chic |> 
  # Convert season to a factor with seasons in the correct order
  mutate(season = factor(season, levels = c("Winter", "Spring", "Summer", "Autumn"))) |> 
  # Determine the color and shape of the points by season
  ggplot(aes(x = date, y = temp, color = season, shape = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

If a step of data wrangling should apply to all plots, it is easier to
save the resulting data as a new object or overwrite the original data
(if it doesn’t lead to a loss of information).

``` r
# Convert season to a factor in the original data
chic <- chic |> 
  mutate(season = factor(season, levels = c("Winter", "Spring", "Summer", "Autumn")))
```

## Other geoms

So far you have seen point and line geoms, but there are many more. The
[R Graph Gallery](https://r-graph-gallery.com/) provides a long list of
common plot types, and so do Chapters 4 and 5 of [*Modern Data
Visualization with R*](https://rkabacoff.github.io/datavis/index.html).
Both resources group geoms by the type of variable(s) plotted.

For the frequency distribution of a continuous variable, you’d often use
a histogram or density plot, while for the frequencies of a categorical
variable you’d use a bar chart.

``` r
# Histogram of temperatures
ggplot(chic, aes(temp)) +
  geom_histogram(fill = "grey", color = "red")
```

![](visualization_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# Density plot of temperatures
ggplot(chic, aes(temp)) +
  geom_density(fill = "grey", alpha = 0.5)
```

![](visualization_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

``` r
# Density plot of temperatures per season
ggplot(chic, aes(temp, fill = season)) +
  geom_density(alpha = 0.3)
```

![](visualization_files/figure-gfm/unnamed-chunk-7-3.png)<!-- -->

``` r
# Number of observations per month
ggplot(chic, aes(month)) +
  geom_bar()
```

![](visualization_files/figure-gfm/unnamed-chunk-7-4.png)<!-- -->

``` r
# Number of observations per month, colors by year
ggplot(chic, aes(month, fill = factor(year))) +
  geom_bar()
```

![](visualization_files/figure-gfm/unnamed-chunk-7-5.png)<!-- -->

``` r
# Number of observations per month, colors by year
ggplot(chic, aes(season, fill = factor(year))) +
  geom_bar(position = "dodge")
```

![](visualization_files/figure-gfm/unnamed-chunk-7-6.png)<!-- --> To
make relationships between two continuous variables clearer, you can add
smoothing curves – you can keep the curve flexible or restrict it to a
straight line.

``` r
# Add a smooth curve to the scatterplot
ggplot(chic, aes(date, temp)) +
  geom_point() +
  geom_smooth() +
  labs(x = "Year", y = "Temperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
# Use a linear fit and remove confidence interval around the line
ggplot(chic, aes(date, temp)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) +
  labs(x = "Year", y = "Temperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

You can use text as geoms as well, either on top of other geoms to label
them, or as the main data markers. In this case the y-coordinates of the
labels correspond to the average temperature of the season.

``` r
# Plot the mean temperature per season
chic |> 
  # Calculate mean temperature per year and season
  group_by(year, season) |> 
  summarize(temp = mean(temp)) |> 
  ggplot(aes(year, temp)) +
  geom_text(aes(label = season)) +
  labs(x = NULL, y = "Mean temperature per season")
```

![](visualization_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
# geom_label is the same as geom_text but with a filled background
chic |> 
  group_by(year, season) |> 
  summarize(temp = mean(temp)) |> 
  ggplot(aes(year, temp)) +
  # Fill label background per season
  geom_label(aes(label = season, fill = season)) +
  labs(x = NULL, y = "Mean temperature per season")
```

![](visualization_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

You can use various other geoms to highlight particular points or
boundaries of your plot, e.g. by adding horizontal or vertical lines at
key locations. An easy way to highlight particular observations is to
add a new layer of geoms, where instead of the full dataset you use a
filtered version with only the highlighted observations. In that case
you can override the global data choice in `ggplot()` by adding a `data`
argument within a geom. In this case `chic |> filter(yday %in% 358:360)`
keeps the Chistmas period in all years, and highlights them in red. The
dashed blue line at 32F temperature (0 Celsius) makes it easy to
determine whether a particular year had below-freezing temperatures on
Christmas.

``` r
ggplot(chic, aes(date, temp)) +
  geom_point(color = "grey70", alpha = 0.5, size = 2) +
  # Highlight selected points: Christmas from each year
  geom_point(data = chic |> filter(yday %in% 358:360), color = "red", size = 2) +
  # Add a horizontal line at temp == 32
  geom_hline(yintercept = 32, color = "blue", linetype = "dashed", size = 1.5) +
  labs(x = "Year", y = "Temperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

If you’d like to plot intervals, ribbon geoms may be useful. For each x
(y) value the ribbon needs a minimum and maximum point along the y (x)
axis. In this case the monthly minimum and maximum temperatures form the
lower and upper bounds of the ribbon. It can also be used to plot
confidence intervals or standard errors around estimated values.

``` r
# Add a ribbon showing monthly range of temperatures
chic |> 
  # Calculate lowest and highest temperature per month
  group_by(year, month) |> 
  mutate(upper = max(temp), 
         lower = min(temp)) |> 
  ggplot() +
  # use those temperatures as upper and lower bound of a ribbon
  geom_ribbon(aes(x = date, ymin = lower, ymax = upper), alpha = 0.2) +
  geom_point(aes(date, temp))
```

![](visualization_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Heatmaps have a variety of uses; they are most commonly used to show
correlations between predictors, but they can have any two categorical
variables on the axes, and use color to show how the value of a (usually
continuous) variable differs between different combinations of the
categorical variables. For example this figure shows how average
temperature changes in different years and seasons.

``` r
# Heatmap of average temperature per season
chic |> 
  # Calculate average temperature per season
  group_by(year, season) |> 
  summarize(temp = mean(temp)) |> 
  ggplot() +
  # geom_tile with fill aesthetic creates heatmap
  geom_tile(aes(year, season, fill = temp)) +
  # Add text to display average temperatures
  geom_text(aes(year, season, label = round(temp, 1)), color = "white") +
  labs(x = NULL, y = NULL, fill = "Average\ntemperature")
```

![](visualization_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

To show how relationships evole over time, you can use `geom_path` that
connects observations based on their ordering in the original dataframe
(so make sure that your data is properly sorted, otherwise you’ll end up
with nonsense results). If you have may observations, paths can look
very cluttered (which is why the following example restricts the data to
a single month). In those cases it may be useful to add markers to
notable points, or use arrows to specify the direction of the path. In
addition to highlighting observations as you have seen before, you can
add segment geoms to draw lines or arrows on the plot. While you can
specify the coordinates of these segments within the `ggplot` workflow,
it is often clearer to store these coordinates in a separate tibble.

``` r
# Scatterplot of temperature and ozone levels in Dec 2000 - evolution over time is unclear
chic |> 
  filter(year == 2000, month == "Dec") |> 
  ggplot(aes(temp, o3)) +
  geom_point() +
  labs(x = "Temperature", y = "Ozone", title = "December 2000")
```

![](visualization_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
# Define the coordinates of an arrow pointing to Dec 1 2000
arrow_data <- tibble(
  x = 20,
  y = 25,
  xend = chic |> filter(date == ymd(20001201)) |> pull(temp),
  yend = chic |> filter(date == ymd(20001201)) |> pull(o3)
)

# Path of temperature and ozone levels make it clear how values change over time
chic |> 
  filter(year == 2000, month == "Dec") |> 
  ggplot(aes(temp, o3)) + 
  # Plot the path of temperature and ozone
  geom_path() +
  # Highlight Dec 31
  geom_point(data = filter(chic, date == ymd(20001231))) +
  # Add the previously defined arrow with geom_segment
  geom_segment(data = arrow_data, aes(x = x, y = y, xend = xend, yend = yend),
               # Do not use the global aesthetics from ggplot(aes())
               inherit.aes = FALSE, color = "red",
               # Specify that the segment is an arrow
               arrow = arrow()) +
  # Add a label to Dec 1 with coordinates defined within aes()
  geom_text(data = NULL, aes(x = 20, y = 26, label = "Dec 1, 2000"), color = "red") +
  labs(x = "Temperature", y = "Ozone", title = "December 2000")
```

![](visualization_files/figure-gfm/unnamed-chunk-13-2.png)<!-- -->

## Scales

For any aesthetic you specify, you can override the default behavior by
adding `scale_...` elements. You can manually specify legend titles,
color palettes, labels, etc.

``` r
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature") +
  # Specify the title and legend labels of the color scale
  scale_color_discrete(
    name = "Seasons:",
    labels = c("Mar—May", "Jun—Aug", "Sep—Nov", "Dec—Feb")
  )
```

![](visualization_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature") +
  # Manually specify the colors per season
  scale_color_manual(values = c("darkblue", "green3", "pink", "gold"))
```

![](visualization_files/figure-gfm/unnamed-chunk-14-2.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature") +
  # Use a predefined color palette from RColorBrewer
  scale_colour_brewer(type = "qual", palette = 2)
```

![](visualization_files/figure-gfm/unnamed-chunk-14-3.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp, color = o3, shape = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature") +
  # Use a gradient color palette specifying the endpoints
  scale_color_gradient(low = "lightblue", high = "darkblue") +
  # Manually specify the shapes per season
  scale_shape_manual(values = c(15, 16, 17, 18))
```

![](visualization_files/figure-gfm/unnamed-chunk-14-4.png)<!-- -->

## Coordinate systems

Similarly to scales, you can adjust the default behavior of the x and y
axes as well (e.g. specify different axis limits, breaks, or labels),
either by the `scale_x_...()/scale_y_...()` arguments or by changing the
coordinate system with `coord_...()`.

`scale_x_...()/scale_y_...()` is most useful for changing the axis
breaks and labels, while `coord_...()` can e.g. flip the axes, adjust
the aspect ratio (see `coord_fixed()`), and determine whether
observations beyond the plot boundaries should be displayed.

Both `scale` and `coord` can adjust axis limits, with a subtle
difference in their behavior.

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  labs(x = "Year", y = "Temperature") +
  # Only plot observations with temp between 0-50 
  scale_y_continuous(limits = c(0, 50))
```

![](visualization_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  labs(x = "Year", y = "Temperature") +
  # Limit the y-axis between 0 and 50, but don't filter out points
  coord_cartesian(y = c(0, 50))
```

![](visualization_files/figure-gfm/unnamed-chunk-15-2.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  labs(x = "Year", y = "Temperature") +
  # Limit the y-axis between 0 and 50, and let points show beyond the plot panel up to the plot margins
  coord_cartesian(y = c(0, 50), clip = "off")
```

![](visualization_files/figure-gfm/unnamed-chunk-15-3.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  labs(x = "Year", y = "Temperature") +
  # Customize y-axis breaks to be 0, 10, 20, ... 90
  scale_y_continuous(breaks = seq(0, 90, 10), minor_breaks = NULL) +
  # Customize x-axis breaks to be every 6 months in the format of year-month
  scale_x_date(date_breaks = "6 months", date_labels = "%Y-%b", minor_breaks = NULL)
```

![](visualization_files/figure-gfm/unnamed-chunk-15-4.png)<!-- -->

``` r
ggplot(chic, aes(temp)) +
  geom_histogram(fill = "grey", color = "red") +
  # Flip the x and y axes
  coord_flip()
```

![](visualization_files/figure-gfm/unnamed-chunk-15-5.png)<!-- -->

``` r
ggplot(chic, aes(temp)) +
  geom_histogram(fill = "grey", color = "red") +
  # Reverse the x axis
  scale_x_reverse()
```

![](visualization_files/figure-gfm/unnamed-chunk-15-6.png)<!-- -->

## Multiple plots

Often you would like to present multiple plots side-by-side: maybe show
how the relationship between variables differs depending on some
groupings of observations (and putting all observations on a single plot
looks too cluttered), or compare how different outcome variables react
to changes in an explanatory variable. The two main ways to nicely
arrange plots are

- creating a single plot with multiple panels, known as facets;
- creating multiple independent plots, and arranging them into one
  figure afterwards.

### Using facets

By specifying a grouping variable along which to facet, you can create a
separate plot for each value of your facet variable. You can create
faceted plots by adding `facet_wrap()` to your `ggplot` object, and
specify the faceting variable with a tilde (`~`). You can also specify
additional arguments such as whether to allow the axis limits to vary
between panels. Only use variables with relatively few unique values as
your facet dimension, otherwise R will attempt to create far too many
plots, which takes a long time and might even crash your R session.

``` r
# Change the default theme to bw
theme_set(theme_bw())

ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "orangered", alpha = .3) +
  labs(x = "Year", y = "Temperature") +
  # Create separate plots per year
  facet_wrap(~year)
```

![](visualization_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "orangered", alpha = .3) +
  labs(x = "Year", y = "Temperature") +
  # Create separate plots per year and allow different x-axes per plot
  facet_wrap(~year, scales = "free_x")
```

![](visualization_files/figure-gfm/unnamed-chunk-16-2.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "orangered", alpha = .3) +
  labs(x = "Year", y = "Temperature") +
  # Create separate plots per season, arrange all plots in one row, let all scales vary
  facet_wrap(~season, nrow = 1, scales = "free")
```

![](visualization_files/figure-gfm/unnamed-chunk-16-3.png)<!-- -->

If you would like to group your data based on two variables, you can use
`facet_grid()`, separating your two variables with a tilde. However,
note that axis limit customization options are more limited with
`facet_grid()`, so if you need your axes to vary within columns/rows as
well, you can use `facet_wrap()` with the same two-variable argument as
well. However, with `facet_wrap()` your faceting variables are “stuck”
together, which makes the overview of which dimension corresponds to
changes in whcih faceting variable less clear.

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "orangered", alpha = .3) +
  labs(x = "Year", y = "Temperature") +
  # Arrange plots vertically per year and horizontally per season with facet_grid
  facet_grid(year~season)
```

![](visualization_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "orangered", alpha = .3) +
  labs(x = "Year", y = "Temperature") +
  # Same but with facet_wrap
  facet_wrap(year~season, scales = "free")
```

![](visualization_files/figure-gfm/unnamed-chunk-17-2.png)<!-- -->

### Combining independent plots with `patchwork`

In order to nicely arrange separate plots and same them as a single
file, you can use the `patchwork packages`. First you need to save each
of your plots into an R object (here `p1`, `p2`, `p3`), then use `+`
signs to combine the plots horizontally, and `/` signs to combine them
vertically. You can make these layouts as complex as you want to by
using parentheses to group rows. Alternatively, you can specify custom
layout options, including additional options such as whether to repeat
or collect legends, by adding a `plot_layout()` function to the plot
objects.

``` r
# Create and save plots of temp, ozone level and dewpoint over time

p1 <- ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature")

p2 <- ggplot(chic, aes(x = date, y = o3, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Ozone")

p3 <- ggplot(chic, aes(x = date, y = dewpoint, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Dewpoint")

# Combine temp and ozone horizontally
p1 + p2
```

![](visualization_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
# Combine temp and ozone vertically
p1 / p2
```

![](visualization_files/figure-gfm/unnamed-chunk-18-2.png)<!-- -->

``` r
# Combine temp and ozone vertically with plot_layout, do not repeat legends
p1 + p2 + plot_layout(ncol = 1, guides = "collect")
```

![](visualization_files/figure-gfm/unnamed-chunk-18-3.png)<!-- -->

``` r
# Arrange p1 and p2 horizontally on top, p1, p2 and p3 horizontally below
(p1 + p2) / (p1 + p2 + p3) + plot_layout(guides = "collect")
```

![](visualization_files/figure-gfm/unnamed-chunk-18-4.png)<!-- -->

Often it is possible to achieve your desired plot layout both by
faceting or with `patchwork`, but depending on your goal and the
structure of your data, one approach may be easier than the other. A
good rule to keep in mind is that faceting wants data in long format,
while `patchwork` often wants wide format: faceting needs a grouping
variable that for each observation defines which facet it should go on,
while in `patchwork` you can change the aesthetics between plots, so you
can easily switch which variables to use per plot.

The following example shows how we can create the same plots with
faceting as with `patchwork` by converting the data to long format where
the `name` variable specifies whether the value is the value of
temperature, ozone, or dewpoint.

``` r
# Side-by-side plots of temperature, ozone and dewpoint with facet_wrap
chic |> 
  # Convert data to long format with variable names to "name" and values to "value" 
  pivot_longer(c(temp, o3, dewpoint)) |> 
  ggplot(aes(date, value, color = season)) +
  geom_point() + 
  # Facet by variable name, arrange in one column
  facet_wrap(~name, ncol = 1)
```

![](visualization_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

## Customizing plot elements

So far we modified the default theme (aka `theme_grey()`) by specifying
alternative predefined themes (e.g. `theme_light()`). A way to customize
plot elements even further is to change the `theme()` function by
redefining particular plot elements.

Every design element of a plot (panel, grid, axes, text, legend keys,
etc.) can be changed with an `element_...()` function
(e.g. `element_text()` for text, `element_line()` for lines,
`element_blank()` if the element shouldn’t be displayed). Each element
type has different characteristics you can customize; a few examples are
shown here. The help file of the `theme()` functions describes the
options in detail.

``` r
ggplot(chic, aes(x = date, y = temp)) +
  geom_point(color = "firebrick") +
  labs(x = "Year", y = "Temperature (F)") +
  # Customize axis title fonts
  theme(axis.title = element_text(size = 15, color = "firebrick", face = "italic"),
        # Remove y-axis ticks
        axis.ticks.y = element_blank(),
        # Change minor grid to dashed lines
        panel.grid.minor = element_line(linetype = "dashed"))
```

![](visualization_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature", color = "Season") +
  # Move legend to above the plot
  theme(legend.position = "top",
        # Change legend background color
        legend.background = element_rect(fill = "grey90"),
        # Remove legend title
        legend.title = element_blank())
```

![](visualization_files/figure-gfm/unnamed-chunk-20-2.png)<!-- -->

``` r
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature") +
  # Move legend to coordinates within the plot
  theme(legend.position = c(0.85, 0.2),
        # Add whitespace to the left side of the plot
        plot.margin = margin(l = 50))
```

![](visualization_files/figure-gfm/unnamed-chunk-20-3.png)<!-- -->

In addition to the `theme()` function there are also other ways to
customize legends. You can change what key shape to use by specifying
the `key_glyph` within a geom (for the available options of shapes, see
[here](https://ggplot2.tidyverse.org/reference/draw_key.html)). You can
also add a `guides()` function to your `ggplot` object: some options
there overlap with options in `theme()`, but there are also additional
arguments such as arranging legend items in multiple rows/columns or
specifying the order of legends if there are multiple.

``` r
ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point(key_glyph = "vline") +
  labs(x = "Year", y = "Temperature") +
  # Customize the legend further with guide_legend()
  guides(color = guide_legend("Season:", title.hjust = 0.5, nrow = 2))
```

![](visualization_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

## Saving plots

You can save `ggplot` objects to use outside of the R environment with
the `ggsave` function. You can specify an existing `ggplot` object as
the first argument, but by default `ggsave()` saves the last plot
displayed in your Plots panel. You always need to specify the file path
of the saved plot, including the preferred file format (e.g. .png, .jpg,
.pdf). You can adjust the plot size with the `scale` argument or by
specifying the height and width in your preferred units (the default
units are inches).

``` r
ggplot(chic, aes(x = date, y = temp)) + 
  geom_point()

# Save last plot
ggsave("figures/plot1.png", scale = 1.5)

p <- ggplot(chic, aes(x = date, y = temp)) + 
  geom_point()

# Save plot saved to the Environment
ggsave(p, "figures/plot2.png", height = 10, width = 15, units = "cm")
```

# Other plotting packages and plot types

While `ggplot` is extremely flexible and therefore sufficient for most
of your plotting needs, it is good to be aware of how to use a few other
packages developed for more specific purposes.

## Correlation scatterplot matrix with `GGally`

To estimate the relationships between a set of (continuous) variables in
a dataset, you’d usually calculate a correlation matrix. The `ggpairs()`
function from the `GGally` packages presents both this correlation
matrix, and plots the distribution of each variable and the
relationships for each variable pair. You can adjust the default
function e.g. by specifying an additional grouping variable; for more
options, see the examples in [*Modern Data Visualization with
R*](https://rkabacoff.github.io/datavis/Other.html#scatterplot-matrix).

``` r
chic |> 
  # Keep only 4 continuous variables
  select(temp, o3, dewpoint, pm10) |> 
  # Create correlation matrix of the selected variables
  ggpairs()
```

![](visualization_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

``` r
chic |> 
  # Keep 4 continuous variables and season as the grouping variable
  select(temp, o3, dewpoint, pm10, season) |> 
  # Recreate the previous plot but group observations by season
  # Use only the first 4 columns for the plots (exclude season)
  ggpairs(columns = 1:4, ggplot2::aes(color = season))
```

![](visualization_files/figure-gfm/unnamed-chunk-23-2.png)<!-- -->

## Pie chart

While data scientists do not recommend using pie charts (because humans
are quite bad at comparing areas, and better at comparing lengths such
as on bar charts), you can nevertheless create pie charts in `ggplot`.
In order to do so, you need to create a bar chart, and change the
coordinate system to polar coordinates. To make the bars look nice, you
should specify your aesthetics and geoms similarly to the example below.

``` r
chic |> 
  # Get the number of observations per month
  count(month) |> 
  # Specify y as the counts and fill as the categorical variable
  ggplot(aes(x = "", y = n, fill = month)) +
  # Create bars with white borders
  geom_bar(stat = "identity", width = 1, color = "white") +
  # Change coordinate system to polar coordinates instead of Cartesian
  coord_polar("y", start = 0) +
  # Remove background theme elements
  theme_void()
```

![](visualization_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

## 3-D scatterplot

Similarly to pie charts, 3-D plots are also discouraged because they are
hard to interpret, so use them only if absolutely necessary. In that
case, the `scatterplot3d` package contains the `scatterplot3d()`
function where you need to specify the variables to put on each axis.
Note that this function does not have a separate `data` argument, so you
need to specify the variables by extracting the column from the
dataframe with the `$` operator.

``` r
# Create a 3-D scatterplot of temperature, dewpoint and ozone levels
scatterplot3d(x = chic$temp,
              y = chic$dewpoint, 
              z = chic$o3)
```

![](visualization_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

## Alluvial/Sankey diagrams with `ggalluvial` and `networkD3`

An interesting plot type is an alluvial diagram, also known as a Sankey
diagram. It shows flows between different categories, and is frequently
used e.g. to show changes over time in the share of observations
belonging to particular groups (e.g. what energy sources households use
in 1950 versus in 2000). For demonstration purposes, we’ll instead look
at how observations per different seasons are split between high and low
temperatures (e.g. expecting to see mostly low temperatures in Winter).

In order to create a static alluvial diagram, you can use the
`ggalluvial` package and standard `ggplot` workflows with `alluvium` and
`stratum` geoms.

``` r
chic |> 
  # Redefine temperature as a categorical variable: above mean temperature is high, below is low
  mutate(temp = ifelse(temp > mean(temp), "High temp", "Low temp")) |> 
  # Get number of observations per year, season, temp category
  count(year, season, temp) |> 
  # axis1, axis2, axis3 are the categorical grouping variables, y is the number of observations per group
  ggplot(aes(axis1 = year, axis2 = season, axis3 = temp, y = n)) +
  # Create flows, with colors per year
  geom_alluvium(aes(fill = factor(year))) +
  # Add rectangles for the categories of each variable
  geom_stratum() +
  # Label the rectangles
  geom_text(stat = "stratum", 
            aes(label = after_stat(stratum))) +
  # Remove background plot elements and legend
  theme_void() +
  theme(legend.position = "none")
```

![](visualization_files/figure-gfm/unnamed-chunk-26-1.png)<!-- -->

For an interactive version of the previous plot, you can use the
`networkD3` package, however, note that this package requires the input
data to have quite a specific format. You need a dataframe of the widths
(frequencies/shares) of each link, specifying a numeric identifier for
the source and target nodes. In addition, you need a dataframe of nodes
that matches the numeric IDs to node names. Once you have both of these
dataframes, you can use the `sankeyNetwork()` function. Note that
creating the input dataframes gets significantly more complex if the
diagram has more than two levels.

(Interactive plots do not display properly on the website.)

``` r
# Create a tibble of links with 3 variables: source, target, number of observations
links <- chic |> 
  # Redefine temp to hig/low categorical
  mutate(temp = ifelse(temp > mean(temp), "High temp", "Low temp")) |> 
  # Number of observations per season-temperature combination
  count(season, temp)

# Create a tibble of nodes by listing the unique categories in the links tibble
nodes <- tibble(name = unique(c(links$season, links$temp)))

# Add numerical identifiers of the nodes to the links tibble by using the row index in the nodes tibble 
# Subtract 1 to start the count at 0
links$IDseason <- match(links$season, nodes$name)-1
links$IDtemp <- match(links$temp, nodes$name)-1

# Create an interactive Sankey diagram from the links and nodes tibbles, the numerical category IDs, the observation counts per link and the variable name of the nodes tibble
sankeyNetwork(Links = links, Nodes = nodes, Source = "IDseason",
              Target = "IDtemp", Value = "n", NodeID = "name")
```

<div class="sankeyNetwork html-widget html-fill-item" id="htmlwidget-b551bbe5e631e5e9c305" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-b551bbe5e631e5e9c305">{"x":{"links":{"source":[null,null,null,null,null,null,null,null],"target":[4,5,4,5,4,5,4,5],"value":[26,335,274,90,367,1,103,265]},"nodes":{"name":["1","2","3","4","High temp","Low temp"],"group":["1","2","3","4","High temp","Low temp"]},"options":{"NodeID":"name","NodeGroup":"name","LinkGroup":null,"colourScale":"d3.scaleOrdinal(d3.schemeCategory20);","fontSize":7,"fontFamily":null,"nodeWidth":15,"nodePadding":10,"units":"","margin":{"top":null,"right":null,"bottom":null,"left":null},"iterations":32,"sinksRight":true}},"evals":[],"jsHooks":[]}</script>

## Interactive charts with `plotly`

If you work with interactive documents, you might want to make your
plots interactive as well, e.g. by highlighting the data points that a
user hovers over. It is very easy to turn `ggplot` objects into
interactive plots with the `plotly` package: just save the `ggplot` to
an object, and use that object as the argument of the `ggplotly()`
function.

(Interactive plots do not display properly on the website.)

``` r
# Define a simple plot and assign to an object
p <- ggplot(chic, aes(x = date, y = temp, color = season)) +
  geom_point() +
  labs(x = "Year", y = "Temperature")

# Display the plot as an interactive plot 
ggplotly(p)
```

<div class="plotly html-widget html-fill-item" id="htmlwidget-7c43dc92c89a96848809" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-7c43dc92c89a96848809">{"x":{"data":[{"x":[9862,9863,9864,9865,9866,9867,9868,9869,9870,9871,9872,9873,9874,9875,9876,9877,9878,9879,9880,9881,9882,9883,9884,9885,9886,9887,9888,9889,9890,9891,9892,9893,9894,9895,9896,9897,9898,9899,9900,9901,9902,9903,9904,9905,9906,9907,9908,9909,9910,9911,9912,9913,9914,9915,9916,9917,9918,9919,9920,9921,9922,9923,9924,9925,9926,9927,9928,9929,9930,9931,9932,9933,9934,9935,9936,9937,9938,9939,9940,9941,9942,9943,9944,9945,9946,9947,9948,9949,9950,9951,10227,10228,10229,10230,10231,10232,10233,10234,10235,10236,10237,10238,10239,10240,10241,10242,10243,10244,10245,10246,10247,10248,10249,10250,10251,10252,10253,10254,10255,10256,10257,10258,10259,10260,10261,10262,10263,10264,10265,10266,10267,10268,10269,10270,10271,10272,10273,10274,10275,10276,10277,10278,10279,10280,10281,10282,10283,10284,10285,10286,10287,10288,10289,10290,10291,10292,10293,10294,10295,10296,10297,10298,10299,10300,10301,10302,10303,10304,10305,10306,10307,10308,10309,10310,10311,10312,10313,10314,10315,10316,10592,10593,10594,10595,10596,10597,10598,10599,10600,10601,10602,10603,10604,10605,10606,10607,10608,10609,10610,10611,10612,10613,10614,10615,10616,10617,10618,10619,10620,10621,10622,10623,10624,10625,10626,10627,10628,10629,10630,10631,10632,10633,10634,10635,10636,10637,10638,10639,10640,10641,10642,10643,10644,10645,10646,10647,10648,10649,10650,10651,10652,10653,10654,10655,10656,10657,10658,10659,10660,10661,10662,10663,10664,10665,10666,10667,10668,10669,10670,10671,10672,10673,10674,10675,10676,10677,10678,10679,10680,10681,10957,10958,10959,10960,10961,10962,10963,10964,10965,10966,10967,10968,10969,10970,10971,10972,10973,10974,10975,10976,10977,10978,10979,10980,10981,10982,10983,10984,10985,10986,10987,10988,10989,10990,10991,10992,10993,10994,10995,10996,10997,10998,10999,11000,11001,11002,11003,11004,11005,11006,11007,11008,11009,11010,11011,11012,11013,11014,11015,11016,11017,11018,11019,11020,11021,11022,11023,11024,11025,11026,11027,11028,11029,11030,11031,11032,11033,11034,11035,11036,11037,11038,11039,11040,11041,11042,11043,11044,11045,11046,11047],"y":[36,45,40,51.5,27,17,16,19,26,16,1.5,1,3,10,19,9.5,-3,0,14,31,35,36.5,26,32,14.5,11,17,2,8,16.5,31.5,35,36.5,30,34.5,30,26,25.5,25.5,26,27,23.5,21,20.5,25.5,20,18.5,30,48.5,37.5,35.5,36,26,28,21.5,25.5,36.5,34.5,37.5,45.5,35,33.5,38,33,26.5,35.5,39,37,44,37,33.5,37.5,26.5,19,24.5,45,33.5,35.5,46,53.5,37.5,32.5,33,40.5,44,60.5,55.5,43.5,37.5,38.5,28,44,47,45,48,43,38,34,25,12,18,23,7,17,28,28,23,16,24,26,31,32,28,28,28,31,33,36,37,31,35,39,33,34,32,32,36,34,33,35,39,41,35,36,38,43,42,41,39,40,39,38,44,41,45,42,54,42,41,35,33,34,34,36,34,36,38,26,18,18,18,29,28,27,30,36,45,37,35,37,36,38,40,51,68,67,57,62,73,59,14,22,16,-2,1,12,1,16,4,6,5,20,20,18,23,30,28,29,27,30,37,42,39,33,28,29,42,33,33,34,36,40,38,41,31,31,40,33,37,44,44,49,23,23,34,45,39,27,29,28,25,28,26,29,27,33,36,43,37,38,37,31,30,35,25,21,24,29,29,28,27,28,32,31,43,50,38,36,39,38,36,38,35,32,35,41,43,48,55,60,42,48,35,28,20,34,25,37,39,42,33,31,25,20,34,31,22,25,21,10,5,17,16,6,17,15,20,21,24,28,20,25,21,33,25,21,23,26,22,41,35,24,24,30,26,31,28,27,30,29,27,35,49,59,49,55,56,45,43,54,46,33,35,46,52,51,66,67,46,30,30,34,39,45,50,33,32,34,39,49,47,51,50,57,53,52,47,39,40,40,45],"text":["date: 1997-01-01<br />temp: 36.0<br />season: Winter","date: 1997-01-02<br />temp: 45.0<br />season: Winter","date: 1997-01-03<br />temp: 40.0<br />season: Winter","date: 1997-01-04<br />temp: 51.5<br />season: Winter","date: 1997-01-05<br />temp: 27.0<br />season: Winter","date: 1997-01-06<br />temp: 17.0<br />season: Winter","date: 1997-01-07<br />temp: 16.0<br />season: Winter","date: 1997-01-08<br />temp: 19.0<br />season: Winter","date: 1997-01-09<br />temp: 26.0<br />season: Winter","date: 1997-01-10<br />temp: 16.0<br />season: Winter","date: 1997-01-11<br />temp:  1.5<br />season: Winter","date: 1997-01-12<br />temp:  1.0<br />season: Winter","date: 1997-01-13<br />temp:  3.0<br />season: Winter","date: 1997-01-14<br />temp: 10.0<br />season: Winter","date: 1997-01-15<br />temp: 19.0<br />season: Winter","date: 1997-01-16<br />temp:  9.5<br />season: Winter","date: 1997-01-17<br />temp: -3.0<br />season: Winter","date: 1997-01-18<br />temp:  0.0<br />season: Winter","date: 1997-01-19<br />temp: 14.0<br />season: Winter","date: 1997-01-20<br />temp: 31.0<br />season: Winter","date: 1997-01-21<br />temp: 35.0<br />season: Winter","date: 1997-01-22<br />temp: 36.5<br />season: Winter","date: 1997-01-23<br />temp: 26.0<br />season: Winter","date: 1997-01-24<br />temp: 32.0<br />season: Winter","date: 1997-01-25<br />temp: 14.5<br />season: Winter","date: 1997-01-26<br />temp: 11.0<br />season: Winter","date: 1997-01-27<br />temp: 17.0<br />season: Winter","date: 1997-01-28<br />temp:  2.0<br />season: Winter","date: 1997-01-29<br />temp:  8.0<br />season: Winter","date: 1997-01-30<br />temp: 16.5<br />season: Winter","date: 1997-01-31<br />temp: 31.5<br />season: Winter","date: 1997-02-01<br />temp: 35.0<br />season: Winter","date: 1997-02-02<br />temp: 36.5<br />season: Winter","date: 1997-02-03<br />temp: 30.0<br />season: Winter","date: 1997-02-04<br />temp: 34.5<br />season: Winter","date: 1997-02-05<br />temp: 30.0<br />season: Winter","date: 1997-02-06<br />temp: 26.0<br />season: Winter","date: 1997-02-07<br />temp: 25.5<br />season: Winter","date: 1997-02-08<br />temp: 25.5<br />season: Winter","date: 1997-02-09<br />temp: 26.0<br />season: Winter","date: 1997-02-10<br />temp: 27.0<br />season: Winter","date: 1997-02-11<br />temp: 23.5<br />season: Winter","date: 1997-02-12<br />temp: 21.0<br />season: Winter","date: 1997-02-13<br />temp: 20.5<br />season: Winter","date: 1997-02-14<br />temp: 25.5<br />season: Winter","date: 1997-02-15<br />temp: 20.0<br />season: Winter","date: 1997-02-16<br />temp: 18.5<br />season: Winter","date: 1997-02-17<br />temp: 30.0<br />season: Winter","date: 1997-02-18<br />temp: 48.5<br />season: Winter","date: 1997-02-19<br />temp: 37.5<br />season: Winter","date: 1997-02-20<br />temp: 35.5<br />season: Winter","date: 1997-02-21<br />temp: 36.0<br />season: Winter","date: 1997-02-22<br />temp: 26.0<br />season: Winter","date: 1997-02-23<br />temp: 28.0<br />season: Winter","date: 1997-02-24<br />temp: 21.5<br />season: Winter","date: 1997-02-25<br />temp: 25.5<br />season: Winter","date: 1997-02-26<br />temp: 36.5<br />season: Winter","date: 1997-02-27<br />temp: 34.5<br />season: Winter","date: 1997-02-28<br />temp: 37.5<br />season: Winter","date: 1997-03-01<br />temp: 45.5<br />season: Winter","date: 1997-03-02<br />temp: 35.0<br />season: Winter","date: 1997-03-03<br />temp: 33.5<br />season: Winter","date: 1997-03-04<br />temp: 38.0<br />season: Winter","date: 1997-03-05<br />temp: 33.0<br />season: Winter","date: 1997-03-06<br />temp: 26.5<br />season: Winter","date: 1997-03-07<br />temp: 35.5<br />season: Winter","date: 1997-03-08<br />temp: 39.0<br />season: Winter","date: 1997-03-09<br />temp: 37.0<br />season: Winter","date: 1997-03-10<br />temp: 44.0<br />season: Winter","date: 1997-03-11<br />temp: 37.0<br />season: Winter","date: 1997-03-12<br />temp: 33.5<br />season: Winter","date: 1997-03-13<br />temp: 37.5<br />season: Winter","date: 1997-03-14<br />temp: 26.5<br />season: Winter","date: 1997-03-15<br />temp: 19.0<br />season: Winter","date: 1997-03-16<br />temp: 24.5<br />season: Winter","date: 1997-03-17<br />temp: 45.0<br />season: Winter","date: 1997-03-18<br />temp: 33.5<br />season: Winter","date: 1997-03-19<br />temp: 35.5<br />season: Winter","date: 1997-03-20<br />temp: 46.0<br />season: Winter","date: 1997-03-21<br />temp: 53.5<br />season: Winter","date: 1997-03-22<br />temp: 37.5<br />season: Winter","date: 1997-03-23<br />temp: 32.5<br />season: Winter","date: 1997-03-24<br />temp: 33.0<br />season: Winter","date: 1997-03-25<br />temp: 40.5<br />season: Winter","date: 1997-03-26<br />temp: 44.0<br />season: Winter","date: 1997-03-27<br />temp: 60.5<br />season: Winter","date: 1997-03-28<br />temp: 55.5<br />season: Winter","date: 1997-03-29<br />temp: 43.5<br />season: Winter","date: 1997-03-30<br />temp: 37.5<br />season: Winter","date: 1997-03-31<br />temp: 38.5<br />season: Winter","date: 1998-01-01<br />temp: 28.0<br />season: Winter","date: 1998-01-02<br />temp: 44.0<br />season: Winter","date: 1998-01-03<br />temp: 47.0<br />season: Winter","date: 1998-01-04<br />temp: 45.0<br />season: Winter","date: 1998-01-05<br />temp: 48.0<br />season: Winter","date: 1998-01-06<br />temp: 43.0<br />season: Winter","date: 1998-01-07<br />temp: 38.0<br />season: Winter","date: 1998-01-08<br />temp: 34.0<br />season: Winter","date: 1998-01-09<br />temp: 25.0<br />season: Winter","date: 1998-01-10<br />temp: 12.0<br />season: Winter","date: 1998-01-11<br />temp: 18.0<br />season: Winter","date: 1998-01-12<br />temp: 23.0<br />season: Winter","date: 1998-01-13<br />temp:  7.0<br />season: Winter","date: 1998-01-14<br />temp: 17.0<br />season: Winter","date: 1998-01-15<br />temp: 28.0<br />season: Winter","date: 1998-01-16<br />temp: 28.0<br />season: Winter","date: 1998-01-17<br />temp: 23.0<br />season: Winter","date: 1998-01-18<br />temp: 16.0<br />season: Winter","date: 1998-01-19<br />temp: 24.0<br />season: Winter","date: 1998-01-20<br />temp: 26.0<br />season: Winter","date: 1998-01-21<br />temp: 31.0<br />season: Winter","date: 1998-01-22<br />temp: 32.0<br />season: Winter","date: 1998-01-23<br />temp: 28.0<br />season: Winter","date: 1998-01-24<br />temp: 28.0<br />season: Winter","date: 1998-01-25<br />temp: 28.0<br />season: Winter","date: 1998-01-26<br />temp: 31.0<br />season: Winter","date: 1998-01-27<br />temp: 33.0<br />season: Winter","date: 1998-01-28<br />temp: 36.0<br />season: Winter","date: 1998-01-29<br />temp: 37.0<br />season: Winter","date: 1998-01-30<br />temp: 31.0<br />season: Winter","date: 1998-01-31<br />temp: 35.0<br />season: Winter","date: 1998-02-01<br />temp: 39.0<br />season: Winter","date: 1998-02-02<br />temp: 33.0<br />season: Winter","date: 1998-02-03<br />temp: 34.0<br />season: Winter","date: 1998-02-04<br />temp: 32.0<br />season: Winter","date: 1998-02-05<br />temp: 32.0<br />season: Winter","date: 1998-02-06<br />temp: 36.0<br />season: Winter","date: 1998-02-07<br />temp: 34.0<br />season: Winter","date: 1998-02-08<br />temp: 33.0<br />season: Winter","date: 1998-02-09<br />temp: 35.0<br />season: Winter","date: 1998-02-10<br />temp: 39.0<br />season: Winter","date: 1998-02-11<br />temp: 41.0<br />season: Winter","date: 1998-02-12<br />temp: 35.0<br />season: Winter","date: 1998-02-13<br />temp: 36.0<br />season: Winter","date: 1998-02-14<br />temp: 38.0<br />season: Winter","date: 1998-02-15<br />temp: 43.0<br />season: Winter","date: 1998-02-16<br />temp: 42.0<br />season: Winter","date: 1998-02-17<br />temp: 41.0<br />season: Winter","date: 1998-02-18<br />temp: 39.0<br />season: Winter","date: 1998-02-19<br />temp: 40.0<br />season: Winter","date: 1998-02-20<br />temp: 39.0<br />season: Winter","date: 1998-02-21<br />temp: 38.0<br />season: Winter","date: 1998-02-22<br />temp: 44.0<br />season: Winter","date: 1998-02-23<br />temp: 41.0<br />season: Winter","date: 1998-02-24<br />temp: 45.0<br />season: Winter","date: 1998-02-25<br />temp: 42.0<br />season: Winter","date: 1998-02-26<br />temp: 54.0<br />season: Winter","date: 1998-02-27<br />temp: 42.0<br />season: Winter","date: 1998-02-28<br />temp: 41.0<br />season: Winter","date: 1998-03-01<br />temp: 35.0<br />season: Winter","date: 1998-03-02<br />temp: 33.0<br />season: Winter","date: 1998-03-03<br />temp: 34.0<br />season: Winter","date: 1998-03-04<br />temp: 34.0<br />season: Winter","date: 1998-03-05<br />temp: 36.0<br />season: Winter","date: 1998-03-06<br />temp: 34.0<br />season: Winter","date: 1998-03-07<br />temp: 36.0<br />season: Winter","date: 1998-03-08<br />temp: 38.0<br />season: Winter","date: 1998-03-09<br />temp: 26.0<br />season: Winter","date: 1998-03-10<br />temp: 18.0<br />season: Winter","date: 1998-03-11<br />temp: 18.0<br />season: Winter","date: 1998-03-12<br />temp: 18.0<br />season: Winter","date: 1998-03-13<br />temp: 29.0<br />season: Winter","date: 1998-03-14<br />temp: 28.0<br />season: Winter","date: 1998-03-15<br />temp: 27.0<br />season: Winter","date: 1998-03-16<br />temp: 30.0<br />season: Winter","date: 1998-03-17<br />temp: 36.0<br />season: Winter","date: 1998-03-18<br />temp: 45.0<br />season: Winter","date: 1998-03-19<br />temp: 37.0<br />season: Winter","date: 1998-03-20<br />temp: 35.0<br />season: Winter","date: 1998-03-21<br />temp: 37.0<br />season: Winter","date: 1998-03-22<br />temp: 36.0<br />season: Winter","date: 1998-03-23<br />temp: 38.0<br />season: Winter","date: 1998-03-24<br />temp: 40.0<br />season: Winter","date: 1998-03-25<br />temp: 51.0<br />season: Winter","date: 1998-03-26<br />temp: 68.0<br />season: Winter","date: 1998-03-27<br />temp: 67.0<br />season: Winter","date: 1998-03-28<br />temp: 57.0<br />season: Winter","date: 1998-03-29<br />temp: 62.0<br />season: Winter","date: 1998-03-30<br />temp: 73.0<br />season: Winter","date: 1998-03-31<br />temp: 59.0<br />season: Winter","date: 1999-01-01<br />temp: 14.0<br />season: Winter","date: 1999-01-02<br />temp: 22.0<br />season: Winter","date: 1999-01-03<br />temp: 16.0<br />season: Winter","date: 1999-01-04<br />temp: -2.0<br />season: Winter","date: 1999-01-05<br />temp:  1.0<br />season: Winter","date: 1999-01-06<br />temp: 12.0<br />season: Winter","date: 1999-01-07<br />temp:  1.0<br />season: Winter","date: 1999-01-08<br />temp: 16.0<br />season: Winter","date: 1999-01-09<br />temp:  4.0<br />season: Winter","date: 1999-01-10<br />temp:  6.0<br />season: Winter","date: 1999-01-11<br />temp:  5.0<br />season: Winter","date: 1999-01-12<br />temp: 20.0<br />season: Winter","date: 1999-01-13<br />temp: 20.0<br />season: Winter","date: 1999-01-14<br />temp: 18.0<br />season: Winter","date: 1999-01-15<br />temp: 23.0<br />season: Winter","date: 1999-01-16<br />temp: 30.0<br />season: Winter","date: 1999-01-17<br />temp: 28.0<br />season: Winter","date: 1999-01-18<br />temp: 29.0<br />season: Winter","date: 1999-01-19<br />temp: 27.0<br />season: Winter","date: 1999-01-20<br />temp: 30.0<br />season: Winter","date: 1999-01-21<br />temp: 37.0<br />season: Winter","date: 1999-01-22<br />temp: 42.0<br />season: Winter","date: 1999-01-23<br />temp: 39.0<br />season: Winter","date: 1999-01-24<br />temp: 33.0<br />season: Winter","date: 1999-01-25<br />temp: 28.0<br />season: Winter","date: 1999-01-26<br />temp: 29.0<br />season: Winter","date: 1999-01-27<br />temp: 42.0<br />season: Winter","date: 1999-01-28<br />temp: 33.0<br />season: Winter","date: 1999-01-29<br />temp: 33.0<br />season: Winter","date: 1999-01-30<br />temp: 34.0<br />season: Winter","date: 1999-01-31<br />temp: 36.0<br />season: Winter","date: 1999-02-01<br />temp: 40.0<br />season: Winter","date: 1999-02-02<br />temp: 38.0<br />season: Winter","date: 1999-02-03<br />temp: 41.0<br />season: Winter","date: 1999-02-04<br />temp: 31.0<br />season: Winter","date: 1999-02-05<br />temp: 31.0<br />season: Winter","date: 1999-02-06<br />temp: 40.0<br />season: Winter","date: 1999-02-07<br />temp: 33.0<br />season: Winter","date: 1999-02-08<br />temp: 37.0<br />season: Winter","date: 1999-02-09<br />temp: 44.0<br />season: Winter","date: 1999-02-10<br />temp: 44.0<br />season: Winter","date: 1999-02-11<br />temp: 49.0<br />season: Winter","date: 1999-02-12<br />temp: 23.0<br />season: Winter","date: 1999-02-13<br />temp: 23.0<br />season: Winter","date: 1999-02-14<br />temp: 34.0<br />season: Winter","date: 1999-02-15<br />temp: 45.0<br />season: Winter","date: 1999-02-16<br />temp: 39.0<br />season: Winter","date: 1999-02-17<br />temp: 27.0<br />season: Winter","date: 1999-02-18<br />temp: 29.0<br />season: Winter","date: 1999-02-19<br />temp: 28.0<br />season: Winter","date: 1999-02-20<br />temp: 25.0<br />season: Winter","date: 1999-02-21<br />temp: 28.0<br />season: Winter","date: 1999-02-22<br />temp: 26.0<br />season: Winter","date: 1999-02-23<br />temp: 29.0<br />season: Winter","date: 1999-02-24<br />temp: 27.0<br />season: Winter","date: 1999-02-25<br />temp: 33.0<br />season: Winter","date: 1999-02-26<br />temp: 36.0<br />season: Winter","date: 1999-02-27<br />temp: 43.0<br />season: Winter","date: 1999-02-28<br />temp: 37.0<br />season: Winter","date: 1999-03-01<br />temp: 38.0<br />season: Winter","date: 1999-03-02<br />temp: 37.0<br />season: Winter","date: 1999-03-03<br />temp: 31.0<br />season: Winter","date: 1999-03-04<br />temp: 30.0<br />season: Winter","date: 1999-03-05<br />temp: 35.0<br />season: Winter","date: 1999-03-06<br />temp: 25.0<br />season: Winter","date: 1999-03-07<br />temp: 21.0<br />season: Winter","date: 1999-03-08<br />temp: 24.0<br />season: Winter","date: 1999-03-09<br />temp: 29.0<br />season: Winter","date: 1999-03-10<br />temp: 29.0<br />season: Winter","date: 1999-03-11<br />temp: 28.0<br />season: Winter","date: 1999-03-12<br />temp: 27.0<br />season: Winter","date: 1999-03-13<br />temp: 28.0<br />season: Winter","date: 1999-03-14<br />temp: 32.0<br />season: Winter","date: 1999-03-15<br />temp: 31.0<br />season: Winter","date: 1999-03-16<br />temp: 43.0<br />season: Winter","date: 1999-03-17<br />temp: 50.0<br />season: Winter","date: 1999-03-18<br />temp: 38.0<br />season: Winter","date: 1999-03-19<br />temp: 36.0<br />season: Winter","date: 1999-03-20<br />temp: 39.0<br />season: Winter","date: 1999-03-21<br />temp: 38.0<br />season: Winter","date: 1999-03-22<br />temp: 36.0<br />season: Winter","date: 1999-03-23<br />temp: 38.0<br />season: Winter","date: 1999-03-24<br />temp: 35.0<br />season: Winter","date: 1999-03-25<br />temp: 32.0<br />season: Winter","date: 1999-03-26<br />temp: 35.0<br />season: Winter","date: 1999-03-27<br />temp: 41.0<br />season: Winter","date: 1999-03-28<br />temp: 43.0<br />season: Winter","date: 1999-03-29<br />temp: 48.0<br />season: Winter","date: 1999-03-30<br />temp: 55.0<br />season: Winter","date: 1999-03-31<br />temp: 60.0<br />season: Winter","date: 2000-01-01<br />temp: 42.0<br />season: Winter","date: 2000-01-02<br />temp: 48.0<br />season: Winter","date: 2000-01-03<br />temp: 35.0<br />season: Winter","date: 2000-01-04<br />temp: 28.0<br />season: Winter","date: 2000-01-05<br />temp: 20.0<br />season: Winter","date: 2000-01-06<br />temp: 34.0<br />season: Winter","date: 2000-01-07<br />temp: 25.0<br />season: Winter","date: 2000-01-08<br />temp: 37.0<br />season: Winter","date: 2000-01-09<br />temp: 39.0<br />season: Winter","date: 2000-01-10<br />temp: 42.0<br />season: Winter","date: 2000-01-11<br />temp: 33.0<br />season: Winter","date: 2000-01-12<br />temp: 31.0<br />season: Winter","date: 2000-01-13<br />temp: 25.0<br />season: Winter","date: 2000-01-14<br />temp: 20.0<br />season: Winter","date: 2000-01-15<br />temp: 34.0<br />season: Winter","date: 2000-01-16<br />temp: 31.0<br />season: Winter","date: 2000-01-17<br />temp: 22.0<br />season: Winter","date: 2000-01-18<br />temp: 25.0<br />season: Winter","date: 2000-01-19<br />temp: 21.0<br />season: Winter","date: 2000-01-20<br />temp: 10.0<br />season: Winter","date: 2000-01-21<br />temp:  5.0<br />season: Winter","date: 2000-01-22<br />temp: 17.0<br />season: Winter","date: 2000-01-23<br />temp: 16.0<br />season: Winter","date: 2000-01-24<br />temp:  6.0<br />season: Winter","date: 2000-01-25<br />temp: 17.0<br />season: Winter","date: 2000-01-26<br />temp: 15.0<br />season: Winter","date: 2000-01-27<br />temp: 20.0<br />season: Winter","date: 2000-01-28<br />temp: 21.0<br />season: Winter","date: 2000-01-29<br />temp: 24.0<br />season: Winter","date: 2000-01-30<br />temp: 28.0<br />season: Winter","date: 2000-01-31<br />temp: 20.0<br />season: Winter","date: 2000-02-01<br />temp: 25.0<br />season: Winter","date: 2000-02-02<br />temp: 21.0<br />season: Winter","date: 2000-02-03<br />temp: 33.0<br />season: Winter","date: 2000-02-04<br />temp: 25.0<br />season: Winter","date: 2000-02-05<br />temp: 21.0<br />season: Winter","date: 2000-02-06<br />temp: 23.0<br />season: Winter","date: 2000-02-07<br />temp: 26.0<br />season: Winter","date: 2000-02-08<br />temp: 22.0<br />season: Winter","date: 2000-02-09<br />temp: 41.0<br />season: Winter","date: 2000-02-10<br />temp: 35.0<br />season: Winter","date: 2000-02-11<br />temp: 24.0<br />season: Winter","date: 2000-02-12<br />temp: 24.0<br />season: Winter","date: 2000-02-13<br />temp: 30.0<br />season: Winter","date: 2000-02-14<br />temp: 26.0<br />season: Winter","date: 2000-02-15<br />temp: 31.0<br />season: Winter","date: 2000-02-16<br />temp: 28.0<br />season: Winter","date: 2000-02-17<br />temp: 27.0<br />season: Winter","date: 2000-02-18<br />temp: 30.0<br />season: Winter","date: 2000-02-19<br />temp: 29.0<br />season: Winter","date: 2000-02-20<br />temp: 27.0<br />season: Winter","date: 2000-02-21<br />temp: 35.0<br />season: Winter","date: 2000-02-22<br />temp: 49.0<br />season: Winter","date: 2000-02-23<br />temp: 59.0<br />season: Winter","date: 2000-02-24<br />temp: 49.0<br />season: Winter","date: 2000-02-25<br />temp: 55.0<br />season: Winter","date: 2000-02-26<br />temp: 56.0<br />season: Winter","date: 2000-02-27<br />temp: 45.0<br />season: Winter","date: 2000-02-28<br />temp: 43.0<br />season: Winter","date: 2000-02-29<br />temp: 54.0<br />season: Winter","date: 2000-03-01<br />temp: 46.0<br />season: Winter","date: 2000-03-02<br />temp: 33.0<br />season: Winter","date: 2000-03-03<br />temp: 35.0<br />season: Winter","date: 2000-03-04<br />temp: 46.0<br />season: Winter","date: 2000-03-05<br />temp: 52.0<br />season: Winter","date: 2000-03-06<br />temp: 51.0<br />season: Winter","date: 2000-03-07<br />temp: 66.0<br />season: Winter","date: 2000-03-08<br />temp: 67.0<br />season: Winter","date: 2000-03-09<br />temp: 46.0<br />season: Winter","date: 2000-03-10<br />temp: 30.0<br />season: Winter","date: 2000-03-11<br />temp: 30.0<br />season: Winter","date: 2000-03-12<br />temp: 34.0<br />season: Winter","date: 2000-03-13<br />temp: 39.0<br />season: Winter","date: 2000-03-14<br />temp: 45.0<br />season: Winter","date: 2000-03-15<br />temp: 50.0<br />season: Winter","date: 2000-03-16<br />temp: 33.0<br />season: Winter","date: 2000-03-17<br />temp: 32.0<br />season: Winter","date: 2000-03-18<br />temp: 34.0<br />season: Winter","date: 2000-03-19<br />temp: 39.0<br />season: Winter","date: 2000-03-20<br />temp: 49.0<br />season: Winter","date: 2000-03-21<br />temp: 47.0<br />season: Winter","date: 2000-03-22<br />temp: 51.0<br />season: Winter","date: 2000-03-23<br />temp: 50.0<br />season: Winter","date: 2000-03-24<br />temp: 57.0<br />season: Winter","date: 2000-03-25<br />temp: 53.0<br />season: Winter","date: 2000-03-26<br />temp: 52.0<br />season: Winter","date: 2000-03-27<br />temp: 47.0<br />season: Winter","date: 2000-03-28<br />temp: 39.0<br />season: Winter","date: 2000-03-29<br />temp: 40.0<br />season: Winter","date: 2000-03-30<br />temp: 40.0<br />season: Winter","date: 2000-03-31<br />temp: 45.0<br />season: Winter"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(248,118,109,1)"}},"hoveron":"points","name":"Winter","legendgroup":"Winter","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[9952,9953,9954,9955,9956,9957,9958,9959,9960,9961,9962,9963,9964,9965,9966,9967,9968,9969,9970,9971,9972,9973,9974,9975,9976,9977,9978,9979,9980,9981,9982,9983,9984,9985,9986,9987,9988,9989,9990,9991,9992,9993,9994,9995,9996,9997,9998,9999,10000,10001,10002,10003,10004,10005,10006,10007,10008,10009,10010,10011,10012,10013,10014,10015,10016,10017,10018,10019,10020,10021,10022,10023,10024,10025,10026,10027,10028,10029,10030,10031,10032,10033,10034,10035,10036,10037,10038,10039,10040,10041,10042,10317,10318,10319,10320,10321,10322,10323,10324,10325,10326,10327,10328,10329,10330,10331,10332,10333,10334,10335,10336,10337,10338,10339,10340,10341,10342,10343,10344,10345,10346,10347,10348,10349,10350,10351,10352,10353,10354,10355,10356,10357,10358,10359,10360,10361,10362,10363,10364,10365,10366,10367,10368,10369,10370,10371,10372,10373,10374,10375,10376,10377,10378,10379,10380,10381,10382,10383,10384,10385,10386,10387,10388,10389,10390,10391,10392,10393,10394,10395,10396,10397,10398,10399,10400,10401,10402,10403,10404,10405,10406,10407,10682,10683,10684,10685,10686,10687,10688,10689,10690,10691,10692,10693,10694,10695,10696,10697,10698,10699,10700,10701,10702,10703,10704,10705,10706,10707,10708,10709,10710,10711,10712,10713,10714,10715,10716,10717,10718,10719,10720,10721,10722,10723,10724,10725,10726,10727,10728,10729,10730,10731,10732,10733,10734,10735,10736,10737,10738,10739,10740,10741,10742,10743,10744,10745,10746,10747,10748,10749,10750,10751,10752,10753,10754,10755,10756,10757,10758,10759,10760,10761,10762,10763,10764,10765,10766,10767,10768,10769,10770,10771,10772,11048,11049,11050,11051,11052,11053,11054,11055,11056,11057,11058,11059,11060,11061,11062,11063,11064,11065,11066,11067,11068,11069,11070,11071,11072,11073,11074,11075,11076,11077,11078,11079,11080,11081,11082,11083,11084,11085,11086,11087,11088,11089,11090,11091,11092,11093,11094,11095,11096,11097,11098,11099,11100,11101,11102,11103,11104,11105,11106,11107,11108,11109,11110,11111,11112,11113,11114,11115,11116,11117,11118,11119,11120,11121,11122,11123,11124,11125,11126,11127,11128,11129,11130,11131,11132,11133,11134,11135,11136,11137,11138],"y":[44.5,53,59.5,62.5,60.5,45,34,28.5,30,30.5,33.5,33.5,38.5,41.5,49,43,40.5,40,45.5,49,45,43,48.5,47.5,48,49.5,47.5,50.5,60,55,48.5,48.5,49,51.5,62,55.5,52.5,61,50.5,52.5,59.5,52.5,45.5,49.5,45,51,50.5,59.5,54,51.5,48,50.5,58,68.5,50,50,56,58.5,58,61,59,60.5,58,61.5,59,62,63.5,58.5,61,62.5,66.5,67.5,70,71,56.5,63,67,60.5,67,70,81.5,75.5,77,82.5,84,78,73.5,72.5,74,75,76,44,44,41,41,41,48,52,48,43,46,50,64,62,58,53,42,45,50,52,52,50,53,56,62,56,48,45,46,54,55,59,57,53,60,62,68,57,62,58,58,61,63,68,68,75,72,68,76,79,65,64,57,61,61,62,63,67,75,74,73,70,63,63,55,56,50,54,58,56,61,68,68,74,69,67,70,70,73,77,73,78,79,74,74,83,83,79,82,78,77,74,58,64,63,56,50,53,55,55,45,47,46,45,50,49,49,44,44,42,42,45,54,48,43,44,48,51,50,51,52,51,56,58,64,67,70,58,56,53,51,58,67,62,51,53,60,69,69,61,61,64,66,59,60,51,56,60,64,75,76,70,73,65,64,58,64,75,80,81,80,79,79,78,75,65,66,58,61,57,59,68,67,72,74,76,76,75,76,78,77,66,68,52,51,52,38,44,53,41,34,45,37,34,39,49,58,56,44,41,48,52,53,44,48,47,52,49,52,53,54,48,54,57,57,63,72,74,74,71,76,59,55,67,73,53,52,57,61,61,58,46,55,57,67,69,68,64,61,60,55,56,65,67,73,60,55,56,54,60,64,78,77,80,70,57,69,72,70,67,60,65,66,74,74,72,71,71,75,72,68,62,66,66],"text":["date: 1997-04-01<br />temp: 44.5<br />season: Spring","date: 1997-04-02<br />temp: 53.0<br />season: Spring","date: 1997-04-03<br />temp: 59.5<br />season: Spring","date: 1997-04-04<br />temp: 62.5<br />season: Spring","date: 1997-04-05<br />temp: 60.5<br />season: Spring","date: 1997-04-06<br />temp: 45.0<br />season: Spring","date: 1997-04-07<br />temp: 34.0<br />season: Spring","date: 1997-04-08<br />temp: 28.5<br />season: Spring","date: 1997-04-09<br />temp: 30.0<br />season: Spring","date: 1997-04-10<br />temp: 30.5<br />season: Spring","date: 1997-04-11<br />temp: 33.5<br />season: Spring","date: 1997-04-12<br />temp: 33.5<br />season: Spring","date: 1997-04-13<br />temp: 38.5<br />season: Spring","date: 1997-04-14<br />temp: 41.5<br />season: Spring","date: 1997-04-15<br />temp: 49.0<br />season: Spring","date: 1997-04-16<br />temp: 43.0<br />season: Spring","date: 1997-04-17<br />temp: 40.5<br />season: Spring","date: 1997-04-18<br />temp: 40.0<br />season: Spring","date: 1997-04-19<br />temp: 45.5<br />season: Spring","date: 1997-04-20<br />temp: 49.0<br />season: Spring","date: 1997-04-21<br />temp: 45.0<br />season: Spring","date: 1997-04-22<br />temp: 43.0<br />season: Spring","date: 1997-04-23<br />temp: 48.5<br />season: Spring","date: 1997-04-24<br />temp: 47.5<br />season: Spring","date: 1997-04-25<br />temp: 48.0<br />season: Spring","date: 1997-04-26<br />temp: 49.5<br />season: Spring","date: 1997-04-27<br />temp: 47.5<br />season: Spring","date: 1997-04-28<br />temp: 50.5<br />season: Spring","date: 1997-04-29<br />temp: 60.0<br />season: Spring","date: 1997-04-30<br />temp: 55.0<br />season: Spring","date: 1997-05-01<br />temp: 48.5<br />season: Spring","date: 1997-05-02<br />temp: 48.5<br />season: Spring","date: 1997-05-03<br />temp: 49.0<br />season: Spring","date: 1997-05-04<br />temp: 51.5<br />season: Spring","date: 1997-05-05<br />temp: 62.0<br />season: Spring","date: 1997-05-06<br />temp: 55.5<br />season: Spring","date: 1997-05-07<br />temp: 52.5<br />season: Spring","date: 1997-05-08<br />temp: 61.0<br />season: Spring","date: 1997-05-09<br />temp: 50.5<br />season: Spring","date: 1997-05-10<br />temp: 52.5<br />season: Spring","date: 1997-05-11<br />temp: 59.5<br />season: Spring","date: 1997-05-12<br />temp: 52.5<br />season: Spring","date: 1997-05-13<br />temp: 45.5<br />season: Spring","date: 1997-05-14<br />temp: 49.5<br />season: Spring","date: 1997-05-15<br />temp: 45.0<br />season: Spring","date: 1997-05-16<br />temp: 51.0<br />season: Spring","date: 1997-05-17<br />temp: 50.5<br />season: Spring","date: 1997-05-18<br />temp: 59.5<br />season: Spring","date: 1997-05-19<br />temp: 54.0<br />season: Spring","date: 1997-05-20<br />temp: 51.5<br />season: Spring","date: 1997-05-21<br />temp: 48.0<br />season: Spring","date: 1997-05-22<br />temp: 50.5<br />season: Spring","date: 1997-05-23<br />temp: 58.0<br />season: Spring","date: 1997-05-24<br />temp: 68.5<br />season: Spring","date: 1997-05-25<br />temp: 50.0<br />season: Spring","date: 1997-05-26<br />temp: 50.0<br />season: Spring","date: 1997-05-27<br />temp: 56.0<br />season: Spring","date: 1997-05-28<br />temp: 58.5<br />season: Spring","date: 1997-05-29<br />temp: 58.0<br />season: Spring","date: 1997-05-30<br />temp: 61.0<br />season: Spring","date: 1997-05-31<br />temp: 59.0<br />season: Spring","date: 1997-06-01<br />temp: 60.5<br />season: Spring","date: 1997-06-02<br />temp: 58.0<br />season: Spring","date: 1997-06-03<br />temp: 61.5<br />season: Spring","date: 1997-06-04<br />temp: 59.0<br />season: Spring","date: 1997-06-05<br />temp: 62.0<br />season: Spring","date: 1997-06-06<br />temp: 63.5<br />season: Spring","date: 1997-06-07<br />temp: 58.5<br />season: Spring","date: 1997-06-08<br />temp: 61.0<br />season: Spring","date: 1997-06-09<br />temp: 62.5<br />season: Spring","date: 1997-06-10<br />temp: 66.5<br />season: Spring","date: 1997-06-11<br />temp: 67.5<br />season: Spring","date: 1997-06-12<br />temp: 70.0<br />season: Spring","date: 1997-06-13<br />temp: 71.0<br />season: Spring","date: 1997-06-14<br />temp: 56.5<br />season: Spring","date: 1997-06-15<br />temp: 63.0<br />season: Spring","date: 1997-06-16<br />temp: 67.0<br />season: Spring","date: 1997-06-17<br />temp: 60.5<br />season: Spring","date: 1997-06-18<br />temp: 67.0<br />season: Spring","date: 1997-06-19<br />temp: 70.0<br />season: Spring","date: 1997-06-20<br />temp: 81.5<br />season: Spring","date: 1997-06-21<br />temp: 75.5<br />season: Spring","date: 1997-06-22<br />temp: 77.0<br />season: Spring","date: 1997-06-23<br />temp: 82.5<br />season: Spring","date: 1997-06-24<br />temp: 84.0<br />season: Spring","date: 1997-06-25<br />temp: 78.0<br />season: Spring","date: 1997-06-26<br />temp: 73.5<br />season: Spring","date: 1997-06-27<br />temp: 72.5<br />season: Spring","date: 1997-06-28<br />temp: 74.0<br />season: Spring","date: 1997-06-29<br />temp: 75.0<br />season: Spring","date: 1997-06-30<br />temp: 76.0<br />season: Spring","date: 1998-04-01<br />temp: 44.0<br />season: Spring","date: 1998-04-02<br />temp: 44.0<br />season: Spring","date: 1998-04-03<br />temp: 41.0<br />season: Spring","date: 1998-04-04<br />temp: 41.0<br />season: Spring","date: 1998-04-05<br />temp: 41.0<br />season: Spring","date: 1998-04-06<br />temp: 48.0<br />season: Spring","date: 1998-04-07<br />temp: 52.0<br />season: Spring","date: 1998-04-08<br />temp: 48.0<br />season: Spring","date: 1998-04-09<br />temp: 43.0<br />season: Spring","date: 1998-04-10<br />temp: 46.0<br />season: Spring","date: 1998-04-11<br />temp: 50.0<br />season: Spring","date: 1998-04-12<br />temp: 64.0<br />season: Spring","date: 1998-04-13<br />temp: 62.0<br />season: Spring","date: 1998-04-14<br />temp: 58.0<br />season: Spring","date: 1998-04-15<br />temp: 53.0<br />season: Spring","date: 1998-04-16<br />temp: 42.0<br />season: Spring","date: 1998-04-17<br />temp: 45.0<br />season: Spring","date: 1998-04-18<br />temp: 50.0<br />season: Spring","date: 1998-04-19<br />temp: 52.0<br />season: Spring","date: 1998-04-20<br />temp: 52.0<br />season: Spring","date: 1998-04-21<br />temp: 50.0<br />season: Spring","date: 1998-04-22<br />temp: 53.0<br />season: Spring","date: 1998-04-23<br />temp: 56.0<br />season: Spring","date: 1998-04-24<br />temp: 62.0<br />season: Spring","date: 1998-04-25<br />temp: 56.0<br />season: Spring","date: 1998-04-26<br />temp: 48.0<br />season: Spring","date: 1998-04-27<br />temp: 45.0<br />season: Spring","date: 1998-04-28<br />temp: 46.0<br />season: Spring","date: 1998-04-29<br />temp: 54.0<br />season: Spring","date: 1998-04-30<br />temp: 55.0<br />season: Spring","date: 1998-05-01<br />temp: 59.0<br />season: Spring","date: 1998-05-02<br />temp: 57.0<br />season: Spring","date: 1998-05-03<br />temp: 53.0<br />season: Spring","date: 1998-05-04<br />temp: 60.0<br />season: Spring","date: 1998-05-05<br />temp: 62.0<br />season: Spring","date: 1998-05-06<br />temp: 68.0<br />season: Spring","date: 1998-05-07<br />temp: 57.0<br />season: Spring","date: 1998-05-08<br />temp: 62.0<br />season: Spring","date: 1998-05-09<br />temp: 58.0<br />season: Spring","date: 1998-05-10<br />temp: 58.0<br />season: Spring","date: 1998-05-11<br />temp: 61.0<br />season: Spring","date: 1998-05-12<br />temp: 63.0<br />season: Spring","date: 1998-05-13<br />temp: 68.0<br />season: Spring","date: 1998-05-14<br />temp: 68.0<br />season: Spring","date: 1998-05-15<br />temp: 75.0<br />season: Spring","date: 1998-05-16<br />temp: 72.0<br />season: Spring","date: 1998-05-17<br />temp: 68.0<br />season: Spring","date: 1998-05-18<br />temp: 76.0<br />season: Spring","date: 1998-05-19<br />temp: 79.0<br />season: Spring","date: 1998-05-20<br />temp: 65.0<br />season: Spring","date: 1998-05-21<br />temp: 64.0<br />season: Spring","date: 1998-05-22<br />temp: 57.0<br />season: Spring","date: 1998-05-23<br />temp: 61.0<br />season: Spring","date: 1998-05-24<br />temp: 61.0<br />season: Spring","date: 1998-05-25<br />temp: 62.0<br />season: Spring","date: 1998-05-26<br />temp: 63.0<br />season: Spring","date: 1998-05-27<br />temp: 67.0<br />season: Spring","date: 1998-05-28<br />temp: 75.0<br />season: Spring","date: 1998-05-29<br />temp: 74.0<br />season: Spring","date: 1998-05-30<br />temp: 73.0<br />season: Spring","date: 1998-05-31<br />temp: 70.0<br />season: Spring","date: 1998-06-01<br />temp: 63.0<br />season: Spring","date: 1998-06-02<br />temp: 63.0<br />season: Spring","date: 1998-06-03<br />temp: 55.0<br />season: Spring","date: 1998-06-04<br />temp: 56.0<br />season: Spring","date: 1998-06-05<br />temp: 50.0<br />season: Spring","date: 1998-06-06<br />temp: 54.0<br />season: Spring","date: 1998-06-07<br />temp: 58.0<br />season: Spring","date: 1998-06-08<br />temp: 56.0<br />season: Spring","date: 1998-06-09<br />temp: 61.0<br />season: Spring","date: 1998-06-10<br />temp: 68.0<br />season: Spring","date: 1998-06-11<br />temp: 68.0<br />season: Spring","date: 1998-06-12<br />temp: 74.0<br />season: Spring","date: 1998-06-13<br />temp: 69.0<br />season: Spring","date: 1998-06-14<br />temp: 67.0<br />season: Spring","date: 1998-06-15<br />temp: 70.0<br />season: Spring","date: 1998-06-16<br />temp: 70.0<br />season: Spring","date: 1998-06-17<br />temp: 73.0<br />season: Spring","date: 1998-06-18<br />temp: 77.0<br />season: Spring","date: 1998-06-19<br />temp: 73.0<br />season: Spring","date: 1998-06-20<br />temp: 78.0<br />season: Spring","date: 1998-06-21<br />temp: 79.0<br />season: Spring","date: 1998-06-22<br />temp: 74.0<br />season: Spring","date: 1998-06-23<br />temp: 74.0<br />season: Spring","date: 1998-06-24<br />temp: 83.0<br />season: Spring","date: 1998-06-25<br />temp: 83.0<br />season: Spring","date: 1998-06-26<br />temp: 79.0<br />season: Spring","date: 1998-06-27<br />temp: 82.0<br />season: Spring","date: 1998-06-28<br />temp: 78.0<br />season: Spring","date: 1998-06-29<br />temp: 77.0<br />season: Spring","date: 1998-06-30<br />temp: 74.0<br />season: Spring","date: 1999-04-01<br />temp: 58.0<br />season: Spring","date: 1999-04-02<br />temp: 64.0<br />season: Spring","date: 1999-04-03<br />temp: 63.0<br />season: Spring","date: 1999-04-04<br />temp: 56.0<br />season: Spring","date: 1999-04-05<br />temp: 50.0<br />season: Spring","date: 1999-04-06<br />temp: 53.0<br />season: Spring","date: 1999-04-07<br />temp: 55.0<br />season: Spring","date: 1999-04-08<br />temp: 55.0<br />season: Spring","date: 1999-04-09<br />temp: 45.0<br />season: Spring","date: 1999-04-10<br />temp: 47.0<br />season: Spring","date: 1999-04-11<br />temp: 46.0<br />season: Spring","date: 1999-04-12<br />temp: 45.0<br />season: Spring","date: 1999-04-13<br />temp: 50.0<br />season: Spring","date: 1999-04-14<br />temp: 49.0<br />season: Spring","date: 1999-04-15<br />temp: 49.0<br />season: Spring","date: 1999-04-16<br />temp: 44.0<br />season: Spring","date: 1999-04-17<br />temp: 44.0<br />season: Spring","date: 1999-04-18<br />temp: 42.0<br />season: Spring","date: 1999-04-19<br />temp: 42.0<br />season: Spring","date: 1999-04-20<br />temp: 45.0<br />season: Spring","date: 1999-04-21<br />temp: 54.0<br />season: Spring","date: 1999-04-22<br />temp: 48.0<br />season: Spring","date: 1999-04-23<br />temp: 43.0<br />season: Spring","date: 1999-04-24<br />temp: 44.0<br />season: Spring","date: 1999-04-25<br />temp: 48.0<br />season: Spring","date: 1999-04-26<br />temp: 51.0<br />season: Spring","date: 1999-04-27<br />temp: 50.0<br />season: Spring","date: 1999-04-28<br />temp: 51.0<br />season: Spring","date: 1999-04-29<br />temp: 52.0<br />season: Spring","date: 1999-04-30<br />temp: 51.0<br />season: Spring","date: 1999-05-01<br />temp: 56.0<br />season: Spring","date: 1999-05-02<br />temp: 58.0<br />season: Spring","date: 1999-05-03<br />temp: 64.0<br />season: Spring","date: 1999-05-04<br />temp: 67.0<br />season: Spring","date: 1999-05-05<br />temp: 70.0<br />season: Spring","date: 1999-05-06<br />temp: 58.0<br />season: Spring","date: 1999-05-07<br />temp: 56.0<br />season: Spring","date: 1999-05-08<br />temp: 53.0<br />season: Spring","date: 1999-05-09<br />temp: 51.0<br />season: Spring","date: 1999-05-10<br />temp: 58.0<br />season: Spring","date: 1999-05-11<br />temp: 67.0<br />season: Spring","date: 1999-05-12<br />temp: 62.0<br />season: Spring","date: 1999-05-13<br />temp: 51.0<br />season: Spring","date: 1999-05-14<br />temp: 53.0<br />season: Spring","date: 1999-05-15<br />temp: 60.0<br />season: Spring","date: 1999-05-16<br />temp: 69.0<br />season: Spring","date: 1999-05-17<br />temp: 69.0<br />season: Spring","date: 1999-05-18<br />temp: 61.0<br />season: Spring","date: 1999-05-19<br />temp: 61.0<br />season: Spring","date: 1999-05-20<br />temp: 64.0<br />season: Spring","date: 1999-05-21<br />temp: 66.0<br />season: Spring","date: 1999-05-22<br />temp: 59.0<br />season: Spring","date: 1999-05-23<br />temp: 60.0<br />season: Spring","date: 1999-05-24<br />temp: 51.0<br />season: Spring","date: 1999-05-25<br />temp: 56.0<br />season: Spring","date: 1999-05-26<br />temp: 60.0<br />season: Spring","date: 1999-05-27<br />temp: 64.0<br />season: Spring","date: 1999-05-28<br />temp: 75.0<br />season: Spring","date: 1999-05-29<br />temp: 76.0<br />season: Spring","date: 1999-05-30<br />temp: 70.0<br />season: Spring","date: 1999-05-31<br />temp: 73.0<br />season: Spring","date: 1999-06-01<br />temp: 65.0<br />season: Spring","date: 1999-06-02<br />temp: 64.0<br />season: Spring","date: 1999-06-03<br />temp: 58.0<br />season: Spring","date: 1999-06-04<br />temp: 64.0<br />season: Spring","date: 1999-06-05<br />temp: 75.0<br />season: Spring","date: 1999-06-06<br />temp: 80.0<br />season: Spring","date: 1999-06-07<br />temp: 81.0<br />season: Spring","date: 1999-06-08<br />temp: 80.0<br />season: Spring","date: 1999-06-09<br />temp: 79.0<br />season: Spring","date: 1999-06-10<br />temp: 79.0<br />season: Spring","date: 1999-06-11<br />temp: 78.0<br />season: Spring","date: 1999-06-12<br />temp: 75.0<br />season: Spring","date: 1999-06-13<br />temp: 65.0<br />season: Spring","date: 1999-06-14<br />temp: 66.0<br />season: Spring","date: 1999-06-15<br />temp: 58.0<br />season: Spring","date: 1999-06-16<br />temp: 61.0<br />season: Spring","date: 1999-06-17<br />temp: 57.0<br />season: Spring","date: 1999-06-18<br />temp: 59.0<br />season: Spring","date: 1999-06-19<br />temp: 68.0<br />season: Spring","date: 1999-06-20<br />temp: 67.0<br />season: Spring","date: 1999-06-21<br />temp: 72.0<br />season: Spring","date: 1999-06-22<br />temp: 74.0<br />season: Spring","date: 1999-06-23<br />temp: 76.0<br />season: Spring","date: 1999-06-24<br />temp: 76.0<br />season: Spring","date: 1999-06-25<br />temp: 75.0<br />season: Spring","date: 1999-06-26<br />temp: 76.0<br />season: Spring","date: 1999-06-27<br />temp: 78.0<br />season: Spring","date: 1999-06-28<br />temp: 77.0<br />season: Spring","date: 1999-06-29<br />temp: 66.0<br />season: Spring","date: 1999-06-30<br />temp: 68.0<br />season: Spring","date: 2000-04-01<br />temp: 52.0<br />season: Spring","date: 2000-04-02<br />temp: 51.0<br />season: Spring","date: 2000-04-03<br />temp: 52.0<br />season: Spring","date: 2000-04-04<br />temp: 38.0<br />season: Spring","date: 2000-04-05<br />temp: 44.0<br />season: Spring","date: 2000-04-06<br />temp: 53.0<br />season: Spring","date: 2000-04-07<br />temp: 41.0<br />season: Spring","date: 2000-04-08<br />temp: 34.0<br />season: Spring","date: 2000-04-09<br />temp: 45.0<br />season: Spring","date: 2000-04-10<br />temp: 37.0<br />season: Spring","date: 2000-04-11<br />temp: 34.0<br />season: Spring","date: 2000-04-12<br />temp: 39.0<br />season: Spring","date: 2000-04-13<br />temp: 49.0<br />season: Spring","date: 2000-04-14<br />temp: 58.0<br />season: Spring","date: 2000-04-15<br />temp: 56.0<br />season: Spring","date: 2000-04-16<br />temp: 44.0<br />season: Spring","date: 2000-04-17<br />temp: 41.0<br />season: Spring","date: 2000-04-18<br />temp: 48.0<br />season: Spring","date: 2000-04-19<br />temp: 52.0<br />season: Spring","date: 2000-04-20<br />temp: 53.0<br />season: Spring","date: 2000-04-21<br />temp: 44.0<br />season: Spring","date: 2000-04-22<br />temp: 48.0<br />season: Spring","date: 2000-04-23<br />temp: 47.0<br />season: Spring","date: 2000-04-24<br />temp: 52.0<br />season: Spring","date: 2000-04-25<br />temp: 49.0<br />season: Spring","date: 2000-04-26<br />temp: 52.0<br />season: Spring","date: 2000-04-27<br />temp: 53.0<br />season: Spring","date: 2000-04-28<br />temp: 54.0<br />season: Spring","date: 2000-04-29<br />temp: 48.0<br />season: Spring","date: 2000-04-30<br />temp: 54.0<br />season: Spring","date: 2000-05-01<br />temp: 57.0<br />season: Spring","date: 2000-05-02<br />temp: 57.0<br />season: Spring","date: 2000-05-03<br />temp: 63.0<br />season: Spring","date: 2000-05-04<br />temp: 72.0<br />season: Spring","date: 2000-05-05<br />temp: 74.0<br />season: Spring","date: 2000-05-06<br />temp: 74.0<br />season: Spring","date: 2000-05-07<br />temp: 71.0<br />season: Spring","date: 2000-05-08<br />temp: 76.0<br />season: Spring","date: 2000-05-09<br />temp: 59.0<br />season: Spring","date: 2000-05-10<br />temp: 55.0<br />season: Spring","date: 2000-05-11<br />temp: 67.0<br />season: Spring","date: 2000-05-12<br />temp: 73.0<br />season: Spring","date: 2000-05-13<br />temp: 53.0<br />season: Spring","date: 2000-05-14<br />temp: 52.0<br />season: Spring","date: 2000-05-15<br />temp: 57.0<br />season: Spring","date: 2000-05-16<br />temp: 61.0<br />season: Spring","date: 2000-05-17<br />temp: 61.0<br />season: Spring","date: 2000-05-18<br />temp: 58.0<br />season: Spring","date: 2000-05-19<br />temp: 46.0<br />season: Spring","date: 2000-05-20<br />temp: 55.0<br />season: Spring","date: 2000-05-21<br />temp: 57.0<br />season: Spring","date: 2000-05-22<br />temp: 67.0<br />season: Spring","date: 2000-05-23<br />temp: 69.0<br />season: Spring","date: 2000-05-24<br />temp: 68.0<br />season: Spring","date: 2000-05-25<br />temp: 64.0<br />season: Spring","date: 2000-05-26<br />temp: 61.0<br />season: Spring","date: 2000-05-27<br />temp: 60.0<br />season: Spring","date: 2000-05-28<br />temp: 55.0<br />season: Spring","date: 2000-05-29<br />temp: 56.0<br />season: Spring","date: 2000-05-30<br />temp: 65.0<br />season: Spring","date: 2000-05-31<br />temp: 67.0<br />season: Spring","date: 2000-06-01<br />temp: 73.0<br />season: Spring","date: 2000-06-02<br />temp: 60.0<br />season: Spring","date: 2000-06-03<br />temp: 55.0<br />season: Spring","date: 2000-06-04<br />temp: 56.0<br />season: Spring","date: 2000-06-05<br />temp: 54.0<br />season: Spring","date: 2000-06-06<br />temp: 60.0<br />season: Spring","date: 2000-06-07<br />temp: 64.0<br />season: Spring","date: 2000-06-08<br />temp: 78.0<br />season: Spring","date: 2000-06-09<br />temp: 77.0<br />season: Spring","date: 2000-06-10<br />temp: 80.0<br />season: Spring","date: 2000-06-11<br />temp: 70.0<br />season: Spring","date: 2000-06-12<br />temp: 57.0<br />season: Spring","date: 2000-06-13<br />temp: 69.0<br />season: Spring","date: 2000-06-14<br />temp: 72.0<br />season: Spring","date: 2000-06-15<br />temp: 70.0<br />season: Spring","date: 2000-06-16<br />temp: 67.0<br />season: Spring","date: 2000-06-17<br />temp: 60.0<br />season: Spring","date: 2000-06-18<br />temp: 65.0<br />season: Spring","date: 2000-06-19<br />temp: 66.0<br />season: Spring","date: 2000-06-20<br />temp: 74.0<br />season: Spring","date: 2000-06-21<br />temp: 74.0<br />season: Spring","date: 2000-06-22<br />temp: 72.0<br />season: Spring","date: 2000-06-23<br />temp: 71.0<br />season: Spring","date: 2000-06-24<br />temp: 71.0<br />season: Spring","date: 2000-06-25<br />temp: 75.0<br />season: Spring","date: 2000-06-26<br />temp: 72.0<br />season: Spring","date: 2000-06-27<br />temp: 68.0<br />season: Spring","date: 2000-06-28<br />temp: 62.0<br />season: Spring","date: 2000-06-29<br />temp: 66.0<br />season: Spring","date: 2000-06-30<br />temp: 66.0<br />season: Spring"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(124,174,0,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(124,174,0,1)"}},"hoveron":"points","name":"Spring","legendgroup":"Spring","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[10043,10044,10045,10046,10047,10048,10049,10050,10051,10052,10053,10054,10055,10056,10057,10058,10059,10060,10061,10062,10063,10064,10065,10066,10067,10068,10069,10070,10071,10072,10073,10074,10075,10076,10077,10078,10079,10080,10081,10082,10083,10084,10085,10086,10087,10088,10089,10090,10091,10092,10093,10094,10095,10096,10097,10098,10099,10100,10101,10102,10103,10104,10105,10106,10107,10108,10109,10110,10111,10112,10113,10114,10115,10116,10117,10118,10119,10120,10121,10122,10123,10124,10125,10126,10127,10128,10129,10130,10131,10132,10133,10134,10408,10409,10410,10411,10412,10413,10414,10415,10416,10417,10418,10419,10420,10421,10422,10423,10424,10425,10426,10427,10428,10429,10430,10431,10432,10433,10434,10435,10436,10437,10438,10439,10440,10441,10442,10443,10444,10445,10446,10447,10448,10449,10450,10451,10452,10453,10454,10455,10456,10457,10458,10459,10460,10461,10462,10463,10464,10465,10466,10467,10468,10469,10470,10471,10472,10473,10474,10475,10476,10477,10478,10479,10480,10481,10482,10483,10484,10485,10486,10487,10488,10489,10490,10491,10492,10493,10494,10495,10496,10497,10498,10499,10773,10774,10775,10776,10777,10778,10779,10780,10781,10782,10783,10784,10785,10786,10787,10788,10789,10790,10791,10792,10793,10794,10795,10796,10797,10798,10799,10800,10801,10802,10803,10804,10805,10806,10807,10808,10809,10810,10811,10812,10813,10814,10815,10816,10817,10818,10819,10820,10821,10822,10823,10824,10825,10826,10827,10828,10829,10830,10831,10832,10833,10834,10835,10836,10837,10838,10839,10840,10841,10842,10843,10844,10845,10846,10847,10848,10849,10850,10851,10852,10853,10854,10855,10856,10857,10858,10859,10860,10861,10862,10863,10864,11139,11140,11141,11142,11143,11144,11145,11146,11147,11148,11149,11150,11151,11152,11153,11154,11155,11156,11157,11158,11159,11160,11161,11162,11163,11164,11165,11166,11167,11168,11169,11170,11171,11172,11173,11174,11175,11176,11177,11178,11179,11180,11181,11182,11183,11184,11185,11186,11187,11188,11189,11190,11191,11192,11193,11194,11195,11196,11197,11198,11199,11200,11201,11202,11203,11204,11205,11206,11207,11208,11209,11210,11211,11212,11213,11214,11215,11216,11217,11218,11219,11220,11221,11222,11223,11224,11225,11226,11227,11228,11229,11230],"y":[77.5,79.5,68.5,61.5,65.5,70,65.5,71,62,64.5,68.5,74.5,82.5,83,79,82.5,84,79,74.5,76.5,72,69,69,72.5,76,85.5,81.5,74.5,66.5,68.5,69,74.5,81.5,78,71,62.5,64.5,67,69.5,70.5,75.5,67.5,69.5,68.5,65.5,77,80,68.5,63.5,64,67,63.5,64,65.5,70.5,65.5,69,77,68.5,67,65,72,70.5,68,58.5,59,60.5,74.5,66,67.5,68.5,64.5,61.5,61.5,63.5,69,69.5,74.5,70,69,73,58.5,57,57.5,59,56.5,66,60,62.5,64,65,59.5,70,73,79,69,70,78,72,74,75,73,71,71,73,78,79,80,75,77,81,79,85,76,73,72,70,70,74,79,77,72,71,72,72,78,70,76,78,75,77,77,76,71,70,71,71,71,72,75,68,69,73,80,78,81,76,76,73,72,69,76,70,69,69,65,68,69,70,81,66,63,63,63,71,74,74,72,70,72,68,68,72,76,63,58,56,59,66,79,73,63,63,64,74,77,84,86,87,79,75,76,76,64,67,68,70,74,78,82,75,77,79,76,83,82,83,82,85,80,79,82,83,90,83,74,71,71,76,73,76,72,66,66,78,75,70,72,64,66,73,75,67,68,67,65,67,72,71,70,72,74,80,69,62,65,70,74,76,79,77,66,65,71,62,62,64,68,59,59,55,59,59,62,64,58,51,57,66,57,62,72,66,58,56,56,71,74,68,70,76,74,70,73,81,73,69,72,75,77,73,73,77,66,63,67,67,63,63,66,69,73,77,74,73,71,70,73,76,63,67,67,78,76,77,79,74,71,71,74,76,82,70,68,67,64,65,67,76,76,71,70,73,72,75,77,77,80,80,79,77,66,63,61,74,75,76,78,75,63,63,65,56,56,68,69,75,58,54,58,63,53,49,56,59,55,61,65],"text":["date: 1997-07-01<br />temp: 77.5<br />season: Summer","date: 1997-07-02<br />temp: 79.5<br />season: Summer","date: 1997-07-03<br />temp: 68.5<br />season: Summer","date: 1997-07-04<br />temp: 61.5<br />season: Summer","date: 1997-07-05<br />temp: 65.5<br />season: Summer","date: 1997-07-06<br />temp: 70.0<br />season: Summer","date: 1997-07-07<br />temp: 65.5<br />season: Summer","date: 1997-07-08<br />temp: 71.0<br />season: Summer","date: 1997-07-09<br />temp: 62.0<br />season: Summer","date: 1997-07-10<br />temp: 64.5<br />season: Summer","date: 1997-07-11<br />temp: 68.5<br />season: Summer","date: 1997-07-12<br />temp: 74.5<br />season: Summer","date: 1997-07-13<br />temp: 82.5<br />season: Summer","date: 1997-07-14<br />temp: 83.0<br />season: Summer","date: 1997-07-15<br />temp: 79.0<br />season: Summer","date: 1997-07-16<br />temp: 82.5<br />season: Summer","date: 1997-07-17<br />temp: 84.0<br />season: Summer","date: 1997-07-18<br />temp: 79.0<br />season: Summer","date: 1997-07-19<br />temp: 74.5<br />season: Summer","date: 1997-07-20<br />temp: 76.5<br />season: Summer","date: 1997-07-21<br />temp: 72.0<br />season: Summer","date: 1997-07-22<br />temp: 69.0<br />season: Summer","date: 1997-07-23<br />temp: 69.0<br />season: Summer","date: 1997-07-24<br />temp: 72.5<br />season: Summer","date: 1997-07-25<br />temp: 76.0<br />season: Summer","date: 1997-07-26<br />temp: 85.5<br />season: Summer","date: 1997-07-27<br />temp: 81.5<br />season: Summer","date: 1997-07-28<br />temp: 74.5<br />season: Summer","date: 1997-07-29<br />temp: 66.5<br />season: Summer","date: 1997-07-30<br />temp: 68.5<br />season: Summer","date: 1997-07-31<br />temp: 69.0<br />season: Summer","date: 1997-08-01<br />temp: 74.5<br />season: Summer","date: 1997-08-02<br />temp: 81.5<br />season: Summer","date: 1997-08-03<br />temp: 78.0<br />season: Summer","date: 1997-08-04<br />temp: 71.0<br />season: Summer","date: 1997-08-05<br />temp: 62.5<br />season: Summer","date: 1997-08-06<br />temp: 64.5<br />season: Summer","date: 1997-08-07<br />temp: 67.0<br />season: Summer","date: 1997-08-08<br />temp: 69.5<br />season: Summer","date: 1997-08-09<br />temp: 70.5<br />season: Summer","date: 1997-08-10<br />temp: 75.5<br />season: Summer","date: 1997-08-11<br />temp: 67.5<br />season: Summer","date: 1997-08-12<br />temp: 69.5<br />season: Summer","date: 1997-08-13<br />temp: 68.5<br />season: Summer","date: 1997-08-14<br />temp: 65.5<br />season: Summer","date: 1997-08-15<br />temp: 77.0<br />season: Summer","date: 1997-08-16<br />temp: 80.0<br />season: Summer","date: 1997-08-17<br />temp: 68.5<br />season: Summer","date: 1997-08-18<br />temp: 63.5<br />season: Summer","date: 1997-08-19<br />temp: 64.0<br />season: Summer","date: 1997-08-20<br />temp: 67.0<br />season: Summer","date: 1997-08-21<br />temp: 63.5<br />season: Summer","date: 1997-08-22<br />temp: 64.0<br />season: Summer","date: 1997-08-23<br />temp: 65.5<br />season: Summer","date: 1997-08-24<br />temp: 70.5<br />season: Summer","date: 1997-08-25<br />temp: 65.5<br />season: Summer","date: 1997-08-26<br />temp: 69.0<br />season: Summer","date: 1997-08-27<br />temp: 77.0<br />season: Summer","date: 1997-08-28<br />temp: 68.5<br />season: Summer","date: 1997-08-29<br />temp: 67.0<br />season: Summer","date: 1997-08-30<br />temp: 65.0<br />season: Summer","date: 1997-08-31<br />temp: 72.0<br />season: Summer","date: 1997-09-01<br />temp: 70.5<br />season: Summer","date: 1997-09-02<br />temp: 68.0<br />season: Summer","date: 1997-09-03<br />temp: 58.5<br />season: Summer","date: 1997-09-04<br />temp: 59.0<br />season: Summer","date: 1997-09-05<br />temp: 60.5<br />season: Summer","date: 1997-09-06<br />temp: 74.5<br />season: Summer","date: 1997-09-07<br />temp: 66.0<br />season: Summer","date: 1997-09-08<br />temp: 67.5<br />season: Summer","date: 1997-09-09<br />temp: 68.5<br />season: Summer","date: 1997-09-10<br />temp: 64.5<br />season: Summer","date: 1997-09-11<br />temp: 61.5<br />season: Summer","date: 1997-09-12<br />temp: 61.5<br />season: Summer","date: 1997-09-13<br />temp: 63.5<br />season: Summer","date: 1997-09-14<br />temp: 69.0<br />season: Summer","date: 1997-09-15<br />temp: 69.5<br />season: Summer","date: 1997-09-16<br />temp: 74.5<br />season: Summer","date: 1997-09-17<br />temp: 70.0<br />season: Summer","date: 1997-09-18<br />temp: 69.0<br />season: Summer","date: 1997-09-19<br />temp: 73.0<br />season: Summer","date: 1997-09-20<br />temp: 58.5<br />season: Summer","date: 1997-09-21<br />temp: 57.0<br />season: Summer","date: 1997-09-22<br />temp: 57.5<br />season: Summer","date: 1997-09-23<br />temp: 59.0<br />season: Summer","date: 1997-09-24<br />temp: 56.5<br />season: Summer","date: 1997-09-25<br />temp: 66.0<br />season: Summer","date: 1997-09-26<br />temp: 60.0<br />season: Summer","date: 1997-09-27<br />temp: 62.5<br />season: Summer","date: 1997-09-28<br />temp: 64.0<br />season: Summer","date: 1997-09-29<br />temp: 65.0<br />season: Summer","date: 1997-09-30<br />temp: 59.5<br />season: Summer","date: 1998-07-01<br />temp: 70.0<br />season: Summer","date: 1998-07-02<br />temp: 73.0<br />season: Summer","date: 1998-07-03<br />temp: 79.0<br />season: Summer","date: 1998-07-04<br />temp: 69.0<br />season: Summer","date: 1998-07-05<br />temp: 70.0<br />season: Summer","date: 1998-07-06<br />temp: 78.0<br />season: Summer","date: 1998-07-07<br />temp: 72.0<br />season: Summer","date: 1998-07-08<br />temp: 74.0<br />season: Summer","date: 1998-07-09<br />temp: 75.0<br />season: Summer","date: 1998-07-10<br />temp: 73.0<br />season: Summer","date: 1998-07-11<br />temp: 71.0<br />season: Summer","date: 1998-07-12<br />temp: 71.0<br />season: Summer","date: 1998-07-13<br />temp: 73.0<br />season: Summer","date: 1998-07-14<br />temp: 78.0<br />season: Summer","date: 1998-07-15<br />temp: 79.0<br />season: Summer","date: 1998-07-16<br />temp: 80.0<br />season: Summer","date: 1998-07-17<br />temp: 75.0<br />season: Summer","date: 1998-07-18<br />temp: 77.0<br />season: Summer","date: 1998-07-19<br />temp: 81.0<br />season: Summer","date: 1998-07-20<br />temp: 79.0<br />season: Summer","date: 1998-07-21<br />temp: 85.0<br />season: Summer","date: 1998-07-22<br />temp: 76.0<br />season: Summer","date: 1998-07-23<br />temp: 73.0<br />season: Summer","date: 1998-07-24<br />temp: 72.0<br />season: Summer","date: 1998-07-25<br />temp: 70.0<br />season: Summer","date: 1998-07-26<br />temp: 70.0<br />season: Summer","date: 1998-07-27<br />temp: 74.0<br />season: Summer","date: 1998-07-28<br />temp: 79.0<br />season: Summer","date: 1998-07-29<br />temp: 77.0<br />season: Summer","date: 1998-07-30<br />temp: 72.0<br />season: Summer","date: 1998-07-31<br />temp: 71.0<br />season: Summer","date: 1998-08-01<br />temp: 72.0<br />season: Summer","date: 1998-08-02<br />temp: 72.0<br />season: Summer","date: 1998-08-03<br />temp: 78.0<br />season: Summer","date: 1998-08-04<br />temp: 70.0<br />season: Summer","date: 1998-08-05<br />temp: 76.0<br />season: Summer","date: 1998-08-06<br />temp: 78.0<br />season: Summer","date: 1998-08-07<br />temp: 75.0<br />season: Summer","date: 1998-08-08<br />temp: 77.0<br />season: Summer","date: 1998-08-09<br />temp: 77.0<br />season: Summer","date: 1998-08-10<br />temp: 76.0<br />season: Summer","date: 1998-08-11<br />temp: 71.0<br />season: Summer","date: 1998-08-12<br />temp: 70.0<br />season: Summer","date: 1998-08-13<br />temp: 71.0<br />season: Summer","date: 1998-08-14<br />temp: 71.0<br />season: Summer","date: 1998-08-15<br />temp: 71.0<br />season: Summer","date: 1998-08-16<br />temp: 72.0<br />season: Summer","date: 1998-08-17<br />temp: 75.0<br />season: Summer","date: 1998-08-18<br />temp: 68.0<br />season: Summer","date: 1998-08-19<br />temp: 69.0<br />season: Summer","date: 1998-08-20<br />temp: 73.0<br />season: Summer","date: 1998-08-21<br />temp: 80.0<br />season: Summer","date: 1998-08-22<br />temp: 78.0<br />season: Summer","date: 1998-08-23<br />temp: 81.0<br />season: Summer","date: 1998-08-24<br />temp: 76.0<br />season: Summer","date: 1998-08-25<br />temp: 76.0<br />season: Summer","date: 1998-08-26<br />temp: 73.0<br />season: Summer","date: 1998-08-27<br />temp: 72.0<br />season: Summer","date: 1998-08-28<br />temp: 69.0<br />season: Summer","date: 1998-08-29<br />temp: 76.0<br />season: Summer","date: 1998-08-30<br />temp: 70.0<br />season: Summer","date: 1998-08-31<br />temp: 69.0<br />season: Summer","date: 1998-09-01<br />temp: 69.0<br />season: Summer","date: 1998-09-02<br />temp: 65.0<br />season: Summer","date: 1998-09-03<br />temp: 68.0<br />season: Summer","date: 1998-09-04<br />temp: 69.0<br />season: Summer","date: 1998-09-05<br />temp: 70.0<br />season: Summer","date: 1998-09-06<br />temp: 81.0<br />season: Summer","date: 1998-09-07<br />temp: 66.0<br />season: Summer","date: 1998-09-08<br />temp: 63.0<br />season: Summer","date: 1998-09-09<br />temp: 63.0<br />season: Summer","date: 1998-09-10<br />temp: 63.0<br />season: Summer","date: 1998-09-11<br />temp: 71.0<br />season: Summer","date: 1998-09-12<br />temp: 74.0<br />season: Summer","date: 1998-09-13<br />temp: 74.0<br />season: Summer","date: 1998-09-14<br />temp: 72.0<br />season: Summer","date: 1998-09-15<br />temp: 70.0<br />season: Summer","date: 1998-09-16<br />temp: 72.0<br />season: Summer","date: 1998-09-17<br />temp: 68.0<br />season: Summer","date: 1998-09-18<br />temp: 68.0<br />season: Summer","date: 1998-09-19<br />temp: 72.0<br />season: Summer","date: 1998-09-20<br />temp: 76.0<br />season: Summer","date: 1998-09-21<br />temp: 63.0<br />season: Summer","date: 1998-09-22<br />temp: 58.0<br />season: Summer","date: 1998-09-23<br />temp: 56.0<br />season: Summer","date: 1998-09-24<br />temp: 59.0<br />season: Summer","date: 1998-09-25<br />temp: 66.0<br />season: Summer","date: 1998-09-26<br />temp: 79.0<br />season: Summer","date: 1998-09-27<br />temp: 73.0<br />season: Summer","date: 1998-09-28<br />temp: 63.0<br />season: Summer","date: 1998-09-29<br />temp: 63.0<br />season: Summer","date: 1998-09-30<br />temp: 64.0<br />season: Summer","date: 1999-07-01<br />temp: 74.0<br />season: Summer","date: 1999-07-02<br />temp: 77.0<br />season: Summer","date: 1999-07-03<br />temp: 84.0<br />season: Summer","date: 1999-07-04<br />temp: 86.0<br />season: Summer","date: 1999-07-05<br />temp: 87.0<br />season: Summer","date: 1999-07-06<br />temp: 79.0<br />season: Summer","date: 1999-07-07<br />temp: 75.0<br />season: Summer","date: 1999-07-08<br />temp: 76.0<br />season: Summer","date: 1999-07-09<br />temp: 76.0<br />season: Summer","date: 1999-07-10<br />temp: 64.0<br />season: Summer","date: 1999-07-11<br />temp: 67.0<br />season: Summer","date: 1999-07-12<br />temp: 68.0<br />season: Summer","date: 1999-07-13<br />temp: 70.0<br />season: Summer","date: 1999-07-14<br />temp: 74.0<br />season: Summer","date: 1999-07-15<br />temp: 78.0<br />season: Summer","date: 1999-07-16<br />temp: 82.0<br />season: Summer","date: 1999-07-17<br />temp: 75.0<br />season: Summer","date: 1999-07-18<br />temp: 77.0<br />season: Summer","date: 1999-07-19<br />temp: 79.0<br />season: Summer","date: 1999-07-20<br />temp: 76.0<br />season: Summer","date: 1999-07-21<br />temp: 83.0<br />season: Summer","date: 1999-07-22<br />temp: 82.0<br />season: Summer","date: 1999-07-23<br />temp: 83.0<br />season: Summer","date: 1999-07-24<br />temp: 82.0<br />season: Summer","date: 1999-07-25<br />temp: 85.0<br />season: Summer","date: 1999-07-26<br />temp: 80.0<br />season: Summer","date: 1999-07-27<br />temp: 79.0<br />season: Summer","date: 1999-07-28<br />temp: 82.0<br />season: Summer","date: 1999-07-29<br />temp: 83.0<br />season: Summer","date: 1999-07-30<br />temp: 90.0<br />season: Summer","date: 1999-07-31<br />temp: 83.0<br />season: Summer","date: 1999-08-01<br />temp: 74.0<br />season: Summer","date: 1999-08-02<br />temp: 71.0<br />season: Summer","date: 1999-08-03<br />temp: 71.0<br />season: Summer","date: 1999-08-04<br />temp: 76.0<br />season: Summer","date: 1999-08-05<br />temp: 73.0<br />season: Summer","date: 1999-08-06<br />temp: 76.0<br />season: Summer","date: 1999-08-07<br />temp: 72.0<br />season: Summer","date: 1999-08-08<br />temp: 66.0<br />season: Summer","date: 1999-08-09<br />temp: 66.0<br />season: Summer","date: 1999-08-10<br />temp: 78.0<br />season: Summer","date: 1999-08-11<br />temp: 75.0<br />season: Summer","date: 1999-08-12<br />temp: 70.0<br />season: Summer","date: 1999-08-13<br />temp: 72.0<br />season: Summer","date: 1999-08-14<br />temp: 64.0<br />season: Summer","date: 1999-08-15<br />temp: 66.0<br />season: Summer","date: 1999-08-16<br />temp: 73.0<br />season: Summer","date: 1999-08-17<br />temp: 75.0<br />season: Summer","date: 1999-08-18<br />temp: 67.0<br />season: Summer","date: 1999-08-19<br />temp: 68.0<br />season: Summer","date: 1999-08-20<br />temp: 67.0<br />season: Summer","date: 1999-08-21<br />temp: 65.0<br />season: Summer","date: 1999-08-22<br />temp: 67.0<br />season: Summer","date: 1999-08-23<br />temp: 72.0<br />season: Summer","date: 1999-08-24<br />temp: 71.0<br />season: Summer","date: 1999-08-25<br />temp: 70.0<br />season: Summer","date: 1999-08-26<br />temp: 72.0<br />season: Summer","date: 1999-08-27<br />temp: 74.0<br />season: Summer","date: 1999-08-28<br />temp: 80.0<br />season: Summer","date: 1999-08-29<br />temp: 69.0<br />season: Summer","date: 1999-08-30<br />temp: 62.0<br />season: Summer","date: 1999-08-31<br />temp: 65.0<br />season: Summer","date: 1999-09-01<br />temp: 70.0<br />season: Summer","date: 1999-09-02<br />temp: 74.0<br />season: Summer","date: 1999-09-03<br />temp: 76.0<br />season: Summer","date: 1999-09-04<br />temp: 79.0<br />season: Summer","date: 1999-09-05<br />temp: 77.0<br />season: Summer","date: 1999-09-06<br />temp: 66.0<br />season: Summer","date: 1999-09-07<br />temp: 65.0<br />season: Summer","date: 1999-09-08<br />temp: 71.0<br />season: Summer","date: 1999-09-09<br />temp: 62.0<br />season: Summer","date: 1999-09-10<br />temp: 62.0<br />season: Summer","date: 1999-09-11<br />temp: 64.0<br />season: Summer","date: 1999-09-12<br />temp: 68.0<br />season: Summer","date: 1999-09-13<br />temp: 59.0<br />season: Summer","date: 1999-09-14<br />temp: 59.0<br />season: Summer","date: 1999-09-15<br />temp: 55.0<br />season: Summer","date: 1999-09-16<br />temp: 59.0<br />season: Summer","date: 1999-09-17<br />temp: 59.0<br />season: Summer","date: 1999-09-18<br />temp: 62.0<br />season: Summer","date: 1999-09-19<br />temp: 64.0<br />season: Summer","date: 1999-09-20<br />temp: 58.0<br />season: Summer","date: 1999-09-21<br />temp: 51.0<br />season: Summer","date: 1999-09-22<br />temp: 57.0<br />season: Summer","date: 1999-09-23<br />temp: 66.0<br />season: Summer","date: 1999-09-24<br />temp: 57.0<br />season: Summer","date: 1999-09-25<br />temp: 62.0<br />season: Summer","date: 1999-09-26<br />temp: 72.0<br />season: Summer","date: 1999-09-27<br />temp: 66.0<br />season: Summer","date: 1999-09-28<br />temp: 58.0<br />season: Summer","date: 1999-09-29<br />temp: 56.0<br />season: Summer","date: 1999-09-30<br />temp: 56.0<br />season: Summer","date: 2000-07-01<br />temp: 71.0<br />season: Summer","date: 2000-07-02<br />temp: 74.0<br />season: Summer","date: 2000-07-03<br />temp: 68.0<br />season: Summer","date: 2000-07-04<br />temp: 70.0<br />season: Summer","date: 2000-07-05<br />temp: 76.0<br />season: Summer","date: 2000-07-06<br />temp: 74.0<br />season: Summer","date: 2000-07-07<br />temp: 70.0<br />season: Summer","date: 2000-07-08<br />temp: 73.0<br />season: Summer","date: 2000-07-09<br />temp: 81.0<br />season: Summer","date: 2000-07-10<br />temp: 73.0<br />season: Summer","date: 2000-07-11<br />temp: 69.0<br />season: Summer","date: 2000-07-12<br />temp: 72.0<br />season: Summer","date: 2000-07-13<br />temp: 75.0<br />season: Summer","date: 2000-07-14<br />temp: 77.0<br />season: Summer","date: 2000-07-15<br />temp: 73.0<br />season: Summer","date: 2000-07-16<br />temp: 73.0<br />season: Summer","date: 2000-07-17<br />temp: 77.0<br />season: Summer","date: 2000-07-18<br />temp: 66.0<br />season: Summer","date: 2000-07-19<br />temp: 63.0<br />season: Summer","date: 2000-07-20<br />temp: 67.0<br />season: Summer","date: 2000-07-21<br />temp: 67.0<br />season: Summer","date: 2000-07-22<br />temp: 63.0<br />season: Summer","date: 2000-07-23<br />temp: 63.0<br />season: Summer","date: 2000-07-24<br />temp: 66.0<br />season: Summer","date: 2000-07-25<br />temp: 69.0<br />season: Summer","date: 2000-07-26<br />temp: 73.0<br />season: Summer","date: 2000-07-27<br />temp: 77.0<br />season: Summer","date: 2000-07-28<br />temp: 74.0<br />season: Summer","date: 2000-07-29<br />temp: 73.0<br />season: Summer","date: 2000-07-30<br />temp: 71.0<br />season: Summer","date: 2000-07-31<br />temp: 70.0<br />season: Summer","date: 2000-08-01<br />temp: 73.0<br />season: Summer","date: 2000-08-02<br />temp: 76.0<br />season: Summer","date: 2000-08-03<br />temp: 63.0<br />season: Summer","date: 2000-08-04<br />temp: 67.0<br />season: Summer","date: 2000-08-05<br />temp: 67.0<br />season: Summer","date: 2000-08-06<br />temp: 78.0<br />season: Summer","date: 2000-08-07<br />temp: 76.0<br />season: Summer","date: 2000-08-08<br />temp: 77.0<br />season: Summer","date: 2000-08-09<br />temp: 79.0<br />season: Summer","date: 2000-08-10<br />temp: 74.0<br />season: Summer","date: 2000-08-11<br />temp: 71.0<br />season: Summer","date: 2000-08-12<br />temp: 71.0<br />season: Summer","date: 2000-08-13<br />temp: 74.0<br />season: Summer","date: 2000-08-14<br />temp: 76.0<br />season: Summer","date: 2000-08-15<br />temp: 82.0<br />season: Summer","date: 2000-08-16<br />temp: 70.0<br />season: Summer","date: 2000-08-17<br />temp: 68.0<br />season: Summer","date: 2000-08-18<br />temp: 67.0<br />season: Summer","date: 2000-08-19<br />temp: 64.0<br />season: Summer","date: 2000-08-20<br />temp: 65.0<br />season: Summer","date: 2000-08-21<br />temp: 67.0<br />season: Summer","date: 2000-08-22<br />temp: 76.0<br />season: Summer","date: 2000-08-23<br />temp: 76.0<br />season: Summer","date: 2000-08-24<br />temp: 71.0<br />season: Summer","date: 2000-08-25<br />temp: 70.0<br />season: Summer","date: 2000-08-26<br />temp: 73.0<br />season: Summer","date: 2000-08-27<br />temp: 72.0<br />season: Summer","date: 2000-08-28<br />temp: 75.0<br />season: Summer","date: 2000-08-29<br />temp: 77.0<br />season: Summer","date: 2000-08-30<br />temp: 77.0<br />season: Summer","date: 2000-08-31<br />temp: 80.0<br />season: Summer","date: 2000-09-01<br />temp: 80.0<br />season: Summer","date: 2000-09-02<br />temp: 79.0<br />season: Summer","date: 2000-09-03<br />temp: 77.0<br />season: Summer","date: 2000-09-04<br />temp: 66.0<br />season: Summer","date: 2000-09-05<br />temp: 63.0<br />season: Summer","date: 2000-09-06<br />temp: 61.0<br />season: Summer","date: 2000-09-07<br />temp: 74.0<br />season: Summer","date: 2000-09-08<br />temp: 75.0<br />season: Summer","date: 2000-09-09<br />temp: 76.0<br />season: Summer","date: 2000-09-10<br />temp: 78.0<br />season: Summer","date: 2000-09-11<br />temp: 75.0<br />season: Summer","date: 2000-09-12<br />temp: 63.0<br />season: Summer","date: 2000-09-13<br />temp: 63.0<br />season: Summer","date: 2000-09-14<br />temp: 65.0<br />season: Summer","date: 2000-09-15<br />temp: 56.0<br />season: Summer","date: 2000-09-16<br />temp: 56.0<br />season: Summer","date: 2000-09-17<br />temp: 68.0<br />season: Summer","date: 2000-09-18<br />temp: 69.0<br />season: Summer","date: 2000-09-19<br />temp: 75.0<br />season: Summer","date: 2000-09-20<br />temp: 58.0<br />season: Summer","date: 2000-09-21<br />temp: 54.0<br />season: Summer","date: 2000-09-22<br />temp: 58.0<br />season: Summer","date: 2000-09-23<br />temp: 63.0<br />season: Summer","date: 2000-09-24<br />temp: 53.0<br />season: Summer","date: 2000-09-25<br />temp: 49.0<br />season: Summer","date: 2000-09-26<br />temp: 56.0<br />season: Summer","date: 2000-09-27<br />temp: 59.0<br />season: Summer","date: 2000-09-28<br />temp: 55.0<br />season: Summer","date: 2000-09-29<br />temp: 61.0<br />season: Summer","date: 2000-09-30<br />temp: 65.0<br />season: Summer"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,196,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(0,191,196,1)"}},"hoveron":"points","name":"Summer","legendgroup":"Summer","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[10135,10136,10137,10138,10139,10140,10141,10142,10143,10144,10145,10146,10147,10148,10149,10150,10151,10152,10153,10154,10155,10156,10157,10158,10159,10160,10161,10162,10163,10164,10165,10166,10167,10168,10169,10170,10171,10172,10173,10174,10175,10176,10177,10178,10179,10180,10181,10182,10183,10184,10185,10186,10187,10188,10189,10190,10191,10192,10193,10194,10195,10196,10197,10198,10199,10200,10201,10202,10203,10204,10205,10206,10207,10208,10209,10210,10211,10212,10213,10214,10215,10216,10217,10218,10219,10220,10221,10222,10223,10224,10225,10226,10500,10501,10502,10503,10504,10505,10506,10507,10508,10509,10510,10511,10512,10513,10514,10515,10516,10517,10518,10519,10520,10521,10522,10523,10524,10525,10526,10527,10528,10529,10530,10531,10532,10533,10534,10535,10536,10537,10538,10539,10540,10541,10542,10543,10544,10545,10546,10547,10548,10549,10550,10551,10552,10553,10554,10555,10556,10557,10558,10559,10560,10561,10562,10563,10564,10565,10566,10567,10568,10569,10570,10571,10572,10573,10574,10575,10576,10577,10578,10579,10580,10581,10582,10583,10584,10585,10586,10587,10588,10589,10590,10591,10865,10866,10867,10868,10869,10870,10871,10872,10873,10874,10875,10876,10877,10878,10879,10880,10881,10882,10883,10884,10885,10886,10887,10888,10889,10890,10891,10892,10893,10894,10895,10896,10897,10898,10899,10900,10901,10902,10903,10904,10905,10906,10907,10908,10909,10910,10911,10912,10913,10914,10915,10916,10917,10918,10919,10920,10921,10922,10923,10924,10925,10926,10927,10928,10929,10930,10931,10932,10933,10934,10935,10936,10937,10938,10939,10940,10941,10942,10943,10944,10945,10946,10947,10948,10949,10950,10951,10952,10953,10954,10955,10956,11231,11232,11233,11234,11235,11236,11237,11238,11239,11240,11241,11242,11243,11244,11245,11246,11247,11248,11249,11250,11251,11252,11253,11254,11255,11256,11257,11258,11259,11260,11261,11262,11263,11264,11265,11266,11267,11268,11269,11270,11271,11272,11273,11274,11275,11276,11277,11278,11279,11280,11281,11282,11283,11284,11285,11286,11287,11288,11289,11290,11291,11292,11293,11294,11295,11296,11297,11298,11299,11300,11301,11302,11303,11304,11305,11306,11307,11308,11309,11310,11311,11312,11313,11314,11315,11316,11317,11318,11319,11320,11321,11322],"y":[52,61,73,68,70,70.5,73.5,72.5,64,59,61.5,70,59.5,42.5,42.5,47.5,45.5,44.5,50,45.5,42.5,36,44.5,46,47,40,32.5,39,44,51,59.5,50,38.5,36.5,39,40,45,43,44,42.5,36.5,28.5,26.5,31.5,36,29,22,25.5,32,27.5,33.5,35.5,34.5,26,26.5,47,43,37.5,49,45.5,42,37,36.5,36.5,32,26,28.5,30,28.5,33,34.5,33.5,27.5,26.5,31,37.5,39,37,38.5,40.5,33,32.5,34.5,34,34,30.5,28.5,23,25.5,29.5,21.5,18,53,49,55,60,65,65,56,54,53,53,56,60,49,48,58,68,67,57,52,50,48,45,52,54,55,62,63,54,52,61,57,54,49,41,37,39,38,35,37,43,52,41,40,43,50,43,48,42,48,45,31,34,49,50,46,50,50,48,54,63,52,45,55,61,61,59,50,39,38,39,35,39,38,38,36,44,37,32,43,35,31,23,10,17,19,25,26,36,29,22,7,13,51,46,44,47,51,52,59,61,59,66,55,62,56,48,64,58,45,44,50,44,52,51,43,40,50,53,48,64,62,66,58,59,42,37,46,55,45,44,55,67,58,44,47,54,45,41,38,40,58,49,39,42,54,52,40,36,37,41,34,32,30,36,50,53,49,39,29,33,38,43,31,31,35,35,38,36,27,23,19,32,21,8,13,10,14,19,28,19,21,36,34,34,72,69,59,57,52,41,40,39,44,49,53,58,66,67,55,51,52,54,62,66,58,59,65,63,62,68,61,50,46,50,59,63,55,48,46,46,49,49,40,39,37,38,45,36,32,31,36,30,27,30,24,19,22,29,32,38,39,34,34,35,34,34,31,28,32,21,15,25,18,16,30,23,4,8,17,17,25,8,8,14,6,6,-1,8,5,5,11,12,13,21,27,16],"text":["date: 1997-10-01<br />temp: 52.0<br />season: Autumn","date: 1997-10-02<br />temp: 61.0<br />season: Autumn","date: 1997-10-03<br />temp: 73.0<br />season: Autumn","date: 1997-10-04<br />temp: 68.0<br />season: Autumn","date: 1997-10-05<br />temp: 70.0<br />season: Autumn","date: 1997-10-06<br />temp: 70.5<br />season: Autumn","date: 1997-10-07<br />temp: 73.5<br />season: Autumn","date: 1997-10-08<br />temp: 72.5<br />season: Autumn","date: 1997-10-09<br />temp: 64.0<br />season: Autumn","date: 1997-10-10<br />temp: 59.0<br />season: Autumn","date: 1997-10-11<br />temp: 61.5<br />season: Autumn","date: 1997-10-12<br />temp: 70.0<br />season: Autumn","date: 1997-10-13<br />temp: 59.5<br />season: Autumn","date: 1997-10-14<br />temp: 42.5<br />season: Autumn","date: 1997-10-15<br />temp: 42.5<br />season: Autumn","date: 1997-10-16<br />temp: 47.5<br />season: Autumn","date: 1997-10-17<br />temp: 45.5<br />season: Autumn","date: 1997-10-18<br />temp: 44.5<br />season: Autumn","date: 1997-10-19<br />temp: 50.0<br />season: Autumn","date: 1997-10-20<br />temp: 45.5<br />season: Autumn","date: 1997-10-21<br />temp: 42.5<br />season: Autumn","date: 1997-10-22<br />temp: 36.0<br />season: Autumn","date: 1997-10-23<br />temp: 44.5<br />season: Autumn","date: 1997-10-24<br />temp: 46.0<br />season: Autumn","date: 1997-10-25<br />temp: 47.0<br />season: Autumn","date: 1997-10-26<br />temp: 40.0<br />season: Autumn","date: 1997-10-27<br />temp: 32.5<br />season: Autumn","date: 1997-10-28<br />temp: 39.0<br />season: Autumn","date: 1997-10-29<br />temp: 44.0<br />season: Autumn","date: 1997-10-30<br />temp: 51.0<br />season: Autumn","date: 1997-10-31<br />temp: 59.5<br />season: Autumn","date: 1997-11-01<br />temp: 50.0<br />season: Autumn","date: 1997-11-02<br />temp: 38.5<br />season: Autumn","date: 1997-11-03<br />temp: 36.5<br />season: Autumn","date: 1997-11-04<br />temp: 39.0<br />season: Autumn","date: 1997-11-05<br />temp: 40.0<br />season: Autumn","date: 1997-11-06<br />temp: 45.0<br />season: Autumn","date: 1997-11-07<br />temp: 43.0<br />season: Autumn","date: 1997-11-08<br />temp: 44.0<br />season: Autumn","date: 1997-11-09<br />temp: 42.5<br />season: Autumn","date: 1997-11-10<br />temp: 36.5<br />season: Autumn","date: 1997-11-11<br />temp: 28.5<br />season: Autumn","date: 1997-11-12<br />temp: 26.5<br />season: Autumn","date: 1997-11-13<br />temp: 31.5<br />season: Autumn","date: 1997-11-14<br />temp: 36.0<br />season: Autumn","date: 1997-11-15<br />temp: 29.0<br />season: Autumn","date: 1997-11-16<br />temp: 22.0<br />season: Autumn","date: 1997-11-17<br />temp: 25.5<br />season: Autumn","date: 1997-11-18<br />temp: 32.0<br />season: Autumn","date: 1997-11-19<br />temp: 27.5<br />season: Autumn","date: 1997-11-20<br />temp: 33.5<br />season: Autumn","date: 1997-11-21<br />temp: 35.5<br />season: Autumn","date: 1997-11-22<br />temp: 34.5<br />season: Autumn","date: 1997-11-23<br />temp: 26.0<br />season: Autumn","date: 1997-11-24<br />temp: 26.5<br />season: Autumn","date: 1997-11-25<br />temp: 47.0<br />season: Autumn","date: 1997-11-26<br />temp: 43.0<br />season: Autumn","date: 1997-11-27<br />temp: 37.5<br />season: Autumn","date: 1997-11-28<br />temp: 49.0<br />season: Autumn","date: 1997-11-29<br />temp: 45.5<br />season: Autumn","date: 1997-11-30<br />temp: 42.0<br />season: Autumn","date: 1997-12-01<br />temp: 37.0<br />season: Autumn","date: 1997-12-02<br />temp: 36.5<br />season: Autumn","date: 1997-12-03<br />temp: 36.5<br />season: Autumn","date: 1997-12-04<br />temp: 32.0<br />season: Autumn","date: 1997-12-05<br />temp: 26.0<br />season: Autumn","date: 1997-12-06<br />temp: 28.5<br />season: Autumn","date: 1997-12-07<br />temp: 30.0<br />season: Autumn","date: 1997-12-08<br />temp: 28.5<br />season: Autumn","date: 1997-12-09<br />temp: 33.0<br />season: Autumn","date: 1997-12-10<br />temp: 34.5<br />season: Autumn","date: 1997-12-11<br />temp: 33.5<br />season: Autumn","date: 1997-12-12<br />temp: 27.5<br />season: Autumn","date: 1997-12-13<br />temp: 26.5<br />season: Autumn","date: 1997-12-14<br />temp: 31.0<br />season: Autumn","date: 1997-12-15<br />temp: 37.5<br />season: Autumn","date: 1997-12-16<br />temp: 39.0<br />season: Autumn","date: 1997-12-17<br />temp: 37.0<br />season: Autumn","date: 1997-12-18<br />temp: 38.5<br />season: Autumn","date: 1997-12-19<br />temp: 40.5<br />season: Autumn","date: 1997-12-20<br />temp: 33.0<br />season: Autumn","date: 1997-12-21<br />temp: 32.5<br />season: Autumn","date: 1997-12-22<br />temp: 34.5<br />season: Autumn","date: 1997-12-23<br />temp: 34.0<br />season: Autumn","date: 1997-12-24<br />temp: 34.0<br />season: Autumn","date: 1997-12-25<br />temp: 30.5<br />season: Autumn","date: 1997-12-26<br />temp: 28.5<br />season: Autumn","date: 1997-12-27<br />temp: 23.0<br />season: Autumn","date: 1997-12-28<br />temp: 25.5<br />season: Autumn","date: 1997-12-29<br />temp: 29.5<br />season: Autumn","date: 1997-12-30<br />temp: 21.5<br />season: Autumn","date: 1997-12-31<br />temp: 18.0<br />season: Autumn","date: 1998-10-01<br />temp: 53.0<br />season: Autumn","date: 1998-10-02<br />temp: 49.0<br />season: Autumn","date: 1998-10-03<br />temp: 55.0<br />season: Autumn","date: 1998-10-04<br />temp: 60.0<br />season: Autumn","date: 1998-10-05<br />temp: 65.0<br />season: Autumn","date: 1998-10-06<br />temp: 65.0<br />season: Autumn","date: 1998-10-07<br />temp: 56.0<br />season: Autumn","date: 1998-10-08<br />temp: 54.0<br />season: Autumn","date: 1998-10-09<br />temp: 53.0<br />season: Autumn","date: 1998-10-10<br />temp: 53.0<br />season: Autumn","date: 1998-10-11<br />temp: 56.0<br />season: Autumn","date: 1998-10-12<br />temp: 60.0<br />season: Autumn","date: 1998-10-13<br />temp: 49.0<br />season: Autumn","date: 1998-10-14<br />temp: 48.0<br />season: Autumn","date: 1998-10-15<br />temp: 58.0<br />season: Autumn","date: 1998-10-16<br />temp: 68.0<br />season: Autumn","date: 1998-10-17<br />temp: 67.0<br />season: Autumn","date: 1998-10-18<br />temp: 57.0<br />season: Autumn","date: 1998-10-19<br />temp: 52.0<br />season: Autumn","date: 1998-10-20<br />temp: 50.0<br />season: Autumn","date: 1998-10-21<br />temp: 48.0<br />season: Autumn","date: 1998-10-22<br />temp: 45.0<br />season: Autumn","date: 1998-10-23<br />temp: 52.0<br />season: Autumn","date: 1998-10-24<br />temp: 54.0<br />season: Autumn","date: 1998-10-25<br />temp: 55.0<br />season: Autumn","date: 1998-10-26<br />temp: 62.0<br />season: Autumn","date: 1998-10-27<br />temp: 63.0<br />season: Autumn","date: 1998-10-28<br />temp: 54.0<br />season: Autumn","date: 1998-10-29<br />temp: 52.0<br />season: Autumn","date: 1998-10-30<br />temp: 61.0<br />season: Autumn","date: 1998-10-31<br />temp: 57.0<br />season: Autumn","date: 1998-11-01<br />temp: 54.0<br />season: Autumn","date: 1998-11-02<br />temp: 49.0<br />season: Autumn","date: 1998-11-03<br />temp: 41.0<br />season: Autumn","date: 1998-11-04<br />temp: 37.0<br />season: Autumn","date: 1998-11-05<br />temp: 39.0<br />season: Autumn","date: 1998-11-06<br />temp: 38.0<br />season: Autumn","date: 1998-11-07<br />temp: 35.0<br />season: Autumn","date: 1998-11-08<br />temp: 37.0<br />season: Autumn","date: 1998-11-09<br />temp: 43.0<br />season: Autumn","date: 1998-11-10<br />temp: 52.0<br />season: Autumn","date: 1998-11-11<br />temp: 41.0<br />season: Autumn","date: 1998-11-12<br />temp: 40.0<br />season: Autumn","date: 1998-11-13<br />temp: 43.0<br />season: Autumn","date: 1998-11-14<br />temp: 50.0<br />season: Autumn","date: 1998-11-15<br />temp: 43.0<br />season: Autumn","date: 1998-11-16<br />temp: 48.0<br />season: Autumn","date: 1998-11-17<br />temp: 42.0<br />season: Autumn","date: 1998-11-18<br />temp: 48.0<br />season: Autumn","date: 1998-11-19<br />temp: 45.0<br />season: Autumn","date: 1998-11-20<br />temp: 31.0<br />season: Autumn","date: 1998-11-21<br />temp: 34.0<br />season: Autumn","date: 1998-11-22<br />temp: 49.0<br />season: Autumn","date: 1998-11-23<br />temp: 50.0<br />season: Autumn","date: 1998-11-24<br />temp: 46.0<br />season: Autumn","date: 1998-11-25<br />temp: 50.0<br />season: Autumn","date: 1998-11-26<br />temp: 50.0<br />season: Autumn","date: 1998-11-27<br />temp: 48.0<br />season: Autumn","date: 1998-11-28<br />temp: 54.0<br />season: Autumn","date: 1998-11-29<br />temp: 63.0<br />season: Autumn","date: 1998-11-30<br />temp: 52.0<br />season: Autumn","date: 1998-12-01<br />temp: 45.0<br />season: Autumn","date: 1998-12-02<br />temp: 55.0<br />season: Autumn","date: 1998-12-03<br />temp: 61.0<br />season: Autumn","date: 1998-12-04<br />temp: 61.0<br />season: Autumn","date: 1998-12-05<br />temp: 59.0<br />season: Autumn","date: 1998-12-06<br />temp: 50.0<br />season: Autumn","date: 1998-12-07<br />temp: 39.0<br />season: Autumn","date: 1998-12-08<br />temp: 38.0<br />season: Autumn","date: 1998-12-09<br />temp: 39.0<br />season: Autumn","date: 1998-12-10<br />temp: 35.0<br />season: Autumn","date: 1998-12-11<br />temp: 39.0<br />season: Autumn","date: 1998-12-12<br />temp: 38.0<br />season: Autumn","date: 1998-12-13<br />temp: 38.0<br />season: Autumn","date: 1998-12-14<br />temp: 36.0<br />season: Autumn","date: 1998-12-15<br />temp: 44.0<br />season: Autumn","date: 1998-12-16<br />temp: 37.0<br />season: Autumn","date: 1998-12-17<br />temp: 32.0<br />season: Autumn","date: 1998-12-18<br />temp: 43.0<br />season: Autumn","date: 1998-12-19<br />temp: 35.0<br />season: Autumn","date: 1998-12-20<br />temp: 31.0<br />season: Autumn","date: 1998-12-21<br />temp: 23.0<br />season: Autumn","date: 1998-12-22<br />temp: 10.0<br />season: Autumn","date: 1998-12-23<br />temp: 17.0<br />season: Autumn","date: 1998-12-24<br />temp: 19.0<br />season: Autumn","date: 1998-12-25<br />temp: 25.0<br />season: Autumn","date: 1998-12-26<br />temp: 26.0<br />season: Autumn","date: 1998-12-27<br />temp: 36.0<br />season: Autumn","date: 1998-12-28<br />temp: 29.0<br />season: Autumn","date: 1998-12-29<br />temp: 22.0<br />season: Autumn","date: 1998-12-30<br />temp:  7.0<br />season: Autumn","date: 1998-12-31<br />temp: 13.0<br />season: Autumn","date: 1999-10-01<br />temp: 51.0<br />season: Autumn","date: 1999-10-02<br />temp: 46.0<br />season: Autumn","date: 1999-10-03<br />temp: 44.0<br />season: Autumn","date: 1999-10-04<br />temp: 47.0<br />season: Autumn","date: 1999-10-05<br />temp: 51.0<br />season: Autumn","date: 1999-10-06<br />temp: 52.0<br />season: Autumn","date: 1999-10-07<br />temp: 59.0<br />season: Autumn","date: 1999-10-08<br />temp: 61.0<br />season: Autumn","date: 1999-10-09<br />temp: 59.0<br />season: Autumn","date: 1999-10-10<br />temp: 66.0<br />season: Autumn","date: 1999-10-11<br />temp: 55.0<br />season: Autumn","date: 1999-10-12<br />temp: 62.0<br />season: Autumn","date: 1999-10-13<br />temp: 56.0<br />season: Autumn","date: 1999-10-14<br />temp: 48.0<br />season: Autumn","date: 1999-10-15<br />temp: 64.0<br />season: Autumn","date: 1999-10-16<br />temp: 58.0<br />season: Autumn","date: 1999-10-17<br />temp: 45.0<br />season: Autumn","date: 1999-10-18<br />temp: 44.0<br />season: Autumn","date: 1999-10-19<br />temp: 50.0<br />season: Autumn","date: 1999-10-20<br />temp: 44.0<br />season: Autumn","date: 1999-10-21<br />temp: 52.0<br />season: Autumn","date: 1999-10-22<br />temp: 51.0<br />season: Autumn","date: 1999-10-23<br />temp: 43.0<br />season: Autumn","date: 1999-10-24<br />temp: 40.0<br />season: Autumn","date: 1999-10-25<br />temp: 50.0<br />season: Autumn","date: 1999-10-26<br />temp: 53.0<br />season: Autumn","date: 1999-10-27<br />temp: 48.0<br />season: Autumn","date: 1999-10-28<br />temp: 64.0<br />season: Autumn","date: 1999-10-29<br />temp: 62.0<br />season: Autumn","date: 1999-10-30<br />temp: 66.0<br />season: Autumn","date: 1999-10-31<br />temp: 58.0<br />season: Autumn","date: 1999-11-01<br />temp: 59.0<br />season: Autumn","date: 1999-11-02<br />temp: 42.0<br />season: Autumn","date: 1999-11-03<br />temp: 37.0<br />season: Autumn","date: 1999-11-04<br />temp: 46.0<br />season: Autumn","date: 1999-11-05<br />temp: 55.0<br />season: Autumn","date: 1999-11-06<br />temp: 45.0<br />season: Autumn","date: 1999-11-07<br />temp: 44.0<br />season: Autumn","date: 1999-11-08<br />temp: 55.0<br />season: Autumn","date: 1999-11-09<br />temp: 67.0<br />season: Autumn","date: 1999-11-10<br />temp: 58.0<br />season: Autumn","date: 1999-11-11<br />temp: 44.0<br />season: Autumn","date: 1999-11-12<br />temp: 47.0<br />season: Autumn","date: 1999-11-13<br />temp: 54.0<br />season: Autumn","date: 1999-11-14<br />temp: 45.0<br />season: Autumn","date: 1999-11-15<br />temp: 41.0<br />season: Autumn","date: 1999-11-16<br />temp: 38.0<br />season: Autumn","date: 1999-11-17<br />temp: 40.0<br />season: Autumn","date: 1999-11-18<br />temp: 58.0<br />season: Autumn","date: 1999-11-19<br />temp: 49.0<br />season: Autumn","date: 1999-11-20<br />temp: 39.0<br />season: Autumn","date: 1999-11-21<br />temp: 42.0<br />season: Autumn","date: 1999-11-22<br />temp: 54.0<br />season: Autumn","date: 1999-11-23<br />temp: 52.0<br />season: Autumn","date: 1999-11-24<br />temp: 40.0<br />season: Autumn","date: 1999-11-25<br />temp: 36.0<br />season: Autumn","date: 1999-11-26<br />temp: 37.0<br />season: Autumn","date: 1999-11-27<br />temp: 41.0<br />season: Autumn","date: 1999-11-28<br />temp: 34.0<br />season: Autumn","date: 1999-11-29<br />temp: 32.0<br />season: Autumn","date: 1999-11-30<br />temp: 30.0<br />season: Autumn","date: 1999-12-01<br />temp: 36.0<br />season: Autumn","date: 1999-12-02<br />temp: 50.0<br />season: Autumn","date: 1999-12-03<br />temp: 53.0<br />season: Autumn","date: 1999-12-04<br />temp: 49.0<br />season: Autumn","date: 1999-12-05<br />temp: 39.0<br />season: Autumn","date: 1999-12-06<br />temp: 29.0<br />season: Autumn","date: 1999-12-07<br />temp: 33.0<br />season: Autumn","date: 1999-12-08<br />temp: 38.0<br />season: Autumn","date: 1999-12-09<br />temp: 43.0<br />season: Autumn","date: 1999-12-10<br />temp: 31.0<br />season: Autumn","date: 1999-12-11<br />temp: 31.0<br />season: Autumn","date: 1999-12-12<br />temp: 35.0<br />season: Autumn","date: 1999-12-13<br />temp: 35.0<br />season: Autumn","date: 1999-12-14<br />temp: 38.0<br />season: Autumn","date: 1999-12-15<br />temp: 36.0<br />season: Autumn","date: 1999-12-16<br />temp: 27.0<br />season: Autumn","date: 1999-12-17<br />temp: 23.0<br />season: Autumn","date: 1999-12-18<br />temp: 19.0<br />season: Autumn","date: 1999-12-19<br />temp: 32.0<br />season: Autumn","date: 1999-12-20<br />temp: 21.0<br />season: Autumn","date: 1999-12-21<br />temp:  8.0<br />season: Autumn","date: 1999-12-22<br />temp: 13.0<br />season: Autumn","date: 1999-12-23<br />temp: 10.0<br />season: Autumn","date: 1999-12-24<br />temp: 14.0<br />season: Autumn","date: 1999-12-25<br />temp: 19.0<br />season: Autumn","date: 1999-12-26<br />temp: 28.0<br />season: Autumn","date: 1999-12-27<br />temp: 19.0<br />season: Autumn","date: 1999-12-28<br />temp: 21.0<br />season: Autumn","date: 1999-12-29<br />temp: 36.0<br />season: Autumn","date: 1999-12-30<br />temp: 34.0<br />season: Autumn","date: 1999-12-31<br />temp: 34.0<br />season: Autumn","date: 2000-10-01<br />temp: 72.0<br />season: Autumn","date: 2000-10-02<br />temp: 69.0<br />season: Autumn","date: 2000-10-03<br />temp: 59.0<br />season: Autumn","date: 2000-10-04<br />temp: 57.0<br />season: Autumn","date: 2000-10-05<br />temp: 52.0<br />season: Autumn","date: 2000-10-06<br />temp: 41.0<br />season: Autumn","date: 2000-10-07<br />temp: 40.0<br />season: Autumn","date: 2000-10-08<br />temp: 39.0<br />season: Autumn","date: 2000-10-09<br />temp: 44.0<br />season: Autumn","date: 2000-10-10<br />temp: 49.0<br />season: Autumn","date: 2000-10-11<br />temp: 53.0<br />season: Autumn","date: 2000-10-12<br />temp: 58.0<br />season: Autumn","date: 2000-10-13<br />temp: 66.0<br />season: Autumn","date: 2000-10-14<br />temp: 67.0<br />season: Autumn","date: 2000-10-15<br />temp: 55.0<br />season: Autumn","date: 2000-10-16<br />temp: 51.0<br />season: Autumn","date: 2000-10-17<br />temp: 52.0<br />season: Autumn","date: 2000-10-18<br />temp: 54.0<br />season: Autumn","date: 2000-10-19<br />temp: 62.0<br />season: Autumn","date: 2000-10-20<br />temp: 66.0<br />season: Autumn","date: 2000-10-21<br />temp: 58.0<br />season: Autumn","date: 2000-10-22<br />temp: 59.0<br />season: Autumn","date: 2000-10-23<br />temp: 65.0<br />season: Autumn","date: 2000-10-24<br />temp: 63.0<br />season: Autumn","date: 2000-10-25<br />temp: 62.0<br />season: Autumn","date: 2000-10-26<br />temp: 68.0<br />season: Autumn","date: 2000-10-27<br />temp: 61.0<br />season: Autumn","date: 2000-10-28<br />temp: 50.0<br />season: Autumn","date: 2000-10-29<br />temp: 46.0<br />season: Autumn","date: 2000-10-30<br />temp: 50.0<br />season: Autumn","date: 2000-10-31<br />temp: 59.0<br />season: Autumn","date: 2000-11-01<br />temp: 63.0<br />season: Autumn","date: 2000-11-02<br />temp: 55.0<br />season: Autumn","date: 2000-11-03<br />temp: 48.0<br />season: Autumn","date: 2000-11-04<br />temp: 46.0<br />season: Autumn","date: 2000-11-05<br />temp: 46.0<br />season: Autumn","date: 2000-11-06<br />temp: 49.0<br />season: Autumn","date: 2000-11-07<br />temp: 49.0<br />season: Autumn","date: 2000-11-08<br />temp: 40.0<br />season: Autumn","date: 2000-11-09<br />temp: 39.0<br />season: Autumn","date: 2000-11-10<br />temp: 37.0<br />season: Autumn","date: 2000-11-11<br />temp: 38.0<br />season: Autumn","date: 2000-11-12<br />temp: 45.0<br />season: Autumn","date: 2000-11-13<br />temp: 36.0<br />season: Autumn","date: 2000-11-14<br />temp: 32.0<br />season: Autumn","date: 2000-11-15<br />temp: 31.0<br />season: Autumn","date: 2000-11-16<br />temp: 36.0<br />season: Autumn","date: 2000-11-17<br />temp: 30.0<br />season: Autumn","date: 2000-11-18<br />temp: 27.0<br />season: Autumn","date: 2000-11-19<br />temp: 30.0<br />season: Autumn","date: 2000-11-20<br />temp: 24.0<br />season: Autumn","date: 2000-11-21<br />temp: 19.0<br />season: Autumn","date: 2000-11-22<br />temp: 22.0<br />season: Autumn","date: 2000-11-23<br />temp: 29.0<br />season: Autumn","date: 2000-11-24<br />temp: 32.0<br />season: Autumn","date: 2000-11-25<br />temp: 38.0<br />season: Autumn","date: 2000-11-26<br />temp: 39.0<br />season: Autumn","date: 2000-11-27<br />temp: 34.0<br />season: Autumn","date: 2000-11-28<br />temp: 34.0<br />season: Autumn","date: 2000-11-29<br />temp: 35.0<br />season: Autumn","date: 2000-11-30<br />temp: 34.0<br />season: Autumn","date: 2000-12-01<br />temp: 34.0<br />season: Autumn","date: 2000-12-02<br />temp: 31.0<br />season: Autumn","date: 2000-12-03<br />temp: 28.0<br />season: Autumn","date: 2000-12-04<br />temp: 32.0<br />season: Autumn","date: 2000-12-05<br />temp: 21.0<br />season: Autumn","date: 2000-12-06<br />temp: 15.0<br />season: Autumn","date: 2000-12-07<br />temp: 25.0<br />season: Autumn","date: 2000-12-08<br />temp: 18.0<br />season: Autumn","date: 2000-12-09<br />temp: 16.0<br />season: Autumn","date: 2000-12-10<br />temp: 30.0<br />season: Autumn","date: 2000-12-11<br />temp: 23.0<br />season: Autumn","date: 2000-12-12<br />temp:  4.0<br />season: Autumn","date: 2000-12-13<br />temp:  8.0<br />season: Autumn","date: 2000-12-14<br />temp: 17.0<br />season: Autumn","date: 2000-12-15<br />temp: 17.0<br />season: Autumn","date: 2000-12-16<br />temp: 25.0<br />season: Autumn","date: 2000-12-17<br />temp:  8.0<br />season: Autumn","date: 2000-12-18<br />temp:  8.0<br />season: Autumn","date: 2000-12-19<br />temp: 14.0<br />season: Autumn","date: 2000-12-20<br />temp:  6.0<br />season: Autumn","date: 2000-12-21<br />temp:  6.0<br />season: Autumn","date: 2000-12-22<br />temp: -1.0<br />season: Autumn","date: 2000-12-23<br />temp:  8.0<br />season: Autumn","date: 2000-12-24<br />temp:  5.0<br />season: Autumn","date: 2000-12-25<br />temp:  5.0<br />season: Autumn","date: 2000-12-26<br />temp: 11.0<br />season: Autumn","date: 2000-12-27<br />temp: 12.0<br />season: Autumn","date: 2000-12-28<br />temp: 13.0<br />season: Autumn","date: 2000-12-29<br />temp: 21.0<br />season: Autumn","date: 2000-12-30<br />temp: 27.0<br />season: Autumn","date: 2000-12-31<br />temp: 16.0<br />season: Autumn"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(199,124,255,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(199,124,255,1)"}},"hoveron":"points","name":"Autumn","legendgroup":"Autumn","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":26.228310502283104,"r":7.3059360730593621,"b":40.182648401826491,"l":37.260273972602747},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[9789,11395],"tickmode":"array","ticktext":["1997","1998","1999","2000","2001"],"tickvals":[9862,10227,10592,10957,11323],"categoryorder":"array","categoryarray":["1997","1998","1999","2000","2001"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Year","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-7.6500000000000004,94.650000000000006],"tickmode":"array","ticktext":["0","25","50","75"],"tickvals":[-8.8817841970012523e-16,25,50,75],"categoryorder":"array","categoryarray":["0","25","50","75"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Temperature","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(51,51,51,1)","width":0.66417600664176002,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"season","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"9294e211d80":{"x":{},"y":{},"colour":{},"type":"scatter"}},"cur_data":"9294e211d80","visdat":{"9294e211d80":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
