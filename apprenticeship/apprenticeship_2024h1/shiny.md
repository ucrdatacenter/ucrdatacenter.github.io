---
title: "Data Center Apprenticeship:\nInteractive applications with Shiny"
subtitle: "Spring 2024" 
date: "Last updated: 2024-08-01"
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
hosted on Shiny. Some elements of this tutorial are based on Joachim
Goedhart’s [A Shiny start -
Tutorial](https://github.com/JoachimGoedhart/A_Shiny_start). The final
app script created in this tutorial is available
[here](https://github.com/ucrdatacenter/projects/blob/main/apprenticeship/2024h1/8_shiny/app.R).

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
data <- read_csv("https://raw.githubusercontent.com/ucrdatacenter/projects/main/apprenticeship/2024h1/1_intro/surveys.csv")

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
panel on the right. A few possible options for different overall layout
options are listed below.

- If you remove the `sidebarLayout(sidebarPanel(...), mainPanel(...))`
  function from the `ui` function and keep only the list of elements,
  the app will use a full-page layout instead, where the inputs and
  outputs are displayed one after the other.
- If you replace the `sidebarLayout(sidebarPanel(...), mainPanel(...))`
  function with `tabsetPanel(tabPanel(...), tabPanel(...))`, the app
  will use a tabbed layout, where the user can switch between different
  tabs, each displaying as a separate page.
- If you replace the `sidebarLayout(sidebarPanel(...), mainPanel(...))`
  function with `fluidRow(column(...), column(...))`, the app will use a
  column layout, where the inputs and outputs are displayed next to each
  other. You can adjust the width of each column and offset them to
  create space between the edges of the page and the page contents.
  Unlike the sidebar or tabset layouts, `fluidRow()` and `column()` can
  be used separately and interchangably: any elements inside the same
  `fluidRow()` are displayed next to each other, and any elements inside
  the same `column()` are displayed on top of each other.
- For other layout options, see the [Shiny layout
  guide](https://shiny.posit.co/r/articles/build/layout-guide/) and the
  Shiny cheatsheet linked above.

Note that the all layouts explained above are still always inside a
`fluidPage()` function, which should always be the outermost function in
the `ui` function.

In addition to adjusting the overall layout, you can add other elements
to the page to give it more structure on top of the inputs and outputs.
Realize that the way the UI is structured is very similar to HTML
script. After all, that’s exactly what’s happening in the background
when you compile your app: R is generating HTML code that is then
displayed in the browser. So it’s also possible to add other elements to
the page that translate to HTML code: indeed, R has an extensive list of
functions that generate HTML code, such as headers, paragraphs,
horizontal lines, bold text, line breaks and more. For example, the
`h2("header text")` function in Shiny corresponds to
`<h2>header text</h2>` in HTML, while a separating line can be added
with `hr()`. Again, the Shiny cheatsheet contains a longer list of these
functions. So let’s add some of these elements to our current definition
of the UI main panel to make the app outputs better structured.

``` r
mainPanel(
  h3("Histogram of chosen variable"), # header for the plot
  plotOutput("plot"),
  textOutput("summary"),
  hr(), # separating line between outputs
  h3("Species counts"), # header for the table
  dataTableOutput("counts")
)
```

## Reactive objects

Now that the app interface looks better, let’s make the code more
efficient. Currently, much the code inside the `renderPlot()` and
`renderTable()` functions is identical: we filter the data in the same
way, and then we either plot or summarize the data. So it would be more
efficient to define a new object inside the server that filters the
data, and use that new, filtered data as the inputs both in the
`renderPlot()` and `renderTable()` functions.

The problem is that you can’t define a new object inside the `server`
function like how you would define a new object in a regular R script.
Instead, you need to use a special function called `reactive()` that
defines a new object that is reactive to the inputs. Once you’ve called
the `reactive({})` function, you can specify the code that defines the
object inside the curly braces, and you can refer to the object in other
elements of `server` by the name of the reactive object followed by
parentheses (as if you were calling a function, which is technically
what you’re doing).

So let’s add an element to the `server` code that defines a new reactive
object called `data_filtered` that filters the data based on the user’s
choices. Then we can replace the `data` object in the `renderPlot()` and
`renderTable()` functions with `data_filtered()`. Make sure to define
the reactive object before the output elements that use the object.

``` r
server <- function(input, output) {
  # define reactive object
  data_filtered <- reactive({
    data |> 
      filter(year >= input$years[1], year <= input$years[2],
             plot_type %in% input$plot_type) |> 
      rename("value" = input$variable) |> 
      select(year, value, genus, species, plot_type)
  })
  
  output$plot <- renderPlot({
    # call reactive object and plot the value variable
    data_filtered() |>
      ggplot(aes(value)) +
      geom_histogram(bins = input$bins) +
      theme_light()
  })
  
  output$summary <- renderText({
    # call value variable from reactive object
    paste0("Sample mean of ", input$variable, ":\n", mean(data_filtered()$value))
  })
}
```

## Conditional UI

The last change we’ll make to the app is to make the plot is to
introduce a new input that appears on the interface only if a particular
condition is satisfied. In this case, we want to add a checkbox that
allows the user to facet the plot by plot type, but only if there is
more than one plot type selected.

There are multiple ways to create conditional UI elements, such as via
the `conditionalPanel()` function, or by using the `shinyjs` package
that allows you to hide/show UI elements. In this tutorial we’ll use the
`uiOutput()` and `renderUI()` functions, which allow you to define UI
elements in the `server` function.

Instead of specifying the details of the input element in the `ui`
function, we use `uiOutput()` to define a placeholder. Then we use
`renderUI()` to actually add the input function, wrapping it in an
if-statement that evaluates the input function only if a logical
condition is satisfied. So let’s add these elements to the app code.

``` r
# inside UI sidebarPanel()
uiOutput("facet_ui") # UI element defined in server

# inside server
output$facet_ui <- renderUI({
  # display facet option only if there is more than one facet
  if (length(input$plot_type) > 1) {
    # UI element definition
    checkboxInput("facet", # input ID
                  "Facet by plot type?", # displayed label
                  FALSE # initial value
    )
  }
})
```

Now that we have this additional input (that outputs a logical value of
length 1 for whether the plot should have facets), we need to adjust the
plot output to facet the plot if `input$facet == TRUE`. So let’s add an
if-statement to the `renderPlot()` function that adds the facetting if
the checkbox is checked. To do so, we’ll break up the existing plotting
function, assign the intermediate plot to an object, and then add the
facetting to that object only if the checkbox is checked, and display
the result.

``` r
output$plot <- renderPlot({
  # assign plot to object
  p <- data_filtered() |>
    ggplot(aes(value)) +
    geom_histogram(bins = input$bins) +
    theme_light()
  
  # add facetting if checkbox is checked
  if (input$facet) p <- p + facet_wrap(~plot_type)
  
  # display plot
  p
})
```

# Publishing the app on the web

Currently the app works well on your own computer, but it’s not yet
available on the web. To make it available on the web, you need to
publish it on the Shiny server. In order to do so, you first need to
make an account on [www.shinyapps.io](https://www.shinyapps.io/). A free
account allows you to publish up to 5 apps, and you can upgrade to a
paid account if you need more.

Once you have an account, you can find the button to publish your app in
RStudio next to the “Run App” button. When publishing an app for the
first time, you’ll be asked to install the `rsconnect` package, and link
your Shiny account to RStudio. You can simply follow the instructions
that RStudio provides when you click the “Publish the application”
button.

You’ll also need to select which files you’d like upload together with
your app script. This part is important if you are using data files from
your computer, as your app will only work online if all necessary files
are also uploaded to the Shiny server. So make sure to select all files
that you are using in your app, including the data file(s) and any other
files that you are sourcing in your app. On the other hand, make sure
not to upload any large and/or confidential files: if your app works
with such files, you should create a separate script that loads the data
and cleans it, save the resulting smaller and non-confidential file that
only contains the data you absolutely need, and load only that datafile
in your app script. In addition, make sure that any files that your app
needs are stored in the same folder as your app script and use relative
file paths, since absolute file paths will not work on the Shiny server.

If all goes well, your published app will be accessible at the URL
`https://username.shinyapps.io/appname`, so make sure to give the app a
sensible name.
