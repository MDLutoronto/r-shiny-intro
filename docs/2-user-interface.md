---
title: B. User Interface
parent: Introduction to R Shiny
layout: default
nav_order: 2
---

## B. User Interface

The user interface consists of the layout of the application, one or more control widgets that users can use to interact with the application, and the output objects.

 

Layout Options
--------------

The most common function that is used to create the user interface is the fluidPage() function. Alternatively, you can also use the fixedPage() or boostrapPage() functions.

Once you have created the page, you need to decide what type of layout you want for your application. There are a few layout options possible:

1. Single Page Sidebar Layout
2. Single Page Multiple Row Layout
3. Multiple Pages:

    1. Multiple Horizontal Tabs
    	2. Multiple Vertical Navigation Bars

You can read more about the different layout options [here](https://mastering-shiny.org/action-layout.html) and [here](https://shiny.posit.co/r/articles/build/layout-guide/).

 

Control Widgets
---------------

Once you have decided on the layout, you can pick the control widgets that users can use to interact with your application. The choice of control widgets depends on the type of input you want from users. The following image shows you the most common control widgets and the functions used to create them in R.

<img src='{{ '/assets/images/controlwidgets.png' | relative_url }}' alt='Control Widgets' title='' width='798' height='427' />

Image Source: [https://shiny.posit.co/r/getstarted/shiny-basics/lesson3/](https://shiny.posit.co/r/getstarted/shiny-basics/lesson3/)

 

TTC Subway Delays: User Interface
---------------------------------

In this section, we will complete the user interface object for our TTC subway delays application. At this stage, we have created the user interface with only a title.

```r
# 1. User Interface
ui <- fluidPage(
	titlePanel(“Toronto TTC Subway Delays”)
)
```
 

### Layout

The layout we will be using in this guide is the single page sidebar layout. To create the sidebar layout, we add a comma after the title and then we use the sidebarLayout() function. Inside this function, we specify the sidebar and the main panel using the sidebarPanel() and the mainPanel() functions respectively. And we separate these two functions using a comma.

```r
ui <- fluidPage(
  titlePanel("Toronto TTC Subway Delays"), 
  sidebarLayout(
		sidebarPanel(),
		mainPanel()
	)
)
```
<img src='{{ '/assets/images/sidebarlayout.png' | relative_url }}' alt='Sidebar Layout' title='' width='732' height='403' />

Image Source: [https://mastering-shiny.org/action-layout.html](https://mastering-shiny.org/action-layout.html)

 

In our application, we will add a few control widgets in the sidebar panel. Then we will add a graph output and a data table output in the main panel. Let’s start by adding our control widgets in the sidebarPanel() function.

```r
ui <- fluidPage(
	titlePanel("Toronto TTC Subway Delays"), 
	sidebarLayout(
		sidebarPanel(
            # Add Control Widgets Here
		), 
		mainPanel(
			# Add Graph Output and Data Table Output Here
		)
	)
)
```
 

### Slider Input: Year Range

First, we want to give the user the option to specify the years they are interested in using the slider input widget. To create a slider widget, we use the sliderInput() function.

```r
      # SLIDER: YEAR RANGE
      sliderInput(inputId="year",
                  label="Range of Years:", 
                  min=2014, max=2024, 
                  value=c(2014,2024), 
                  sep="")
```
The first argument is the inputId. We assign the inputId argument a variable name of our choosing. In this guide, we use “year”. This variable will store the user’s year range selection on the slider. The label is the title used for the slider input widget.

Then using the min and max arguments, we specify the smallest and largest numbers on the slider. The TTC delays in this dataset range from 2014 to 2024 so we use these numbers. Then we specify the initial or default range of the slider using the value argument. We want the default range to be the entire dataset so we specify the range of 2014 to 2024 inside the c() function. Finally, we use the sep argument to avoid adding a comma to these years (i.e., display 2014 instead of 2,014).

When you save your code and run the application now, you should see the first control widget.

<img src='{{ '/assets/images/2.%20R%20Shiny%20-%20Slider%20Year%20Range.png' | relative_url }}' alt='2. R Shiny - Slider Year Range' title='' width='711' height='420' />

Consecutive control widgets are separated by a comma so we add a comma before moving on to the next widget.

```r
      # SLIDER: YEAR RANGE
      sliderInput(inputId="year",
                  label="Range of Years:", 
                  min=2014, max=2024, 
                  value=c(2014,2024), 
                  sep=""),
```
 

### Radio Button: Y-Axis and X-Axis of the graph

In the main panel, we will have a line graph showing the trend in TTC delays over different time variables. In the next two widgets, we want to give the user control over the variables used on the y-axis and the x-axis using radio buttons. The y-axis will contain the time delay variable and the x-axis will contain the time period variable to plot.

Let’s start with the y-axis. To create radio buttons, we use the radioButtons() function.

```r
      # RADIO BUTTON: Y-AXIS
      radioButtons(inputId = "yvariable",
                   label = "Y-Axis: Delay Variable to Plot:",
                   choices = list(
                     "Total Hours of Delay" = "totalhours",
                     "Average Minutes of Delay" = "averagemin"))
```
Inside the function, we specify the inputId as “yvariable” to store the selection of the user. We add the widget title “Y-Axis: Delay Variable to Plot:”.

We will give the user two choices: total hours of delay or average minutes of delay. The default selection will be the first one. We specify the choices as a list using the choices argument. We use the list() function to provide the list of choices. Inside the list() function, for each choice, we provide the choice label, the equal to symbol, and the choice value. The label of the choice is how it is displayed on the application page. The choice value is the identifier for each choice and it will be used later in the server function code.

<img src='{{ '/assets/images/3.%20R%20Shiny%20-%20Radio%20Button%20Y-Axis.png' | relative_url }}' alt='3. R Shiny - Radio Button Y-Axis' title='' width='735' height='512' />

We do the same for the x-axis. For the x-axis, we give the user four choices between year, month, day of the month and hour of the day. Do not forget to add a comma after the y-axis widget before including the code for the x-axis.

```r
      # RADIO BUTTON: X-AXIS
      radioButtons(inputId = "xvariable",
                   label = "X-Axis: Time Variable to Plot:",
                   choices = list(
                     "Year" = "year",
                     "Month" = "month",
                     "Day of the Month" = "day",
                     "Hour of the Day" = "hourofday"))
```
Now, when you save and run the application code, you should see three control widgets at this stage.

<img src='{{ '/assets/images/4.%20R%20Shiny%20-%20Radio%20Button%20X-Axis.png' | relative_url }}' alt='4. R Shiny - Radio Button X-Axis' title='' width='724' height='507' />

  
 

### Checkbox: Delay Codes

In addition to a graph, our application will display an optional data table in the main panel. We want to give the user the option to display the data table by checking off a checkbox widget. The checkbox widget does not allow us to add a title so we will add a title first using the h5() function. To get a boldfaced title, we use the strong() function inside the h5() function. Then we add the title “Show Most Common Delay Codes:”.

```r
      # CHECKBOX: DELAY CODES
      h5(strong("Show Most Common Delay Codes:"))
```
To create a single checkbox, we can use the checkboxInput() function. We store the selection of the user in the variable “showcodes” using the inputId argument. We label the checkbox “Yes” so that the user can check it off to display the data table. And lastly, we set the default value of the checkbox as unchecked or FALSE. In other words, we do not want the data table to be displayed by default.

```r
      # CHECKBOX: DELAY CODES
      h5(strong("Show Most Common Delay Codes:")),
     checkboxInput(inputId="showcodes",
                         label="Yes",
                         value=FALSE)
```
<img src='{{ '/assets/images/5.%20R%20Shiny%20-%20Checkbox%20Delay%20Codes.png' | relative_url }}' alt='5. R Shiny - Checkbox Delay Codes' title='' width='790' height='590' />

  
 

### Dropdown Menu: Subway Station

Finally, we want to give the user the option to visualize TTC delays at specific subway stations. We will use a dropdown menu to select a station. To create a dropdown menu, we can use the selectInput() function. Similar to the other widgets, we specify the inputId and the label arguments.

Then we specify the choices inside the dropdown menu the same way we did for the radio button widget using the choices argument. The first choice is the default value of all subway stations. Then we add the full list of Toronto subway stations as the next choices. For each subway station, we add the choice label to be displayed on the application and the choice value to be used in the server function code.

```r
	# DROPDOWN MENU: SUBWAY STATION
     selectInput(inputId="subwaystation",
                  label="Select Specific Subway Station:",
                  choices = list(
                    "All Stations"="allstations",
                    "Kipling Station"="kipling", 
                    "Islington Station"="islington", 
                    "Royal York Station"="royalyork", 
                    "Old Mill Station"= "oldmill", 
                    "Jane Station" = "jane", 
                    "Runnymede Station" = "runnymede", 
                    "High Park Station" = "highpark", 
                    "Keele Station" = "keele", 
                    "Dundas West Station" = "dundaswest", 
                    "Lansdowne Station" = "lansdowne", 
                    "Dufferin Station" = "dufferin", 
                    "Ossington Station" = "ossington", 
                    "Christie Station" = "christie", 
                    "Bathurst Station" = "bathurst", 
                    "Spadina Station" = "spadina", 
                    "St George Station" = "stgeorge", 
                    "Bay Station" = "bay", 
                    "Bloor-Yonge Station" = "bloor", 
                    "Sherbourne Station" = "sherbourne", 
                    "Castle Frank Station" = "castlefrank", 
                    "Broadview Station" = "broadview", 
                    "Chester Station" = "chester", 
                    "Pape Station" = "pape", 
                    "Donlands Station" = "donlands", 
                    "Greenwood Station" = "greenwood", 
                    "Coxwell Station" = "coxwell", 
                    "Woodbine Station" = "woodbine", 
                    "Main Street Station" = "mainstreet", 
                    "Victoria Park Station" = "victoriapark", 
                    "Warden Station" = "warden", 
                    "Kennedy Station" = "kennedy", 
                    "Lawrence East Station" = "lawrenceeast", 
                    "Ellesmere Station" = "ellesmere", 
                    "Midland Station" = "midland", 
                    "Scarborough Centre Station" = "scarboroughcentre", 
                    "McCowan Station" = "mccowan",
                    "Vaughan MC Station" = "vaughanmc", 
                    "Highway 407 Station" = "highway407", 
                    "Pioneer Village Station" = "pioneervillage", 
                    "York University Station" = "yorkuniversity", 
                    "Finch West Station" = "finchwest", 
                    "Downsview Park Station" = "downsviewpark", 
                    "Sheppard West Station" = "sheppardwest", 
                    "Wilson Station" = "wilson", 
                    "Yorkdale Station" = "yorkdale", 
                    "Lawrence West Station" = "lawrencewest", 
                    "Glencairn Station" = "glencairn", 
                    "Eglinton West Station" = "eglintonwest", 
                    "St Clair West Station" = "stclairwest", 
                    "Dupont Station" = "dupont", 
                    "Museum Station" = "museum", 
                    "Queens Park Station" = "queenspark", 
                    "St Patrick Station" = "stpatrick", 
                    "Osgoode Station" = "osgoode", 
                    "St Andrew Station" = "standrew", 
                    "Union Station" = "union", 
                    "King Station" = "king", 
                    "Queen Station" = "queen", 
                    "Dundas Station" = "dundas", 
                    "College Station" = "college", 
                    "Wellesley Station" = "wellesley", 
                    "Rosedale Station" = "rosedale", 
                    "Summerhill Station" = "summerhill", 
                    "St Clair Station" = "stclair", 
                    "Davisville Station" = "davisville", 
                    "Eglinton Station" = "eglinton", 
                    "Lawrence Station" = "lawrence", 
                    "York Mills Station" = "yorkmills", 
                    "Sheppard Station" = "sheppard", 
                    "North York Centre" = "northyorkcentre", 
                    "Finch Station" = "finch", 
                    "Bayview Station" = "bayview", 
                    "Bessarion Station" = "bessarion", 
                    "Leslie Station" = "leslie", 
                    "Don Mills Station" = "donmills"))
```
<img src='{{ '/assets/images/6.%20R%20Shiny%20-%20Drowndown%20Menu%20Subway%20Stations.png' | relative_url }}' alt='6. R Shiny - Drowndown Menu Subway Stations' title='' width='804' height='672' />

  
 

### Text: Data Source

At the bottom of the list of control widgets, we want to add some text about the data source, the dataset, the author of the application and the link to this guide. We can simply add text in quotation marks without any function. However, if you are typing text over multiple lines in the R-script, you will need to add a comma at the end of each line before continuing on to the next line.

To add hyperlinks, you can use the a() function from the htmltools package. And to start a new paragraph in your text, you can add a break using the br() function from the htmltools package.

```r
      # TEXT: DATA SOURCE
      "Data collected from the City of Toronto's Open Data Portal:",
      htmltools::a("TTC Subway Delays Data.", href="https://open.toronto.ca/dataset/ttc-subway-delay-data/"),
      "It contains the TTC subway delays from January 2014 until July 2024.",
      htmltools::br(),
      "This app was developed by Nadia Muhe.",
      htmltools::br(),
      "The guide for this application can be found",
      htmltools::a("here.", href="https://mdl.library.utoronto.ca/technology/tutorials/introduction-r-shiny")

```
<img src='{{ '/assets/images/7.%20R%20Shiny%20-%20Text_0.png' | relative_url }}' alt='7. R Shiny - Text' title='' width='738' height='713' />

This concludes the code for the sidebar part of the user interface. We move on to the main panel.

 

### Main Panel: Graph Output & Data Table Output

Inside the main panel, we want to display a graph and a data table.

```r
    # GRAPH & TABLE
    mainPanel(
 
    )
```
To display our graph output first, we use the plotOutput() function and we give our graph the output identifier “graph”. Then to display the TTC delay codes data table output, we use the tableOutput() function and we give the table the output identifier “codestable”. We use a comma to separate the graph and the data table outputs. This code will display the graph first and the data table below it.

```r
    # GRAPH & TABLE
    mainPanel(
        plotOutput("graph"), 
        tableOutput("codestable")
    )
```
When you save and run the code at this stage, nothing shows up on the right-hand side of the application. We need to complete the code for the server function before we can see any output in the main panel.

<img src='{{ '/assets/images/8.%20R%20Shiny%20-%20Main%20Panel.png' | relative_url }}' alt='8. R Shiny - Main Panel' title='' width='739' height='714' />