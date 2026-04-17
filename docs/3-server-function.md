---
title: C. Server Function
parent: Introduction to R Shiny
layout: default
nav_order: 3
---

## C. Server Function

The server function is the second component of the R Shiny application. It contains the instructions needed to build the application or more specifically the output of the application based on the user’s input.

 

Input & Output Parameters
--------------------------

The server function contains two parameters: input and output. The input parameter is used to access the user’s input. We can access the user’s selections by using the inputId variables we specified in the sidebar user interface code. For example, to access user’s selection of years, we can use the following code:

```r
input$year
```
The output parameter is used to create the output elements. The output can be a data table, an image, a graph, text etc. Once the output object has been created in the server function, we store it in the output parameter using the outputId variable we specified in the main panel user interface code. For example, when we create our graph, we will store it in the following graph output:

```r
output$graph
```
 

Reactive Elements
-----------------

Reactive elements are Shiny functions or expressions that are sensitive to the input of the user. These expressions react and update the output whenever the user changes the input values.

We want to organize and break up the code in the server function into separate reactive elements to prevent unnecessary computation that can slow down our application. That way, when the user changes one input, instead of having to compute the entire code in the server function, only the particular reactive expression that uses that input will be computed again.

 

TTC Subway Delays: Server Function
----------------------------------

In our TTC subway delays application, the server function will import the TTC subway delays dataset, filter it based on the user’s input, create the graph output, and create the delay codes data table output.

```r
# 2. Server Function
server <- function(input, output){
} 
```
 

### Import Dataset

