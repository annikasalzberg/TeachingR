---
title: "Graphing - ggplot basics"
author: "Annika Salzberg & Casey Hale"
date: "2/20/2019"
output:
  html_document:
    toc: yes
    toc_float: yes
  pdf_document:
    toc: yes
---

```{r , echo = FALSE, warning=FALSE, message=FALSE}
library(ggplot2)
library(HistData)
library(survival)
library(cowplot)
```

## Introduction
In this file, we're going to go over the basics of the ggplot2 package! Many of you may have used this package before, but think of this as an overview of all the most important graphs in the package and how to change their aesthetics to make them as beautiful and publication-ready as possible!
We'll be using several sample datasets included in the HistData package, just for ease of access. They're simple and easy to understand, so they'll be good for getting the hang of ggplot structure.

### Quick notes first:
###### Here are a bunch of useful functions to call to find out more about your data:

```{r, eval=FALSE}
mtcars  # This is a random preset dataframe
dim(mtcars) # Gives you dataframe dimensions! (rows and columns)
summary(mtcars)  # lots of info about each column like mean, median, quartiles...
names(mtcars)  # names of all the columns
str(mtcars)  # name of column, type of data in it, then some examples of the data
mean(mtcars$mpg) # mean of the particular column you call with "$"
```


# Let's Get Plotting!
***
#### Basic Scatterplot
This is just a quick example of what it looks like to make a scatterplot using base R: 
```{r, warning=FALSE}
plot(Prostitutes$Year, Prostitutes$count, main="Prostitutes in Paris", xlab="Years ", ylab="# of Prostitutes")
abline(lm(Prostitutes$count~Prostitutes$Year))
```

***
This is fine, but leaves a lot to be desired. If you're just looking to make a quick graph to get an idea of what your data looks like, it serves its purpose - but there are so many more options for graphs that you want to publish!



####  ggplot2: a better way to graph 
geom_line
geom_bar
geom_histogram
geom_point
geom_vline (vertical line)
geom_hline (horizontal line)
geom_abline (angled line)
geom_map, geom_boxplot, geom_polygon, geom_text, etc. etc.!

There are a ton of these, but we'll just go over a few of the most useful ones.

*****************************************************************************
*****************************************************************************

# Scatterplots
_Using geom_point_ 

#### Here's a basic graph we can start with:
```{r}
ggplot(Prostitutes,aes(x=Year, y=count, color=month)) + 
  geom_point() +
  geom_smooth(method = "lm", color="black", se = FALSE)
```
   
   This is the exact same graph we made with base r, but it already looks better!   It's pretty simple to understand: we've taken the base ggplot code that specifies what data we're using, and added geom_point() to specify we want a scatterplot. We then added a linear model line, colored it black, and specified that we don't want them to include the region of error around the line that is automatically included (with se=FALSE). We could have kept that region of error in, but I took it out just for the sake of replicating our earlier graph.      
  Now let's add some color!

```{r}
ggplot(Prostitutes,
       aes(x=Year, y=count, color=month)) + 
  geom_point() 
```

But the months are displaying in the wrong order! There are two ways to fix this. Here's the first:
```{r}
Prostitutes$month <- factor(Prostitutes$month, 
  levels = c("Jan", "Feb", "Mar", "Apr", "May", "Jun",
             "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"))
```

This is a manual fix for when you can't automatically change the levels of a column. We learned last week that we can use the arrange() function for this:
```{r, eval=FALSE}
rearrangedVector <- arrange(data, variable1, variable2, variable3)
```
But if our levels are characters that the program doesn't automatically recognize how to order, we have to do it the manual way.

### More color changes
If I want to change the colors another way, I can actually create a vector made up of colors, and insert that in any ggplot code when color is applicable! Doing this is as simple as adding the following line of code:
```{r, eval=FALSE}
scale_fill_manual(values=c("red", "blue", "green"))
```
But a caveat of this is you have to make sure there are enough colors in your new vector for ggplot to use! For example, if your data has three categories that you want different colors for, but you only give it two colors, it won't work.  

Next we're going to change some elements. Another thing we can do is assign the basic graph to a simple name, so we don't have to keep typing the whole thing!
```{r}
prostituteBasic <- ggplot(Prostitutes,
       aes(x=Year, y=count, color=month)) + geom_point()
```


### Add labels!
Don't like the automatic labels applied by R? Just change them!
```{r}
prostituteBasic + 
  ylab('Number of Prostitutes') +
  xlab('Years') +
  ggtitle('Prostitute Population of Paris')
```

**************************************************************************

# Line graphs
_geom_line and geom_point_

Line graphs are simple - they're basically just scatterplots with geom_line added onto them! We won't spend much time on these, because much of what applies to scatter plots applies to them as well.
```{r}
ggplot(Prostitutes,
       aes(x=Year, y=count, color=month, shape=month)) + 
  geom_point(size=2) +
  geom_line(size=1) +
  scale_shape_manual(values=c(1,20,3,4,5,6,7,8,9,10,11,12)) 
```

