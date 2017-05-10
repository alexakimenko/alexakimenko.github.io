---
title: "test2"
author: "Alex Akimenko"
date: "10 05 2017"
output: html_document
---

html_fragment


## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


{% highlight r %}
summary(cars)
{% endhighlight %}



{% highlight text %}
##      speed           dist       
##  Min.   : 4.0   Min.   :  2.00  
##  1st Qu.:12.0   1st Qu.: 26.00  
##  Median :15.0   Median : 36.00  
##  Mean   :15.4   Mean   : 42.98  
##  3rd Qu.:19.0   3rd Qu.: 56.00  
##  Max.   :25.0   Max.   :120.00
{% endhighlight %}

## Including Plots

You can also embed plots, for example:


{% highlight text %}
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
{% endhighlight %}



{% highlight text %}
## Warning in normalizePath(f2): path[1]="./webshot6224852b4cd.png": No such
## file or directory
{% endhighlight %}



{% highlight text %}
## Warning in file(con, "rb"): не могу открыть файл './
## webshot6224852b4cd.png': No such file or directory
{% endhighlight %}



{% highlight text %}
## Error in file(con, "rb"): не могу открыть соединение
{% endhighlight %}

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
