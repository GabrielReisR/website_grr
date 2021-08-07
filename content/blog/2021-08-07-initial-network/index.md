---
title: "Estimating a psychometric network with qgraph"
subtitle: "Learn how to estimate a simple network with qgraph"
excerpt: "Understand qgraph's simplicity in estimating networks"
author: "Gabriel R. R."
date: 2021-08-07
output: 
  html_document:
    toc: true
    toc_float: true
    collapsed: false
    smooth_scroll: true
    df_print: paged
    code_folding: show
draft: true
images:
series:
tags:
categories:
- network psychometrics
- tutorial
layout: single # or single-sidebar
---
## A lot can be done with just a matrix

We're gonna learn how to estimate a simple network with qgraph.
For starters, we're gonna need to install and/or load some packages.


```r
load_libraries <- function(){
  if (!require("dplyr"))
    install.packages("dplyr"); library(dplyr)
  if(!require("qgraph"))
    install.packages("qgraph"); library(qgraph)
}
load_libraries()
```

There we go! Now, say we have a symmetrical matrix called *mat*. 
This matrix could be interpreted as a mock-correlation matrix. To be fairly
simple, we only need a matrix to estimate a network in qgraph.


```r
mat <- matrix(
  c(
       0,  0.3,    0, -0.3, 0.2, 0.3,
     0.3,    0, -0.9,    0,   0,    0,
       0, -0.9,    0,  0.8,   0,    0,
    -0.3,    0,  0.8,    0, 0.3,    0,
     0.2,    0,    0,  0.3,   0,    0,
     0.3,    0,    0,    0,   0,    0
    ),
  ncol = 6, nrow = 6,
  byrow = TRUE)

qgraph(mat)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/Creating matrix-1.png" width="672" />

## Let's tweak some things

Ok.