A cool new thing we've done with this graph (though it's a bit crowded to see it) is to manually map different shapes onto the points! Check out all the options for points (with their requisite call numbers placed above them):

```{r, echo=FALSE}
# Ignore this code, it's just to display the points!!
d <- data.frame(p=c(0:25,32:127))
ggplot() +
  scale_y_continuous(name="") +
  scale_x_continuous(name="") +
  scale_shape_identity() +
  geom_point(data=d, mapping=aes(x=p%%16, y=p%/%16, shape=p), size=5, fill="red") +
  geom_text(data=d, mapping=aes(x=p%%16, y=p%/%16+0.25, label=p), size=3)
```

**************************************************************************

# Bar graphs
_geom_bar()_

We're going to use a new dataframe for this one! This dataframe is of passengers on the Titanic.
```{r, echo=FALSE}
tableTitanic <- as.data.frame(Titanic)
```

Very basic bar graph to start out with:
```{r}
ggplot(data=tableTitanic, aes(x=Sex, y=Freq)) +
    geom_bar(stat="identity")
```

The stat="identity" part of this is very important, but we'll get to that later. Right now, let's make this graph nicer! Let's map the class of passenger to different fill colors:
```{r}
ggplot(data=tableTitanic, aes(x=Sex, y=Freq, fill=Class)) +
    geom_bar(stat="identity", 
             position=position_dodge())
```

Now how about we map colors by age, but ALSO make the labels a bit nicer?
```{r}
ggplot(data=tableTitanic, aes(x=Sex, y=Freq, fill=Age)) +
    geom_bar(stat="identity", 
             position=position_dodge()) + 
  scale_x_discrete(limits=c("Male", 
                            "Female"), 
                             labels=c("Men\n(n=1731)", 
                                      "Women\n(n=470)"))
```

Here's how I got those n values, by the way - though there are many ways to do so:
```{r}
# First I made two new dataframes, subsetting to include only male and only female in each.
TitanicF <- tableTitanic[tableTitanic$Sex == c("Female"),]
TitanicM <- tableTitanic[tableTitanic$Sex == c("Male"),]
```
```{r}
# Then I just summed all the values in the Freq column!
sum(TitanicF$Freq)
sum(TitanicM$Freq)
```

## Adding standard error bars
This might not make much sense for this dataframe, but it's a very important thing to know how to add to a graph:
```{r}
ggplot(data=tableTitanic, aes(x=Sex, y=Freq)) +
    geom_bar(stat="summary", 
             position=position_dodge(), fun.y="mean") + 
  scale_x_discrete(limits=c("Male", "Female"), labels=c("Men\n(n=1731)", "Women\n(n=470)")) +
  geom_errorbar(stat="summary", fun.data="mean_se", width=0.2)
```

This code looks a little messy, but the important parts we added were the "fun.y="mean"" part within the geom_bar() parentheses, and the whole section added on in geom_errorbar. This gives us lines representing the standard error on each of our bars.


## stat="identity" and stat="bin"

+ bin is the default, makes the bar represent the count of that number (like a histogram)
+ identity is when you want the bar to represent the VALUE in the row/column
```{r, warning=FALSE}
# Example 1:
binGraph <- ggplot(ChestSizes, aes(x=chest)) + 
  geom_bar(stat="bin", binwidth = 10)

# Example 2:
identityGraph <- ggplot(ChestSizes, aes(x=chest, y=count)) + 
  geom_bar(stat="identity")
```
```{r, echo=FALSE}
#theme_set(theme_gray())
cowplot::plot_grid(binGraph, identityGraph, labels = c("Example 1", "Example 2"))
```
However, the "bin" functionality is being phased out of ggplot - and you should really use the geom_histogram function instead if you want to graph count data! The point of the above is to show why it's important that you take note of which values your graph is actually showing.

**************************************************************************
# Histograms
_geom_histogram_

```{r, echo=FALSE}
riverData <- as.data.frame(rivers)
```

Once again, we start with a basic graph (with another new dataset):
```{r}
ggplot(riverData, aes(x=rivers)) + 
  geom_histogram(binwidth=100)
```

This doesn't give us much information, though! It's very unclear what this data is showing. Let's change some aesthetics to fix that...
```{r}
ggplot(riverData, aes(x=rivers)) + 
  geom_histogram(binwidth=100,
                 color = "black",
                 fill  = "blue", alpha=.2) +
  ggtitle('Lengths of Major US Rivers') +
  xlab('Length of Rivers (miles)') +
  ylab('Number of Rivers') +
  geom_vline(aes(xintercept=mean(rivers, na.rm=T)),
               color="red", linetype="longdash", size=.5)
```

We did a lot at once here, but let's break it down. We changed all the labels, for starters, then we added a vertical red line to mark the mean value. We also changed the colors of the bars, and used "alpha" to make them more transparent!


