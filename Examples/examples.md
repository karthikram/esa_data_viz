# Additional ggplot2 examples.
Here are several more example plots you can do with ggplot2.

* Distributions


```coffee
# First we load up all the relevant libraries
library(ggplot2)
library(ggthemes)
library(grid)
library(plyr)
```






# Overlapped distributions example

This is an example of how to make overlapping, transparent distributions using ggplot2. The data is fake, and just simulated within the code.




```coffee
dt <- rnorm(50, 2, 0.5)
dt2 <- rnorm(50, 3, 0.5)
dt.all <- c(dt, dt2)
sp <- c("SP1", "SP2")
data <- data.frame(species = factor(rep(sp, each = 50)), var1 = dt.all)
# The color-blind palette with grey:
cbPalette <- c("#999999", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", 
    "#D55E00", "#CC79A7")
ggplot(data, aes(x = var1, fill = species)) + geom_density(alpha = 0.2) + xlab(expression(beta)) + 
    ylab("posterior density") + theme_bw() + scale_fill_hue(l = 40) + scale_x_continuous(limits = c(0, 
    4)) + theme(axis.title.x = element_text(size = 20), axis.title.y = element_text(size = 20, 
    angle = 90), axis.text.x = element_text(size = 14), axis.text.y = element_text(size = 14), 
    panel.grid.major = element_blank(), panel.grid.minor = element_blank())
```

![](figure/distributions.png) 


# Error Bars

This R script shows an example of how to make grouped bar plots with error bars using ggplot2 
Also shows great use of 'plyr' for summarizing data


```coffee
#read in data (change 'datafile' to match your computer)
datafile <- "../data/GrazingData.csv"
data <- read.csv(file=datafile, header=TRUE)
head(data)
```

```
##   X biomass grazed nutadd
## 1 1   214.5      y      y
## 2 2   241.4      y      y
## 3 3   202.5      y      y
## 4 4   170.9      y      y
## 5 5   127.3      y      y
## 6 6   228.2      y      y
```

```coffee
#remove 'X' column
data <- data[,2:4]
head(data)
```

```
##   biomass grazed nutadd
## 1   214.5      y      y
## 2   241.4      y      y
## 3   202.5      y      y
## 4   170.9      y      y
## 5   127.3      y      y
## 6   228.2      y      y
```

```coffee

df.avg <- ddply(data, .(grazed, nutadd), summarize,
                mean.biomass = mean(biomass), 
                sd.biomass = sd(biomass))


#The bars and the errorbars will have different widths, so...
#we need to specify how wide the objects we are dodging are
dodge <- position_dodge(width=0.9)

#Create a sophisticated label for the y-axis
y.label <- expression(paste("Biomass (g ", m^-2, ")"))

#now start the plot
myplot <- ggplot(data=df.avg, aes(y=mean.biomass, x=grazed, fill=nutadd)) +
#adds the bars
 geom_bar(position=dodge, stat="identity") + 
 #adds the errorbars by adding/subtracting std. devs.
  geom_errorbar(position=dodge, aes(ymin=(mean.biomass-sd.biomass), 
                                    ymax=(mean.biomass+sd.biomass)),
                width=0.15)+ 
  #adds desired x and y axis labels
  ylab(y.label) + xlab("Grazing Treatment") + 
  #desired labels for x axis
  scale_x_discrete(labels=c("Not Grazed", "Grazed")) + 
  scale_fill_grey(start=0.5, end=0.8,
  	#set colors and labels for bar groups
labels = c("Nutrient Added", "No Nutrient Added")) + 
  #set theme from 'ggthemes'
  theme_classic() + 
  
  #add extra theme modifications
  theme(legend.position = c(0.75,0.9), #position legend in plot
        legend.title = element_blank(), #no legend title
        legend.key = element_blank(), #no legend ky
        legend.background = element_rect(colour = NA), #no legend background color
        legend.key.height = unit(5, "mm") #set height of legend (requires 'grid' library)
  ) 

#print plot to window
myplot
```

![](figure/error_bars.png) 

```coffee

#save the plot as a pdf (change file variable first)
file = "../data/Grazing.pdf"
ggsave(filename=file, plot=myplot, width=4, height=4, units="in")
```
