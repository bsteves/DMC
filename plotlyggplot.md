---
name: ggplotly
layout: post
title: Make your ggplots shareable, collaborative, and with D3
date: 2014-04-17
author: Matt Sundquist
authorurl: https://plot.ly/team
tags:
- R
- API
- plotly
- ggplot2
- data
- maps
---

[Ggplotly](https://github.com/ropensci/plotly) and [Plotly's R API](https://plot.ly/api/r) let you make ggplot2 plots, add `py$ggplotly()`, and make your plots interactive, online, and drawn with D3. Let's make some.





## 1. Getting Started and Examples

Here is Fisher's iris data.


```r
library("ggplot2")
ggiris <- qplot(Petal.Width, Sepal.Length, data = iris, color = Species)
print(ggiris)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


Let's make it in Plotly. Install:

Load.


```r
library("plotly")
```

```
## Loading required package: RCurl
## Loading required package: bitops
## Loading required package: RJSONIO
```




```r
username <- "bsteves"
api_key <- "hlc84pq6aq"
py <- plotly(username, api_key)
```


It just works.


```r
py$ggplotly(ggiris)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/14" width="600" frameBorder="0"></iframe>


The call opens a browser tab. Or in an `.Rmd` document, the plot is embedded if you specify the `plotly=TRUE` chunk option (see [source](https://gist.github.com/msund/10820797)). If you're running this from the source, it makes all the graphs at once in your browser. Reaction my first time: here be dragons. 

If you click the _data and graph_ link in the embed, it takes you to Plotly's GUI, where you can edit the graph, see the data, and share your plot with collaborators. 

<br><br>

### 1.2 Maps

Next: Maps!


```r
data(canada.cities, package="maps")
viz <- ggplot(canada.cities, aes(long, lat)) +
  borders(regions="canada", name="borders") +
  coord_equal() +
  geom_point(aes(text=name, size=pop), colour="red", alpha=1/2, name="cities")
```


Call Plotly.


```r
py$ggplotly(viz)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/15" width="600" frameBorder="0"></iframe>


<br><br>

### 1.3 Scatter

Want to make a scatter and add a [smoothed conditional mean](http://docs.ggplot2.org/current/geom_smooth.html)? Here's how to do it in Plotly. For the rest of the plots, we'll just print the Plotly version to save space. You can hover on text to get data, or click and drag across a section to zoom in.


```r
model <- lm(mpg ~ wt + factor(cyl), data=mtcars)
grid <- with(mtcars, expand.grid(
  wt = seq(min(wt), max(wt), length = 20),
  cyl = levels(factor(cyl))
))

grid$mpg <- stats::predict(model, newdata=grid)

viz2 <- qplot(wt, mpg, data=mtcars, colour=factor(cyl)) + geom_line(data=grid)
py$ggplotly(viz2)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/16" width="600" frameBorder="0"></iframe>


<br><br>

### 1.4 Lines

Or, take `ggplotly` for a spin with the orange dataset:


```r
orange <- qplot(age, circumference, data = Orange, colour = Tree, geom = "line")
py$ggplotly(orange)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/17" width="600" frameBorder="0"></iframe>


<br><br>

### 1.5 Alpha blend

Or, make plots [beautiful](http://mandymejia.wordpress.com/2013/11/13/10-reasons-to-switch-to-ggplot-7/). 


```r
prettyPlot <- ggplot(data=diamonds, aes(x=carat, y=price, colour=clarity))
prettyPlot <- prettyPlot + geom_point(alpha = 1/10)
py$ggplotly(prettyPlot)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/18" width="600" frameBorder="0"></iframe>


<br><br>

### 1.6 Functions

Want to [draw functions](http://stackoverflow.com/questions/1853703/plotting-functions-in-r) with a `curve`?


```r
eq <- function(x) {x*x}
tmp <- data.frame(x=1:50, y=eq(1:50))

# Make plot object
p <- qplot(x, y, data=tmp, xlab="X-axis", ylab="Y-axis")
c <- stat_function(fun=eq)

py$ggplotly(p + c)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/19" width="600" frameBorder="0"></iframe>


<br><br>

## 2. A GitHub for data and graphs

Like we might work together on code on GitHub or a project in a Google Doc, we can edit graphs and data together on Plotly. Here's how it works: 

- Your URL is shareable. 
- Public use is free.
- You can set [the privacy](http://plot.ly/api/r/docs/privacy) of your graph. 
- You can edit and add to plots from our GUI or with R or [APIs](https://plot.ly/api) for Python, MATLA, Julia, Perl, Arduino, Raspberry Pi, and REST. 
- You get a profile of graphs, like [Rhett Allain](https://plot.ly/~RhettAllain/) from Wired Science.
- You can [embed interactive graphs in iframes](http://plot.ly/api/r/docs/iframes).

<br><br>

### 2.1 Inspiration and team

Plotly's API is part of `[rOpenSci](ropensci.org)`, being developed by the brilliant [Toby Hocking](http://cbio.ensmp.fr/~thocking/), and on [GitHub](https://github.com/ropensci/plotly). Your thoughts, issues, and pull requests are welcome. Right now, you can make scatter and line plots; let us know what you'd like to see next.

The project was inspired by [Hadley Wickham](https://github.com/hadley/) and the elegance and precision of [`ggplot2`](http://ggplot2.org/). Thanks to [Scott Chamberlain](scottchamberlain.info), [Joe Cheng](https://github.com/jcheng5), and [Elizabeth Morrison-Wells](https://twitter.com/efvmw) for their help.

<br><br>

## 3. ggthemes and Plotly

Using [`ggthemes`](https://github.com/jrnold/ggthemes) opens up another set of custom graph filters for styling your graphs. To get started, you'll want to install `ggthemes`.


```r
library("devtools")
install_github("ggthemes", "jrnold")
```


and load your data.


```r
library("ggplot2")
library("ggthemes")
dsamp <- diamonds[sample(nrow(diamonds), 1000), ]
```

<br><br>
Inverse gray.


```r
gray <- (qplot(carat, price, data = dsamp, colour = cut) + 
           theme_igray())
py$ggplotly(gray)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/20" width="600" frameBorder="0"></iframe>

<br><br>
The Tableau scale.


```r
tableau <- (qplot(carat, price, data = dsamp, colour = cut) + 
              theme_igray() + 
              scale_colour_tableau())
py$ggplotly(tableau)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/21" width="600" frameBorder="0"></iframe>

<br><br>
[Stephen Few's](http://www.perceptualedge.com/articles/visual_business_intelligence/rules_for_using_color.pdf) scale.


```r
few <- (qplot(carat, price, data = dsamp, colour = cut) + 
          theme_few() + 
          scale_colour_few())
py$ggplotly(few)
```

<iframe height="600" id="igraph" scrolling="no" seamless="seamless"
				src="https://plot.ly/~bsteves/22" width="600" frameBorder="0"></iframe>

