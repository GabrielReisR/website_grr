---
title: "Estimating a psychometric network with qgraph"
subtitle: "Learn how to estimate a psychometric network with qgraph"
excerpt: "Understand qgraph's simplicity in estimating psychometric networks."
author: "Gabriel R. R."
date: 2021-08-10
output: 
  html_document:
    toc: true
    toc_float: true
    collapsed: false
    smooth_scroll: true
    df_print: paged
    code_folding: show
draft: false
images:
series:
tags:
categories:
- network psychometrics
- tutorials
- qgraph
layout: single # or single-sidebar
---
## We're gonna need data

To estimate a psychological network we're going to need data.
Let's take the 25 item Big-Five questionnaire from the *psych* package.


```r
load_libraries <- function(){
  if (!require("dplyr"))
    install.packages("dplyr"); library(dplyr)
  if (!require("psych"))
    install.packages("psych"); library(psych)
  if(!require("qgraph"))
    install.packages("qgraph"); library(qgraph)
}

load_libraries()

df <- bfi[,1:25]
head(df)
```

```
##       A1 A2 A3 A4 A5 C1 C2 C3 C4 C5 E1 E2 E3 E4 E5 N1 N2 N3 N4 N5 O1 O2 O3 O4
## 61617  2  4  3  4  4  2  3  3  4  4  3  3  3  4  4  3  4  2  2  3  3  6  3  4
## 61618  2  4  5  2  5  5  4  4  3  4  1  1  6  4  3  3  3  3  5  5  4  2  4  3
## 61620  5  4  5  4  4  4  5  4  2  5  2  4  4  4  5  4  5  4  2  3  4  2  5  5
## 61621  4  4  6  5  5  4  4  3  5  5  5  3  4  4  4  2  5  2  4  1  3  3  4  3
## 61622  2  3  3  4  5  4  4  5  3  2  2  2  5  4  5  2  3  4  4  3  3  3  4  3
## 61623  6  6  5  6  5  6  6  6  1  3  2  1  6  5  6  3  5  2  2  3  4  3  5  6
##       O5
## 61617  3
## 61618  3
## 61620  2
## 61621  5
## 61622  3
## 61623  1
```

We have now three initial alternatives of estimation using the *qgraph* package:

1. Estimate network with a correlation matrix
2. Estimate network with a partial correlation matrix
3. Estimate network using EBICglasso

## 1. Estimate network with a correlation matrix


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

network <- qgraph(mat)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/Estimate network with a correlation matrix-1.png" width="672" />

That's it for now :)
