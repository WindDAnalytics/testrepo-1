***
######README
#####Modified by Nithya D , Thomas D , Juan
### Taken from pages 49-50 of O'Neil and Schutt
##Assignment 6 Date:6/15
***
---
http://www1.nyc.gov/site/finance/taxes/property-rolling-sales-data.page

library(plyr)
setwd("/testrepo/testrepo")


***
<<<<<<< HEAD
 Check the data
```{r echo = FALSE}
=======
## Check the data
```{r echo = TRUE}
>>>>>>> 3c75f971e3238bd56eb668a7b606aefe96b26868
head(man)
summary(man)
str(man) # Very handy function!
#Compactly display the internal structure of an R object.
```
***
Clean/format the data with regular expressions
More on these later. For now, know that the
pattern "[^[:digit:]]" refers to members of the variable name that
start with digits. We use the gsub command to replace them with a blank space.
We create a new variable that is a "clean' version of sale.price.
And sale.price.n is numeric, not a factor.
```{r echo = FALSE}
man$SALE.PRICE.N <- as.numeric(gsub("[^[:digit:]]","", man$SALE.PRICE))
count(is.na(man$SALE.PRICE.N))

names(man) <- tolower(names(man)) # make all variable names lower case
```
Removed the 1000 delimiter from the sqft and converted the datatype to numeric for year
```{r echo = FALSE}
man$gross.sqft <- as.numeric(gsub("[^[:digit:]]","", man$gross.square.feet))
man$land.sqft <- as.numeric(gsub("[^[:digit:]]","", man$land.square.feet))
man$year.built <- as.numeric(as.character(man$year.built))
***
```
do a bit of exploration to make sure there's not anything
weird going on with sale prices

```{r echo = FALSE}
attach(man)
hist(sale.price.n) 
detach(man)
```
***
Here's some cool Plots:
![](https://github.com/WindDAnalytics/testrepo-1/blob/master/Analysis/man.sale.price.png) 
![](https://github.com/WindDAnalytics/testrepo-1/blob/master/Analysis/Orig_ActualSales_Scatterplot.png)

```{r echo = FALSE}
keep only the actual sales
man.sale <- man[man$sale.price.n!=0,]
```


for now, let's look at 1-, 2-, and 3-family homes


#Choose a time series:
```{r echo = FALSE}
selectInput("man", "", c("gross.square.feet","land.square.feet"))
```
See a plot:
```{r echo = FALSE}
renderPlot({plot(get(input$man))})
```
***
More Cleaning and Polishing
```{r echo = FALSE}

man.homes <- man.sale[which(grepl("FAMILY",man.sale$building.class.category)),]
dim(man.homes)
plot(log10(man.homes$gross.sqft),log10(man.homes$sale.price.n))
summary(man.homes[which(man.homes$sale.price.n<100000),])
```
Removed outliers that seem like they weren't actual sales
```{r echo = FALSE}
man.homes$outliers <- (log10(man.homes$sale.price.n) <=5) + 0
man.homes <- man.homes[which(man.homes$outliers==0),]
plot(log10(man.homes$gross.sqft),log10(man.homes$sale.price.n))
```
