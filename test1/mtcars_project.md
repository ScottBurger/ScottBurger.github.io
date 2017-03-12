# An Analysis of the mtcars Dataset
Scott Burger

11/20/2015


### Executive Summary

In this analysis, I explore the difference in miles per gallon, or mileage, between automatic and manual transmission cars from the mtcars dataset in R. The mtcars dataset was extracted from the 1974 Motor Trend US magazine, and comprises fuel consumption and 10 aspects of automobile design and performance for 32 automobiles (1973-74 models). This analysis shows that manual transmission cars are more fuel-efficient than automatic transmission cars for this data. We see that the mean miles per gallon for automatic transmission cars is 17.14, and for manual transmission cars is 24.39.

### Exploratory Data Analysis

Our natural first-step would be to simply plot a functional fit between the car's listed mpg and its transmission. Because the data is bimodal, it doesn't make sense to plot a function across two categorical variables. Instead, we should look at the overall distributions across both transmission types. This is done in **Fig1**. We see in **Fig1** that both the interquartile range, and the mean for the manual transmission cars (teal) are higher in mpg than the automatic transmission cars (orange). At first glance in our exploration of the data, we would assume that manual transmission cars have better mileage than automatics.


### Quantifying Mileage to Transmission

Let us quantify that the manual transmissions have better mileage. First I subset the data by transmission type, then take the mean of the data:


```r
a <- subset(x=mtcars, am==0)
m <- subset(x=mtcars, am==1)

mean(a$mpg)
```

```
## [1] 17.14737
```

```r
mean(m$mpg)
```

```
## [1] 24.39231
```

We see that the mean miles per gallon for manuals is about 24, and for automatics is about 17.

We must consider confounding variables with this analysis, however, as there's many aspects to the data that could be influencing the mileage other than just transmission type. We can do this by performing a multivariate regression and seeing which coefficients are the highest, and therefore most impactful on the mileage across all variables:


```r
model2 <- lm(mtcars$mpg ~ . , data=mtcars)
summary(model2)$coefficients
```

```
##                Estimate  Std. Error    t value   Pr(>|t|)
## (Intercept) 12.30337416 18.71788443  0.6573058 0.51812440
## cyl         -0.11144048  1.04502336 -0.1066392 0.91608738
## disp         0.01333524  0.01785750  0.7467585 0.46348865
## hp          -0.02148212  0.02176858 -0.9868407 0.33495531
## drat         0.78711097  1.63537307  0.4813036 0.63527790
## wt          -3.71530393  1.89441430 -1.9611887 0.06325215
## qsec         0.82104075  0.73084480  1.1234133 0.27394127
## vs           0.31776281  2.10450861  0.1509915 0.88142347
## am           2.52022689  2.05665055  1.2254035 0.23398971
## gear         0.65541302  1.49325996  0.4389142 0.66520643
## carb        -0.19941925  0.82875250 -0.2406258 0.81217871
```

The only coefficients that are remotely important, according to the model, are transmission type, **am**, and weight **wt**. We can then look at how mileage overall compares between the two transmission types by fitting a linear model between mileage and weight, as seen in **Fig2**. There we see a might steeper dependence on weight for manual transmission cars than for automatics. We can also see that the overall weights for automatics is higher than that for manuals, which could also impact mileage performance.

Finally, for model selection purposes, we can look at the diagnostic plot for modelling the mileage as a function of all parameters in **Fig3**. Because the other factors are introducing unnecessary residual error, it is best for this analysis to focus on the model design of **Fig2**, which shows the dependance of weight in the sensitivity of mpg as a confounding variable.

***

# Appendix of Figures

Fig1

```r
library(ggplot2)
q <- ggplot(mtcars, aes(am, mpg))
q + geom_boxplot(aes(color=factor(am)))
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

***

Fig2

```r
q1 <- ggplot(mtcars, aes(x=wt, y=mpg))
q2 <- q1 + geom_point(aes(color=factor(am))) + facet_grid(.~am) + geom_smooth(method="lm")
q2
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

***

Fig3

```r
par(mfrow= c(2,2))
plot(model2)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