In the first step, we will import the TTC subway delays dataset. If you have not downloaded the dataset yet, you can download it [here](https://maps.library.utoronto.ca/workshops/RShiny/ttcdelays.php). If you store the dataset in the same folder as the application code, you do not need to specify the directory. To import the RDS data file, we use the readRDS() function and store the dataset as ttc.

```r
# 2. Server Function
server <- function(input, output){
	# IMPORT DATASET
	ttc<-readRDS("ttcdelays.rds")
} 
```
 

### Filter Dataset

In the second step, we will filter the dataset. To filter the dataset, we will create a reactive element using the reactive() function. We will store the reactive expression as ttcfiltered. Note that inside the parentheses of the reactive() function, we add a pair of curly brackets to create the reactive expression.

```r
	# FILTER DATASET
 	ttcfiltered <- reactive({
   	
 	})
```
Inside this reactive element, we will filter the ttc dataset. First, we will filter by the user’s year selection and then by the user’s subway station selection.

We subset the ttc dataset using the filter function from the dplyr package. We want the year variable to be greater than or equal to the user selected minimum year and less than or equal to the user selected maximum year. In other words, we only want to keep the TTC delays within or equal to the upper and lower bound of the user’s year range selection.

The user’s selection of the minimum and maximum year is stored in the input variable year. We use input$year to extract these values. This is a list of two numbers. For the minimum year selected, we use the first index and for the maximum year selected, we use the second index. We store the filtered dataset as ttc.

```r
# FILTER DATASET
 ttcfiltered <- reactive({
   
    # Filter by Year
    ttc <- ttc %>% filter(year>=input$year[1] & year<=input$year[2])
   
  })
```
Next, we filter by the subway station selected. The subway selection input is stored in input$subwaystation. We only want to filter by subway station if the user makes a selection. Otherwise, we want to leave the default of “allstations”. So, we use an if statement. Inside the parentheses, we add our condition: we only want to filter the dataset when the selection is not equal to “allstations”.

Then inside the curly brackets, we filter the ttc dataset by the subway station selected. The ttc dataset has an indicator variable for each station. When a delay took place at a particular station, the station variable has a value of one, otherwise, it has a value of zero. We will use the selected station variable to filter the dataset.

Inside the filter function, if we use input$subwaystation, the selected station will be added as a text value. But we want to use the station name as a variable to filter the dataset. To do this, we use two exclamation marks followed by the function as.name() and inside we add the selected subway station value input$subwaystation (i.e., !!as.name(input$subwaystation)). This step will insert the selected subway station as a variable instead of a text value. Then to select the delays at a particular station, we add the condition equals to 1. Lastly, we store the filtered dataset as ttc again.

```r
# FILTER DATASET
 ttcfiltered <- reactive({
   
    # Filter by Year
    ttc <- ttc %>% filter(year>=input$year[1] & year<=input$year[2])
   
    # Filter by Subway Station
   if(input$subwaystation!="allstations"){
      ttc <- ttc %>% filter(!!as.name(input$subwaystation)==1)
    }
   
  })
```
Finally, we use the return() function to return the filtered dataset ttc at the end of this reactive expression.

```r
# FILTER DATASET
 ttcfiltered <- reactive({
   
    # Filter by Year
    ttc <- ttc %>% filter(year>=input$year[1] & year<=input$year[2])
   
    # Filter by Subway Station
   if(input$subwaystation!="allstations"){
      ttc <- ttc %>% filter(!!as.name(input$subwaystation)==1)
    }
   
    return(ttc)
  })
```
 

### Graph Output

Now let’s we move on to the graph output. To make a graph output, we use the renderPlot() function. This function creates a reactive expression.

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({
   
  })
```
First, we prepare the data used for the graph. We take the filtered dataset reactive element ttcfiltered(), convert it to a data frame, and store it as ttcfiltered.

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({

    # Prepare Graph Data
    ttcfiltered <- as.data.frame(ttcfiltered())
   
  })
```
The user will have the option to plot the total hours of delay (default option) or the average minutes of delay on the y-axis. To prepare the data for the graph, we will use the filtered dataset to calculate these two delay variables. For now, we will not consider any input from the user. The default graph uses total hours of delay on the y-axis and year on the x-axis.

To calculate the two delay variables, we take the filtered dataset, group the delays by year and we calculate the delay variable per year using the summarize function. We calculate the total hours of delay as the sum of the delay minutes divided by 60. And we calculate the average minutes of delay as the mean of delay minutes. We store these two variables as totalhours and averagemin respectively. Note that we use these same variable names as inputId values for the y-axis selection in the user interface code. Lastly, we store this dataset as ttcgraphdata. 

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({
    # Prepare Graph Data
    ttcfiltered <- as.data.frame(ttcfiltered())
    ttcgraphdata <- ttcfiltered %>% 
      group_by(year) %>% 
      summarize(totalhours = sum(delayminutes, na.rm = TRUE)/60, 
                averagemin = mean(delayminutes, na.rm = TRUE))
   
  })
```
The graph we want to make is a line graph. We will use the ggplot2 functions to create our graph. First, we enter the dataset name ttcgraphdata, the year variable for the x-axis and the totalhours variable for the y-axis. To make a line graph, we use the geom_line() function and we give the line graph the colour yellow and a thickness of 1 unit. Lastly, we apply the black and white theme using the theme_bw() function and we also increase the overall font size of the graph to 15 using the argument base_size.

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({
    # Prepare graph data
    ttcfiltered <- as.data.frame(ttcfiltered())
    ttcgraphdata <- ttcfiltered %>% 
      group_by(year) %>% 
      summarize(totalhours = sum(delayminutes, na.rm = TRUE)/60, 
                averagemin = mean(delayminutes, na.rm = TRUE))
    # Graph
    ggplot(ttcgraphdata, aes(x=year, 
                  y=totalhours)) + 
    geom_line(color="yellow3", linewidth=1) + 
    theme_bw(base_size=15) 
   
  }
```
Once we save and run this code, it gives us a plot in the main panel. When you play around with the year range and the subway station selection, you should see the graph updated.

<img src='{{ '/assets/images/9.%20R%20Shiny%20-%20Line%20Graph.png' | relative_url }}' alt='9. R Shiny - Line Graph' title='' width='861' height='654' />

When you change the values of the radio buttons for the X and the Y axes, there are no updates to the graph because we have not included the user’s input in our code yet. To include the user’s input on the graph variables, we need to make the following three changes to our graph output code.

First, in preparing the graph data, we want to group the ttcfiltered dataset by the user’s input rather than just year. The user’s input for the x-axis is stored in input$xvariable. We need use two exclamation marks along with the as.name() function on input$xvariable to include the xvariable input as a variable rather than a text value. So, we replace year by !!as.name(input$xvariable).

Next, we will replace the x-axis and y-axis variables in the ggplot() function with the user’s input. The user’s input for the x-axis is stored in input$xvariable and the user’s input for the y-axis is stored in input$yvariable. Again here, we will use the !!as.name() function with both inputs to insert them as variable names.

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({
    # Prepare graph data
    ttcfiltered <- as.data.frame(ttcfiltered())
    ttcgraphdata <- ttcfiltered %>% 
      group_by(!!as.name(input$xvariable)) %>% 
      summarize(totalhours = sum(delayminutes, na.rm = TRUE)/60, 
                averagemin = mean(delayminutes, na.rm = TRUE))
    # Graph
    ggplot(ttcgraphdata, aes(x=!!as.name(input$xvariable), 
                 y=!!as.name(input$yvariable))) + 
    geom_line(color="yellow3", linewidth=1) + 
    theme_bw(base_size=15) 
   
  })
```
Once you save and run the application, you will see that you can modify all of the widgets except for the checkbox and the graph output will be updated. The image below shows the graph of average minutes of delay by hour of the day.

<img src='{{ '/assets/images/10.%20R%20Shiny%20-%20Line%20Graph%20-%20Variable%20Widget.png' | relative_url }}' alt='10. R Shiny - Line Graph - Variable Widget' title='' width='858' height='651' />

There are few things we still want to improve on this graph. Let’s start with the label of the axes.

To modify the label of the y-axis, we will create a variable called ylabel. And in the first step, we will assign it the value of “Total Hours of Delay” since the totalhours variable is the default option for the y-axis. Then we use an if statement to check if the user selection or input$yvariable has changed to averagemin. When this condition is true, we will change the value of ylabel to “Average Minutes of Delay” inside the curly brackets.

We do the same thing for the x-axis. We first create a variable called xlabel with the value of “Year”. Then we use if and else if statements to modify xlabel based on the user’s input for the x-axis.

To apply these labels to the graph, in the ggplot section, we add the ylab() and the xlab() functions. Inside each of these functions, we include the labels ylabel and xlabel respectively.

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({
    # Prepare graph data
    ttcfiltered <- as.data.frame(ttcfiltered())
    ttcgraphdata <- ttcfiltered %>% 
      group_by(!!as.name(input$xvariable)) %>% 
      summarize(totalhours = sum(delayminutes, na.rm = TRUE)/60, 
                averagemin = mean(delayminutes, na.rm = TRUE))
   
    # Y-Axis Label
    ylabel <- "Total Hours of Delay"
   if(input$yvariable=="averagemin"){
      ylabel <- "Average Minutes of Delay"
    }
    # X-Axis Label
    xlabel <- "Year"
    if(input$xvariable=="month"){
      xlabel <- "Month"
    } else if(input$xvariable=="day"){
      xlabel <- "Day of the Week"
    } else if(input$xvariable=="hourofday"){
      xlabel <- "Hour of the Day"
    }
    # Graph
    ggplot(ttcgraphdata, aes(x=!!as.name(input$xvariable), 
                 y=!!as.name(input$yvariable))) + 
    geom_line(color="yellow3", linewidth=1) + 
    theme_bw(base_size=15) + 
    ylab(ylabel) + xlab(xlabel)
    
  })
```
<img src='{{ '/assets/images/11.%20R%20Shiny%20-%20Line%20Graph%20-%20Axes%20Labels.png' | relative_url }}' alt='11. R Shiny - Line Graph - Axes Labels' title='' width='871' height='661' />

We also want to modify the title of the graph. To add a title, we use the ggtitle() function. We want the title to be based on the variables selected for the y-axis and the x-axis so we will use the ylabel and xlabel variables. We use the paste0() function inside ggtitle() to combine variables and text to make our final title. We start the title with the label of the y-axis, the term “by”, followed by the label of the x-axis. For example, the default title will be “Total Hours of Delay by Year”.

Then at the end of the title, we will add the size of the filtered TTC subway delays dataset we are using in parentheses. We will use the lowercase letter n to represent the sample size. And to calculate the size of the ttcfiltered dataset, we use the nrow() function. This will give us a default title of “Total Hours of Delay by Year (n=210214)”.

```r
    # Graph
    ggplot(ttcgraphdata, aes(x=!!as.name(input$xvariable), 
                 y=!!as.name(input$yvariable))) + 
    geom_line(color="yellow3", linewidth=1) + 
    theme_bw(base_size=15) + 
    ylab(ylabel) + xlab(xlabel) + 
    ggtitle(paste0(ylabel, " by ", xlabel, " (n=", nrow(ttcfiltered), ")")) 
```
<img src='{{ '/assets/images/12.%20R%20Shiny%20-%20Line%20Graph%20-%20Title.png' | relative_url }}' alt='12. R Shiny - Line Graph - Title' title='' width='871' height='644' />

The final improvement we want to make on this graph is the x-axis tick marks. We want to have tick marks for each integer of the time variable selected. To achieve this, first we will store the minimum and the maximum value of the time variable selected for the x-axis.

We start with the ttcgraphdata and we use the summarize() function to calculate the minimum value of input$xvariable. We use !!as.name() to insert the user’s input as a variable. The output from the summarize function is a data frame that contains the minimum value. We will convert it to a number using the as.numeric() function. Then we store this number as xmin. We do the same thing for the maximum value of the time variable and store it as xmax.

To modify the tick marks on the x-axis, in the ggplot section, we use the breaks argument inside the scale_x_continuous() function. We can specify the exact sequence of numbers we want tick marks for using the seq() function. Inside this seq() function, we specify the minimum value as xmin, the maximum value as xmax and the increment in the sequence to be equal to 1.

```r
  # GRAPH OUTPUT
  output$graph<-renderPlot({
    # Prepare graph data
    ttcfiltered <- as.data.frame(ttcfiltered())
    ttcgraphdata <- ttcfiltered %>% 
      group_by(!!as.name(input$xvariable)) %>% 
      summarize(totalhours = sum(delayminutes, na.rm = TRUE)/60, 
                averagemin = mean(delayminutes, na.rm = TRUE))
   
    # Y-Axis Label
    ylabel <- "Total Hours of Delay"
   if(input$yvariable=="averagemin"){
      ylabel <- "Average Minutes of Delay"
    }
    # X-Axis Label
    xlabel <- "Year"
    if(input$xvariable=="month"){
      xlabel <- "Month"
    } else if(input$xvariable=="day"){
      xlabel <- "Day of the Week"
    } else if(input$xvariable=="hourofday"){
      xlabel <- "Hour of the Day"
    }
    # X-Axis Tick Marks
    xmin <- ttcgraphdata %>% summarize(min(!!as.name(input$xvariable))) %>% as.numeric()
    xmax <- ttcgraphdata %>% summarize(max(!!as.name(input$xvariable))) %>% as.numeric()
    # Graph
    ggplot(ttcgraphdata, aes(x=!!as.name(input$xvariable), 
                 y=!!as.name(input$yvariable))) + 
    geom_line(color="yellow3", linewidth=1) + 
    theme_bw(base_size=15) + 
    ylab(ylabel) + xlab(xlabel) + 
    ggtitle(paste0(ylabel, " by ", xlabel, " (n=", nrow(ttcfiltered), ")")) +
    scale_x_continuous(breaks=seq(xmin, xmax, 1))
   
  })
```
When you save and run the application code, you will see that for any of the time variables, the plot will show a tick mark for all of the possible integer values. We have completed the code for the graph output!

<img src='{{ '/assets/images/13.%20R%20Shiny%20-%20Line%20Graph%20-%20Tick%20Marks.png' | relative_url }}' alt='13. R Shiny - Line Graph - Tick Marks' title='' width='882' height='646' /> 

### Data Table Output

Next, we will build the code to generate the delay codes data table. For the specific time period and location selected by the user, we want to display a data table of the ten most common TTC delay codes.

To create a table output, we use the renderTable() function. We will store the table as output$codestable. The variable “codestable” is the output identifier we specified in the main panel user interface code.

```r
  # DELAY CODES DATA TABLE OUTPUT
  output$codestable<-renderTable({
 })
```
We only want to display this table if the user requests it by checking off the show delay codes checkbox. The identifier for this checkbox is “showcodes” so we will use input$showcodes to check if the checkbox has been checked off (TRUE) or not (FALSE). We use an if statement to check if input$showcodes is true.

```r
  # DELAY CODES DATA TABLE OUTPUT
  output$codestable<-renderTable({
  	if(input$showcodes==TRUE){
  	}
  })
```
When the condition is true, we want to take the filtered dataset from the ttcfiltered() reactive element, convert it to a data frame, group it by delay codes and then count the frequency of each code using the summarize() function. Then we sort the dataset by the frequency variable to get the most frequency codes at the top and the least frequent ones at the bottom. We filter this dataset to extract the first ten rows or the ten most common delay codes using row_number(). This entire process will give us a data table with the top ten delay codes and their count. Finally, we rename these two columns, code and count, using the rename() function as “Most Common Delay Codes” and “Delay Count”.

```r
  # DELAY CODES DATA TABLE OUTPUT
  output$codestable<-renderTable({
    if(input$showcodes==TRUE){
      as.data.frame(ttcfiltered()) %>%
        group_by(code) %>%
        summarize(count = n()) %>%
        arrange(desc(count)) %>%
        filter(row_number()<=10) %>% 
        rename("Most Common Delay Codes"="code", "Delay Count"="count")
    }
    
  })
```
Save and run the application code. Now when you check off the show delay codes checkbox, you will see a data table of the top ten most common TTC delay codes below the line graph.

<img src='{{ '/assets/images/14.%20R%20Shiny%20-%20Delay%20Codes%20Data%20Table.png' | relative_url }}' alt='14. R Shiny - Delay Codes Data Table' title='' width='871' height='653' />