**************************************************************************
# Density graphs

Our last graph type! Let's again begin with a basic graph:
```{r}
ggplot(msleep, aes(x=sleep_total, fill = vore)) +
  geom_density()
```

Weird grey section? That's the NAs in the vore column! Remove them and graph again...
```{r}
# Remove NA values:
msleep <- msleep[!is.na(msleep$vore), ]

# Graph the newly cleaned dataframe:
ggplot(msleep, aes(sleep_total, fill = vore)) +
  geom_density()
```

### Beautifying the graph...
There are some problems with this graph! First of all, we can't see some of the lines because they're being blocked by others. Let's fix that, have some custom colors, and rename those categories of animal diets to look nice when labeled on the graph.
```{r}
ggplot(msleep, aes(sleep_total, fill = vore)) +
  geom_density(alpha = 0.5) + 
  xlim(0, 24) +
  scale_fill_manual(values=c("#9a0000", "#69ff69", 
                             "#6eb8f0", "#893bff"),
                    name="Animal Diet",
                    breaks=c("carni", "herbi", "insecti", "omni"),
                    labels=c("Carnivore", "Herbivore",
                                "Insectivore", "Omnivore"))
```

If we want to permanently change the names of the levels within the "vore" column, we can do so like this: 
```{r}
levels(msleep$vore)[levels(msleep$vore)=="carni"] <- "Carnivore"
levels(msleep$vore)[levels(msleep$vore)=="herbi"] <- "Herbivore"
levels(msleep$vore)[levels(msleep$vore)=="insecti"] <- "Insectivore"
levels(msleep$vore)[levels(msleep$vore)=="omni"] <- "Omnivore"
```

Graph again... but this time, we have fewer add-ons because we don't need to clean up the labels as much in the graph code itself!

```{r}
ggplot(msleep, aes(sleep_total, fill = vore)) +
  geom_density(alpha = 0.5) + 
  xlim(0, 24) +
  scale_fill_manual(values=c("#9a0000", "#69ff69", 
                             "#6eb8f0", "#893bff"),
                    name="Animal Diet")
```

Now for a final touch, let's change all the rest of the labels to look nice:
```{r}
ggplot(msleep, aes(sleep_total, fill = vore)) +
  geom_density(alpha = 0.5) + 
  xlim(0, 24) +
  ggtitle("Sleep Patterns of Animals with Differing Diets") +
  xlab('Total Sleep Daily (hours)') +
  ylab('Frequency') +
  scale_fill_manual(values=c("#9a0000", "#69ff69", 
                             "#6eb8f0", "#893bff"),
                    name="Animal Diet")
```

### Facet grid
What if we want to split apart our variables to emphasize each one a bit more? We can make each individual line a different graph with the facet_grid function:
```{r}
ggplot(msleep, aes(sleep_total, fill = vore)) +
  geom_density(alpha = 0.5) + 
  xlim(0, 24) +
  guides(fill=guide_legend()) +
  ggtitle("Sleep Patterns of Animals with Differing Diets") +
  xlab('Total Sleep Daily (hours)') +
  ylab('Frequency') +
  scale_fill_manual(values=c("#9a0000", "#69ff69", 
                             "#6eb8f0", "#893bff"),
                    name="Animal Diet") +
  facet_grid(vore ~ .)
```

The facet_grid function puts your graphs side by side! If I'd written facet_grid(. ~ vore), it would have put them vertically. This is definitely a function you can play around with!
   
You can make a grid of your values in many different ways, not just with facet_grid. There are whole packages dedicated to it! (One fun one is called cowplot, which you might have noticed I used to display some of the graphs in this file)   

***   
### Other lines you can add
We already used a few in previous code, but a couple more are listed here:
+ geom_vline _makes a vertical line at a specified point_
+ geom_hline _makes a horizontal line at a specified point_
+ geom_abline _makes a sloped line given slope and intercept values_


A cheat-sheet resource for a lot of this (and more!) can be found at https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf



**************************************************************************
**************************************************************************
**************************************************************************
##### If there's time...
###### Some useful things
library(plyr)

###### Dealing with NA values

To entirely remove rows with NAs in them for a certain column:
```{r, eval=FALSE}
happyData <- sadData[!is.na(sadData$columnname), ]
```

If your NA values should actually be zeros:
```{r, eval=FALSE}
happyData$column1name[is.na(happyData$column1name)] = 0
```

***
#### Another SUPER useful function when graphing or manipulating data: ddply

__Split -> Apply -> Combine__
          
This function splits large data frames into smaller ones -> you can:
          + split by a variable, or a combination of variables.
          + apply same function to each one
          + re-build the results into new data frame

^ and all of that happens with function ddply!
   
Example:   
>
NewData <- ddply(originalData, .(variableToSplitBy), 
                 summarize,
                 newManipulatedColumn1=mean(originalColumn1), 
                 newManipulatedColumn2=median(originalColumn2))
