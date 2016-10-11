---
layout:     post
title:      Using Shiny to create simple analytics service
date:       2016-07-16 15:31:19
summary:    How to create 2-way web service inside your favourite R language
categories: 
---


If you do analytics in R there two most convinient ways to share the results of your researh and collaboRate: R-Markdown and Shiny.
R-Markdown is perfect for 1-way interaction, while Shiny is 2-way. In the post below I wll desribe main steps you need to complete in order to run you service on the example of my [forecasting service](http://alexakimenko.shinyapps.io/GetForecast)
![GetForecast](https://raw.githubusercontent.com/alexakimenko/alexakimenko.github.io/master/images/GetForecast.png)

### Develop your app

First of all, you need to create 2 files: `ui.R` and `server.R`. First file will control the layout and appearance of your app, second will tell a computer what to do with input. Then you need to install and run Shiny package:

```R
install.packages("shiny")
library(shiny)
```

You can have a bunch of options how to build UI, however I prefer `shinydashboard`. You can develop really beautifull reports, something like this [one](https://gallery.shinyapps.io/087-crandash/). So you need to activate `shinydashboard` by running:

```R
install.packages("shinydashboard")
library(shinydashboard)
```

Next you need to fill `dashboardHeader`, `dashboardSidebar` and `dashboardBody`. In `dashboardBody` we will have input elements to communicate with the user - `dateRangeInput` (range of the dates from a file with actuals), `numericInput` (Number of periods ahead to forecast) and `fileInput` (actual). We also have `checkboxInput`  just to specify forecasting model type. Each input element has at least 3 parts: variable name (it will be used in `server.R` part), help text and default value. Below you can see the full sript of `ui.R` part

```R
dashboardPage(skin = "blue",
              dashboardHeader(title = "GetForecast"),
              dashboardSidebar(
                sidebarMenu(
                  menuItem("Forecast", tabName = "forecast", icon = icon("calculator"))
                )  
              ),
              dashboardBody(
                tabItems(
                  tabItem(tabName = "forecast",
                          helpText( "Forecasting is based on ",  a("STL decomposition",     href="https://www.otexts.org/fpp/6/5",target="_blank"),br(),
                                    "Please select input parameters below and upload your data"),
                          p(),
                          fluidRow(
                            column(4,
                              dateRangeInput("dates", label = h4("Date range"))),
                            column(4,
                              numericInput("n.ahead", label = h4("Number of periods ahead to forecast"), value = 6)),
                            column(4,
                              fileInput('file1', h4('Choose CSV File'),
                                      accept=c('text/csv', 
                                               'text/comma-separated-values,text/plain', 
                                               '.csv')))),
                            checkboxInput("nf_model_flag", label = "Net flow model*", value = F),
                            helpText("*Net flow model is a specific model, which is suitable for delinquency forecasting and works on product level only."),
                            h4("Forecast"),
                            tableOutput('contents')
                          )
                      )
                  )
              )
```

All your calculations in `server.R` part should be inside `shinyServer` function:

```R
shinyServer(function(input, output) {
})
```
Hovewer before developing `server.R` you need to understand a concept of `reactivity`. You can think of it as of Excel table, where if you change value in one sell other linked cell will be changed automatically. And unfortunatelly you need to explicitely tell Shiny to if you want your functions to be reactive. You can do it with `reactive` or `render` functions. Here is the example:

```R
output$contents <- renderTable({
  n.ahead<-input$n.ahead
  v<-data()
  if (is.null(v))
    return(NULL)
  if (input$nf_model_flag==T){
    ts<-net_flow_model(v,n.ahead)
  } else {
    ts<-standart_model(v,n.ahead)
  }
  data.frame(ts)
})
```
Thus the function above takes `input$n.ahead` as input from `ui.R`, result of reactive `data()` function, calculates `ts` and returns `data.frame(ts)`, which we output in `ui.R` with `tableOutput('contents')` function.


### Deploy your app

