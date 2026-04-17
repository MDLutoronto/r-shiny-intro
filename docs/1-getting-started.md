---
title: A. Getting Started
parent: Introduction to R Shiny
layout: default
nav_order: 1
---

## A. Getting Started

This section will discuss installing the necessary R packages, the dataset used for this guide, the basic code structure of R Shiny applications and publishing R Shiny applications on GitHub.

 

Install Package
---------------

To create a R Shiny application, you will need to install the shiny package. In addition to the shiny package, in this guide, we will also install the dplyr package to manipulate the dataset and the ggplot2 package to create a graph.

```r
# Packages
install.packages(c(“shiny”, “dplyr”, “ggplot2”))
library(shiny)
library(dplyr)
library(ggplot2)
```
 

Dataset: TTC Subway Delays
--------------------------

The dataset that we will be using for this guide is the TTC subway delays dataset. You can download the dataset [here](https://maps.library.utoronto.ca/workshops/RShiny/ttcdelays.php). It is in RDS format (the R data file format).

This dataset contains the TTC subway delays from January 2014 until August 2024. The variables of interest for this guide are the date and location of the delay, the length of the delay, and the delay code or cause.

The goal of this application is to explore the trend in TTC subway delays at different time periods, different locations and what the most common causes for these delays are.

 

Basic Code Structure
--------------------

The R Shiny application has three components: the user interface, the server function and combining the two to create the application object.

We start by creating the user interface object or UI. The most common function that is used to create the UI is the fluidPage() function. This function creates a blank page for the application. Then, inside this page, we will add a title. We can add a title using the titlePanel() function. We add our title inside the parentheses in quotation marks. We will add the title *Toronto TTC Subway Delays*.

The second component is the definition of the server function. The server function takes two parameters input and output that we will include in the parentheses. Then we add a pair of curly brackets. Later in this guide, we will add the R code to execute the function of this application inside the curly brackets.

The last step is to create the Shiny application object. We can do this by using the shinyApp() function and specifying the user interface object and the server function inside the parentheses.

```r
 # 1. User Interface
ui <- fluidPage(
titlePanel(“Toronto TTC Subway Delays”)
)
# 2. Server Function
server <- function(input, output){
} 
# 3. Create the Shiny Application
shinyApp(ui, server)
```
Once you have typed the three components, save this R script. In this guide, we save the R script as *app.R*.

 

Running the Application
-----------------------

To run the application on your machine, you can either

1. highlight and run the entire code or
2. click on the “Run App” icon on the RStudio toolbar.

When you run the code at this stage, you should see a new dialog pop open with a blank page and the title *Toronto TTC Subway Delays.*

<img src='{{ '/assets/images/1.%20R%20Shiny%20-%20Blank%20Page%20with%20Title%20.png' | relative_url }}' alt='R Shiny - Blank Page with Title ' title='' width='687' height='525' />

  
 

Separate UI and Server Function Codes
-------------------------------------

It is also possible to write the R shiny application code over two R scripts instead of one. This would require you to have one R script called *ui.R* with the user interface code and another R script called *server.R* with the server function code. You need to save both files in the same folder. You will not need a third component when you type your application code this way. To run the application, you click on the “Run App” icon on the RStudio toolbar.

 

Deploying the Application on GitHub
-----------------------------------

Once you have finalized the code for your application, one way to deploy the application on GitHub is to use the shinyapps.io website: <https://www.shinyapps.io/>. You can use their free account to deploy up to 5 applications for a total of 25 active hours per month. For more advanced service, you need to pay a subscription fee.

There are three steps involved in deploying the application. First, you install and load the rsconnect package.

```r
# Step 1
install.packages("rsconnect")
library(rsconnect)
```
Then you connect to your GitHub account from within RStudio. To do this, you need your GitHub username, a token and a secret code. You can get this entire line of code from the shinyapps.io website by logging in with your GitHub account and going to the dashboard: [https://www.shinyapps.io/admin/#/dashboard](https://www.shinyapps.io/admin/#/dashboard).

```r
# Step 2
rsconnect::setAccountInfo(name='githubusername', token='tokencode', secret=secretcode')
```
In the final step, you deploy the application by specifying the file path to the folder where you have saved the R Shiny application code and any files used within that code. For example, in this guide, the *app.R* R script is saved in a folder called ttcdelays.

```r
# Step 3
rsconnect::deployApp('H:/Documents/R Shiny/ttcdelays')
```
Once you run this final step, you will find the URL where your shiny application has been deployed to in the console window. Note that the shiny application URL contains the name of the folder where you have saved your application code. In this guide, the shiny application was deployed to the following site: <https://MDLutoronto.shinyapps.io/ttcdelays/>.
