Global Capture forecast
========================================================
author: Thomas Berger
date: Jan 31, 2017
width: 1440
height: 900

Introduction
========================================================

FAO Global catch contains capture production statistics by country or territory, for the reporting year 2014; the most recent year for which data is available.

The shiny App is here <https://thomasberger.shinyapps.io/ShinyApp/> 

GIT repo <https://github.com/bergertom/ShinyApp>.

The data was loaded from the FAO query panel: <http://www.fao.org/fishery/statistics/global-capture-production/query/en>, and saved into a CSV file for this report. Data downloaded 17/01/2017.

To export, select country [Alphabetical], Index [All] to have all countries, click [select all]. Under time, select [2014] as year. Under Species, select [Ybk main groups], select [Fish, crustaceans and molluscs, etc.] to have data for human consumption.

Plot explanation
========================================================
<br><div>
- FAO data shown as dots (1000 tons)
- The blue regression line, with 95% confidence interval
- Red dots show predicted (imputed using lm) catch for 2015 and 2016
</div><p><br>
The chart title shows the years selected in the slider.
</p>
<h2>Conclusion</h2>
After 1988, the collapse of several stocks is visible as a drop in global production.
<p><br>
What is noticeable, the claims about
the fish global stock collapsing are not true:
<br>
<b>As you will observe, the data shows that global catch - apart
from normal annual fluctuations - is not declining. For most of the last 20 
years, the regression line is flat, or points slightly upwards.
This indicates a small annual increase in  global fish production.</b>



Server calculation
========================================================


```r
   # read CSV file
   d1 <- read.csv("FAO_Global_Catch.csv")
   # aggregate, round, set column names
   d2 <- aggregate(d1[,2], list(d1[,1]), sum)
   d2[,2] <- round(d2[,2]/1000,0)
   colnames(d2) <- c("Year","Quantity")
   # in the server-app, the year range is slected using the slider
   df <- subset(d2, Year >= 1993 & Year <= 2014)
   # generate the plot
   myplot <- ggplot(data=df, aes(x=Year, y=Quantity)) + 
          xlab("Year") + ylab("Catch Quantity (1000 tons)") +
          ggtitle("Global Capture Forecast 1993-2016")+
          geom_point() +
          geom_smooth(method="glm")
   # do a linear regression for the selected data
   fit <- glm(Quantity ~ Year, data=df)  
   # project data for future years
   p2 <- predict(fit, data.frame(Year=c(2015,2016)))
   d3 <- data.frame(Year=c(2015,2016), Quantity=round(p2,0))
   # add the projection to the plot
   myplot <- myplot + geom_point(data=d3, colour="red")
```

Graph result
========================================================

```r
   myplot
```

<img src="Presentation-figure/unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" width="1200px" height="700px" />
