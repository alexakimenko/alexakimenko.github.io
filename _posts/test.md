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
## 
## Attaching package: 'igraph'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:stats':
## 
##     decompose, spectrum
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:base':
## 
##     union
{% endhighlight %}



{% highlight text %}
## data.table 1.9.6  For help type ?data.table or https://github.com/Rdatatable/data.table/wiki
{% endhighlight %}



{% highlight text %}
## The fastest way to learn (by data.table authors): https://www.datacamp.com/courses/data-analysis-the-data-table-way
{% endhighlight %}



{% highlight text %}
## Loading required package: rJava
{% endhighlight %}



{% highlight text %}
## Loading required package: xlsxjars
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'openxlsx'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:xlsx':
## 
##     createWorkbook, loadWorkbook, read.xlsx, saveWorkbook,
##     write.xlsx
{% endhighlight %}



{% highlight text %}
## network: Classes for Relational Data
## Version 1.13.0 created on 2015-08-31.
## copyright (c) 2005, Carter T. Butts, University of California-Irvine
##                     Mark S. Handcock, University of California -- Los Angeles
##                     David R. Hunter, Penn State University
##                     Martina Morris, University of Washington
##                     Skye Bender-deMoll, University of Washington
##  For citation information, type citation("network").
##  Type help("network-package") to get started.
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'network'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:igraph':
## 
##     %c%, %s%, add.edges, add.vertices, delete.edges,
##     delete.vertices, get.edge.attribute, get.edges,
##     get.vertex.attribute, is.bipartite, is.directed,
##     list.edge.attributes, list.vertex.attributes,
##     set.edge.attribute, set.vertex.attribute
{% endhighlight %}



{% highlight text %}
## Warning: package 'visNetwork' was built under R version 3.3.2
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'visNetwork'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:igraph':
## 
##     %>%
{% endhighlight %}



{% highlight text %}
## Error in loadNamespace(name): there is no package called 'webshot'
{% endhighlight %}

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
