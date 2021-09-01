---
title: "Function to read lots of libraries"
subtitle: ""
excerpt: "Helpful to initiate an analysis script."
author: "Gabriel R. R."
date: 2021-08-21
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
- tutorials
- function
layout: single # or single-sidebar
---
## You want to start a new analysis script in R

The thing is, you're gonna need lots of packages. Say you're interested in
examining some descriptive statistics, generating a Confirmatory Factor
Analysis and doing an IRT on some questionnaire items. Reasonable packages for
these operations could be:


```r
library(dplyr)
library(lavaan)
library(magrittr)
library(mirt)
library(psych)
library(QuantPsyc)
library(qgraph)
library(semPlot)
library(tidyr)
```

Let me save you some trouble: you could have some issues with the code above.
For me, I noticed the following:

1. Everytime I updated R, I had to download every package again. That was
a very boring task to do.
2. When I shared my script with someone who didn't have the same packages I was
reading with the `library` function, that person now had to install the packages
they didn't had.
3. When restarting a R session, I had to click and drag on all those `library`
lines to make sure I read them again in that session.

That happened with me constantly until I stumbled upon 
[this code here](https://github.com/brunamdalmoro/Aplicativos_Prob_Estatistica/blob/master/1-descritivas_graficos/ui.R). The `if (!require("package"));install.packages("package")`
was very ingenious. I started using that, then I adapted the code with a
`library(package)` extension; then I created a function to read all of them at
once.

The code we saw above I now write as:


```r
load_libraries <- function(){
  if (!require("dplyr"))
    install.packages("dplyr"); library(dplyr)
  if (!require("lavaan"))
    install.packages("lavaan"); library(lavaan)
  if(!require("magrittr"))
    install.packages("magrittr"); library(magrittr)
  if(!require("mirt"))
    install.packages("mirt"); library(mirt)
  if (!require("psych"))
    install.packages("psych"); library(psych)
  if (!require("QuantPsyc"))
    install.packages("QuantPsyc"); library(QuantPsyc)
  if (!require("qgraph"))
    install.packages("qgraph"); library(qgraph)
  if (!require("semPlot"))
    install.packages("semPlot"); library(semPlot)
  if (!require("tidyr"))
    install.packages("tidyr"); library(tidyr)
}

load_libraries()
```

For me, the main idea of this code is **reproducibility**. Even if someone 
doesn't have those packages, they can read the function and get on with their
lives.

I hope this helps someone someday as much as it helped me.

