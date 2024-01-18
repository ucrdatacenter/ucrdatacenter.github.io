---
title: "Data Center Apprenticeship:\nInteractive applications with Shiny"
subtitle: "Spring 2024" 
date: "Last updated: 2024-01-18"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Introduction](#introduction)
- [The structure of a Shiny app](#the-structure-of-a-shiny-app)
- [Modifying the default app](#modifying-the-default-app)
  - [Changing the data, adding initial data
    cleaning](#changing-the-data-adding-initial-data-cleaning)
  - [Specifying new inputs](#specifying-new-inputs)
  - [Specifying new outputs that depend on the
    inputs](#specifying-new-outputs-that-depend-on-the-inputs)
- [Advanced options](#advanced-options)
  - [Different layout options](#different-layout-options)
  - [Reactive objects](#reactive-objects)
  - [Conditional UI](#conditional-ui)
- [Publishing the app on the web](#publishing-the-app-on-the-web)

# Introduction

Shiny allows you to create interactive applications using R. These
applications basically function as websites, and they allow the user to
control various characteristics of the output procudes on the page by
adjusting the inputs in a nice, user-friendly interface. That is, the
user gets to control how data that is analyzed and visualized, without
the need to write any code. You can host these applications on your
local device, or on the Shiny server: this latter option allows anybody
on the internet to find and use your app by visiting a URL.

In this tutorial, we discuss the basic layout of a Shiny app and some
examples of customizing the user interface, including inputs, outputs
and reactive elements. We also discuss how to publish your apps on the
Shiny server, and some considerations that specifically apply to apps
hosted on Shiny. The tutorial is partially based on …..

# The structure of a Shiny app

A Shiny app consists of two parts: the user interface (UI) and the
server. The UI defines what the user sees and interacts with, and the
server is the part that does the actual work of generating output. If
instead of opening a plain new R script, you go to File -\> New File -\>
Shiny Web App, RStudio will generate a template for a Shiny app that
contains these basic structures and some examples of how to use them.
The file also automatically includes loading the `shiny` package, so
make sure you have it installed. In addition, let’s load `tidyverse` as
usual on the top of the script.

In the rest of the tutorial we will create an app similar to
[this](https://amsterdamstudygroup.shinyapps.io/A_Shiny_start/) example
(although more complex) by modifying the default app script that R
creates. But first, let’s have a look at some pseudo-code showing the
standard contents of a Shiny app. The functions in uppercase are not
real functions, but denote a category of functions.

``` r
# packages: shiny + anything else used for data wrangling and visualization
library(shiny)
library(tidyverse)

# any data cleaning that doesn't depend on user input
data <- LOAD("data")

# user interface: inputs, outputs, other displayed items in the order of display on the page
ui <- fluidPage(
  # optional title
  titlePanel(),
  
  # page layout specification (e.g. sidebar, tabs, columns, fluidRows)
  LAYOUT(
    INPUT_ELEMENT("id_input", ...), # e.g. textbox, dropdown, buttons
    OUTPUT_ELEMENT("id_output"), # e.g. plots, tables
    OTHER_INTERFACE_ELEMENT # R functions generating HTML (e.g. headers, paragraphs)
  )
)

# function that regenerates outputs every time inputs are modified
server <- function(input, output) {
  
  # optional intermediate result: depends on input, affects output, but not displayed
  value <- REACTIVE_OBJECT({
    FUNCTION(data, input$id_input)
  })
  
  # displayed result: depends on input, possibly via intermediate reactive elements
  output$id_output <- RENDER_ELEMENT({
    FUNCTION(data, input$id_input, value())
  })
  
}

# run app given defined UI and server contents
shinyApp(ui = ui, server = server)
```

Below you find step-by-step instructions that you can follow to add and
modify functionality to a Shiny app that is created by default. With
each step, the functionality (and hence complexity) of the app
increases.

# Modifying the default app

## Changing the data, adding initial data cleaning

You can use your own data in a Shiny app by loading it in the same way
as you would in a regular R script. For example, you can load a CSV file
with `read_csv()` from the `readr` package. In this case, let’s use the
same dataset that we used in the data wrangling tutorial on animal
species and their observed characteristics.

You should load any data that you want to use in the app before the UI
and server functions. You should also undertake any data cleaning steps
that don’t depend on user input there. You do this for the purpose of
efficiency: code outside the server function is only run once, when the
app is started, whereas code inside the server function is run every
time the user changes an input.

So let’s add the code loading and cleaning the data to the top of the
script; make sure to also load `tidyverse` together with the `shiny`
package. In addition, let’s define a new tibble that summarizes the
number of observations per year and per species so that later we can
display these counts in a table in the app.

``` r
data <- read_csv("https://raw.githubusercontent.com/ucrdatacenter/projects/main/apprenticeship/1_intro/surveys.csv")

species_counts <- data |> 
  count(year, plot_type, genus, species)
```

We’ll be able to refer to these objects later when defining the `server`
and `ui` functions.

## Specifying new inputs

The default app contains a slider input that allows the user to control
the number of bins in a histogram. This slider input is only one of many
possible input elements that you can add to a Shiny app. You can add a
text box, a dropdown menu, a checkbox, a radio button, a date picker,
and more: the list of possible inputs is available on the right side of
the first page of [this
cheatsheet](https://ugoproto.github.io/ugo_r_doc/pdf/shiny.pdf),
including the main arguments each input type takes (for the full list of
arguments, check the relevant help files).

Let’s add a few different input elements to the app.

First, let’s adjust the existing slider input to allow the user to
adjust the year range from which we’ll display data. You can modify the
slider input by changing the arguments of the `sliderInput()` function
as follows.

``` r
sliderInput("years", # input ID
            "Year range", # displayed label
            min = 1977, # minimum value
            max = 2002, # maximum value
            value = 1977 # initial value
            )
```

You can also define the input characteristics by referencing the data
you previously loaded. For example, you can make the slider range from
the earliest to the latest year in the data by extracting the relevant
information from your data.

``` r
sliderInput("years", # input ID
            "Year range", # displayed label
            min = min(data$year), # minimum value
            max = max(data$year), # maximum value
            value = c(min(data$year), max(data$year)) # initial value: two elements create a range
            )
```

Next, let’s add checkboxes that let the user display data from as many
plot types as they want. If you want to add a single checkbox, you can
use the `checkboxInput()` function, and for multiple checkboxes, you can
use the `checkboxGroupInput()` function. In this case, let’s use all
unique plot types in the data as the choices for the checkboxes, and
let’s set the initial selection to select all plot types.

Make sure that all your inputs are separated with commas inside your
`ui` function.

``` r
checkboxGroupInput("plot_type", # input ID
                   "Plot type", # displayed label
                   choices = unique(data$plot_type), # choices
                   selected = unique(data$plot_type) # initial selection
                   )
```

Finally, let’s add two more inputs: a set of radio buttons that let the
user choose which of the two numerical variables to plot, and a numeric
input that lets the user control the number of bins in the histogram.

``` r
radioButtons("variable", # input ID
             "Displayed variable", # displayed label
             choiceNames = c("Hindfoot length", "Animal weight"), # displayed choices
             choiceValues = c("hindfoot_length", "weight"), # values returned by input
             ),
numericInput("bins", "Number of bins:", 30)
```

If you run the app now, you’ll see all the inputs show up in the sidebar
panel, and you can see how they are arranged and how you can change
their values. However, they are not yet connected to any outputs, so
changing their values doesn’t do anything yet. Fortunately you can
introduce new UI elements that take user input, but are not yet
connected to an action. This is nice, as it allows you to build the UI
elements gradually without breaking the app.

## Specifying new outputs that depend on the inputs

The default app creates a single output element: a histogram with the
identifier “plot”. The `ui` function places this element in the main
panel by including a `plotOutput()` in the `mainPanel()` function,
specifying the identifier of the plot as the only argument. Then the
`server` function provides the details of how to generate the plot by
defining the element `output$plot` as a `renderPlot({})` function (the
curly braces inside the parentheses allow us to define the plot contents
as a longer code chunk including intermediate objects, line breaks,
etc.).

Let’s modify this default plot and add a few more outputs to the app. To
change the `plot` object to a histogram of the filtered data and
selected variable, we can replace the current contents of the
`renderPlot({})` with code that takes our previously loaded data,
filters it, and plots our selected variable.

To access the values of the inputs defined by the input UI elements, we
can refer to them as `input$input_id` where `input_id` is the identifier
of the input element. For example, to access the value of the `years`
slider, we can use `input$years` – since `input$years` has two elements,
one for the minimum year and one for the maximum year selected, we can
access the individual elements by subsetting the `input$years` vector.
Notice that instead of calling `input$variable` in the `ggplot` function
directly, we are renaming the chosen variable to `value`, and referring
to the `value` variable later. The reason for this is that `ggplot`
expects the variable name to be an object, not a string (i.e. no
quotation signs), but `input$variable` is a string. The `rename()`
function, however, does not care whether variables are specified as
objects or strings, so we can apply this trick instead of having to use
more complex functions to convert character strings to objects.

``` r
data |> 
  filter(year >= input$years[1], year <= input$years[2],
         plot_type %in% input$plot_type) |> 
  rename("value" = input$variable) |> 
  select(year, value, genus, species, plot_type) |> 
  ggplot(aes(value)) +
        geom_histogram(bins = input$bins) +
        theme_light()
```

Don’t be surprised that the previous function does not work outside of a
Shiny app: the `input` object is only defined inside the `server`
function, so you can only access it there. Therefore if you’d like to
test your code, you can do that in two ways:

1.  Run the app and test it in the browser, making sure that all outputs
    look reasonable.
2.  Create a new R script, load the data, and copy the code from the
    `server` function into the new script, replacing all
    `input$input_id` references vectors that you define in that new
    script. (I tend to define e.g. `inputyears <- c(1980, 2000)` at the
    top of the script I use for this testing, since then the only thing
    I need to change in the copied server code is to remove the dollar
    signs `$` from the input specifications.)

Now let’s add some different output types to the app. The only
difference between the different output types is the function that you
use to define them in the `ui` and `server` functions: the `ui` function
uses `typeOutput()` and the `server` function uses `renderType()`, where
`type` is the type of output you want to create. The same
[cheatsheet](https://ugoproto.github.io/ugo_r_doc/pdf/shiny.pdf) linked
above also contains a list of possible output types and the
correspondence between `ui` and `server` functions (they are usually the
same, but not always, see e.g. `verbatimTextOutput` and `renderPrint`).

Let’s add a text output that displays the mean of the selected variable
in the data filtered by the user’s choices, let’s call it `summary`. For
that we can use the `renderText()` function in the `server` function,
and the `textOutput()` function in the `ui` function. Again, the
`textOutput()` just contains the object identifier, and the
`renderText()` function contains a code chunk that generates the text to
be displayed.

``` r
# inside UI mainPanel() (make sure to separate from plotOutput() with a comma)
textOutput("summary")

# inside server (no commas needed between output elements)
output$summary <- renderText({
  mean <- data |> 
    filter(year >= input$years[1], year <= input$years[2],
           plot_type %in% input$plot_type) |> 
    rename("value" = input$variable) |> 
    pull(value) |>
    mean()
  
  paste0("Sample mean of ", input$variable, ":\n", mean)
})
```

Finally, let’s add a table that displays the number of different species
observed in the filtered data. For this we can use our previously
defined `species_counts` object, and the `renderTable()` and
`tableOutput()` functions.

``` r
# inside UI mainPanel()
tableOutput("counts")

# inside server
output$counts <- renderTable({
  species_counts |> 
    filter(year >= input$years[1], year <= input$years[2],
           plot_type %in% input$plot_type) |> 
    group_by(genus, species) |> 
    summarize(n = sum(n))
})
```

Since the resulting table is quite long, it might be nicer to use
another element type that gives more control over how the data is
displayed: the `dataTableOutput()` and `renderDataTable()` functions
from the `DT` package split the data to multiple pages, displaying only
a certain number of observations at once, let the user sort the data
based on each column, and additional arguments even allow the user to
search for specific values in the table. So let’s install and load the
`DT` package and change `tableOutput` to `dataTableOutput()` and
`renderTable()` to `renderDataTable()` instead – no changes are needed
in the function body.

``` r
# inside UI mainPanel()
dataTableOutput("counts")

# inside server
output$counts <- renderDataTable({
  species_counts |> 
    filter(year >= input$years[1], year <= input$years[2],
           plot_type %in% input$plot_type) |> 
    group_by(genus, species) |> 
    summarize(n = sum(n))
})
```

# Advanced options

If you run the app now, you’ll see that all the features are there to
make it work properly: the user can customize many inputs, and these
inputs are immediately reflected in the outputs. However, we can do more
to customize the interface of the app, and to streamline the code.

## Different layout options

The default app uses a sidebar layout, where the inputs are displayed in
a sidebar panel on the left, and the outputs are displayed in the main
panel on the right.

## Reactive objects

## Conditional UI

# Publishing the app on the web

<!-- ## Bugs -->
<!-- Anytime that you write code, you will introduce bugs. Finding and solving bugs (debugging) is an integral part of writing code and (often) you learn something from this process. Debugging a Shiny app can be hard, and I have not found the best way of doing this. A bracket or comma that is missing or in the wrong place can break the app. So pay attention and be precise when introducing new lines of code. One piece of advice is to often run the app and see if it runs at all and if so, whether it runs as designed. If it does not run, check the newly introduced code and any brackets and commas near the new code. -->
<!-- ## Step 4 - Add a checkbox to control the plot -->
<!-- Add a new UI element `checkboxInput()`, which will be used to control whether to plot per `'Species'`: -->
<!-- ```          -->
<!-- checkboxInput("ungroup", "Per species?", value = FALSE) -->
<!-- ``` -->
<!-- Make sure that the elements are properly separated by commas. -->
<!-- Run the code to see whether the new UI element appears. -->
<!-- Let's connect the UI elements to the plotting code. First, we modify the plotting code to: -->
<!-- ```          -->
<!-- p <- ggplot(data = iris, aes(x=Sepal.Width)) + geom_histogram(bins = input$bins, alpha=input$transparency) -->
<!-- return(p) -->
<!-- ``` -->
<!-- > Although the output did not change, we have done something important here. We assign the plotting code to an object `p`. We return this object, which will be rendered as a plot by the app. This way of encoding a plot increases the flexibility, as we can add new functions to the object `p`. Let's look at an example below. -->
<!-- For displaying separate plots for 'Species' we can add (before returning p): -->
<!-- ```          -->
<!-- p <- p + facet_wrap(~Species) -->
<!-- ``` -->
<!-- **Question**: Use an `if()` statement to make the call to `facet_wrap()` conditional? -->
<!-- <details> -->
<!-- <summary>Hint</summary> -->
<!-- -   <code>input$ungroup</code> is "TRUE" or "FALSE", depending on the state of the checkbox. -->
<!-- </details> -->
<!-- <details> -->
<!-- <summary>Hint</summary> -->
<!-- -   To evaluate the state of the checkbx you can use <code>input$ungroup == TRUE</code> inside <code>if()</code> -->
<!-- </details> -->
<!-- <details> -->
<!-- <summary>Solution</summary> -->
<!-- -   <code>if (input$ungroup == TRUE) p <- p + facet_wrap(~Species)</code> -->
<!-- </details> -->
<!-- ## Step 6 - Add text to the UI -->
<!-- Text can be added to the UI, by using quotation marks. To insert a title for the sidebarpanel, add (followed by a comma): -->
<!-- ```          -->
<!-- "Plot controls" -->
<!-- ``` -->
<!-- Styling of the text is achieved with HTML tags as follows: -->
<!-- ```          -->
<!-- h2("Plot Controls") -->
<!-- ``` -->
<!-- The `h2()` in Shiny corresponds to `<h2></h2>` in HTML A separating line can be added with hr(): -->
<!-- ```          -->
<!-- h2("Plot Controls"), hr() -->
<!-- ``` -->
<!-- ## Step 7 - Conditional UI elements -->
<!-- UI elements can be hidden and revealed by conditional statements. Usually, this is controlled by a checkbox or radiobuttons. Here, we use the checkbox to make another UI element conditional. We can use the output of the checkbox with the `Id="ungroup"` to control a UI element with the function `conditionalPanel(condition = "input.ungroup==true", .......)`. After the condition, any other UI element can be inserted. Here, we insert text to explain the action of the checkbox: -->
<!-- ```          -->
<!-- conditionalPanel(condition = "input.ungroup==true", "The plot shows the data per species") -->
<!-- ``` -->
<!-- **Question**: Does the location of this conditional statement in the sidebarPanel() matter? -->
<!-- <details> -->
<!-- <summary>Solution</summary> -->
<!-- -   The location matters for the layout of the User Interface, but it does not matter for reactivity. It will react to the state of the checkbox, regardless of its position within <code>sidebarPanel()</code>. -->
<!-- </details> -->
<!-- ## Post Scriptum - Using your own data in an app -->
<!-- Thus far, we only used example datasets. If you want to use external datasets, there are a couple of options. The most straightforward way is to read a CSV file that is present in the same folder as the app.R file. The code to read the data is outside of the ui and server elements just like loading the packages is. So you can add this code just below the code to load the packages: -->
<!-- ```          -->
<!-- my_data <- read.csv(“data_for_app.csv”) -->
<!-- ``` -->
<!-- After that, you can replace any instances where you use a dataframe (`iris` in our example) by `my_data` -->
<!-- Suppose that you want to load external data, you could add the following line (this will actually work): -->
<!-- ```          -->
<!-- my_data <- read.csv("https://raw.githubusercontent.com/JoachimGoedhart/DataViz-protocols/main/Area_tidy.csv") -->
<!-- ``` -->
<!-- Note that when you load your own data, you need to adjust the variables as well if you want to plot this data. For instance, there is no column named `"Species"`, so the code needs to be adjusted accordingly. For this specific dataframe, this should work: -->
<!-- ```          -->
<!-- p <- ggplot(data = my_data, aes(x = value)) + geom_histogram(bins = input$bins, alpha=input$alpha) -->
<!-- if (input$ungroup) p <- p + facet_wrap(~Condition) -->
<!-- ``` -->
<!-- One approach that I use is to first hard-code the plot, based on the data. After that, I introduce the controls (sliders, input fields) in the User Interface. Finally, the controls can be connected to the variables in the code. Check frequently whether the app runs, because debugging can be complicated and becomes more difficult with every line of code that you add. -->
<!-- ## Post Post Scriptum - Dashboard versus Tool -->
<!-- When the data is included in the app, the result is much like a data *dashboard*. The users can interact with the data, visualize it in different ways to explore and understand it. Many of the apps that I developed are a data visualization *tool*. Users can upload their own data and explore it. This adds another layer of complexity, since this type of app needs to handle different types of data and the app needs to be designed with this in mind. Luckily there is code available that should provide some help to get started. You can check out [my Github repository](https://github.com/JoachimGoedhart) for inspiration! -->
