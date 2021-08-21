---
title: "Exhausting all arguments on qgraph"
subtitle: "Let's use it all!"
excerpt: "A reminder of what each qgraph argument does."
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
- network psychometrics
- tutorials
- qgraph
layout: single # or single-sidebar
---
## Preparing the data

We'll use all arguments on qgraph on this blog post. Well, we'll either do that
or die trying...
Let's again use the 25 item Big-Five questionnaire from the *psych* package.
This time we'll also load the items so we can create a pretty neat viz.


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

# Data
df <- bfi[,1:25]

# Hyperparameters
# Group:
traits <- rep(c('Agreeableness',
                'Conscientiousness',
                'Extraversion',
                'Neuroticism',
                'Openness'),
              each = 5)

# Nodes:
items <- c(
  "Am indifferent to the feelings of others.",
  "Inquire about others' well-being.",
  "Know how to comfort others.",
  "Love children.",
  "Make people feel at ease.",
  "Am exacting in my work.",
  "Continue until everything is perfect.",
  "Do things according to a plan.",
  "Do things in a half-way manner.",
  "Waste my time.",
  "Don't talk a lot.",
  "Find it difficult to approach others.",
  "Know how to captivate people.",
  "Make friends easily.",
  "Take charge.",
  "Get angry easily.",
  "Get irritated easily.",
  "Have frequent mood swings.",
  "Often feel blue.",
  "Panic easily.",
  "Am full of ideas.",
  "Avoid difficult reading material.",
  "Carry the conversation to a higher level.",
  "Spend time reflecting on things.",
  "Will not probe deeply into a subject.")

# Neuroticism columns:
neuroticism <- c('N1', 'N2', 'N3', 'N4', 'N5')
```

## Let's create the biggest function I ever created
Say we're interested in Neuroticism. We want to look at its relationship with
the other personality dimensions, its connections and its place in the network.
In our function, we'll make the neuroticism nodes bigger. 
And well... this is the only major change we'll make.
The rest of the arguments will remain practically the default ones. Oh!, another
fancy thing we'll do is plot the items and its dimensions in the graph.


```r
network <- 
  qgraph(
    input = cor_auto(df),
    
    #' *Important additional arguments* (p. 29)
    
    layout = 'spring', # 'circle', 'groups', 'circular'
    groups = traits, # list or vector
    minimum = 0, # min value to be plotted
    #' *maximum* =, max value to scale edge widths, default is absmax pcor(x,y)
    cut = 0, # value to initiate the scaling of edge widths
    details = F, # if T, min/max/cut is printed under the graph
    threshold = 0, # edges with abs value below this are REMOVED from estimation
    palette = 'colorblind', # 'rainbow', 'pastel', 'gray', 'R', 'ggplot2'
    theme = 'colorblind', # 'classic', 'gray', 'Hollywood', 'Borkulo', 'gimme',
    # 'TeamFortress', 'Reddit', 'Leuven', 'Fried'
    
    #' *Additional options for correlation/covariance matrices* (p. 30)
    
    graph = 'glasso', # 'cor', 'pcor'
    threshold = 'none', # remove edges based on significance testing; 'sig',
    # 'holm', 'hochberg', 'hommel', 'bonferroni', 'BH', 'BY', 'fdr', 'locfdr'
    sampleSize = nrow(df), # sample size, when graph="glasso" or minimum="sig"
    tuning = 0.5, # gamma argument
    lambda.min.ratio = 0.01, # min lambda value for glasso estimation
    gamma = 0.5, # just an alias for tuning, overwrites tuning
    refit = F, # should the optimal graph be refitted without LASSO?
    countDiagonal = F, # count diagonal in EBIC comp? generally F
    alpha = 0.05, # sig value for not showing edges if minimum = 'sig'
    bonf = F, # should a bonferroni correction be used if minimum = 'sig'?
    FDRcutoff = 0.9, # False-Discovery Rate cutoff if *threshold* = 'fdr'
    
    #' *Output arguments* (p. 30-31)
    
    #mar = c(3, 3, 3, 3), # margins' vector c(bottom, left, top, rigth)
    #' *filetype* = 'R', can also be 'pdf', 'svg', 'tex', 'jpg', 'tiff'
    #' *filename* = 'graph', name of the file WITHOUT extension
    width = 7 * 1.4, # width of figue
    height = 7, # height of figure
    normalize = T, # graph's normalized to look the same for all sizes
    DoNotPlot = F, # useful to save plot without plotting
    plot = T, # should a new plot be made? if F, adds graph to existing plot
    rescale = T, # should layout be rescaled? best used with plot = F
    standAlone = F, # make output standalone LaTeX file if filetype = 'tex'
    
    #' *Graphical arguments*
    
    # Nodes (p. 31-32)
    
    #color = rainbow(length(groups)), # vector with a color for each node
    vsize = ifelse(colnames(df) == neuroticism, 7.5, 6),
    # indicates node size, can be a vector with size for each node
    # default =  8*exp(-nNodes/80)+1
    #' *vsize2* = ..., node vertical size if shape = 'rectangle'
    node.width = 1, # scalar on value of vsize
    node.height = 1, # scalar on value of vsize2
    borders = T, # should borders be plotted?
    border.color = 'black', # color vector indicating colors of borders
    border.width = 0.5, # controls width of the border
    shape = 'circle', # 'square', 'triangle', 'diamond', 'ellipse', 'heart'
    #' *polygonList* = ..., list containing named lists for each element to
    #' include polygons
    vTrans = 255, # transparency of nodes, between 0 and 255 (no transparency)
    #' *subplots* = ..., list with as elements R expressions or NULL for 
    #' each node. If an R expression, evaluated to create plot for the node.
    #' *subpars* = ..., list of graphical parameters to use in subplots
    #' *subplotbg* = ..., background to be used in subplots
    images = NA, # indicate file location of PNG of JPEG img to use as nodes
    noPar = T, # don't run the par function
    pastel = F, # should default colors be chosen from pastel colors?
    #' *rainbowStart* = ..., number from 0 to 1 indicating offset of rainbow
    usePCH = F, # nodes drawn using polygons or base R plotting symbols?
    node.resolution = 100, # resolution of nodes if usePCH = F
    # title = "Big-Five Inventory Psychometric Network", string graph title
    # title.cex = 0.5, size of title
    #' *preExpression* = ..., parsable string containing R code to be evaluated
    #' after opening a plot and before drawing the graph
    #' *postExpression* = ..., parsable string containing R code to be evaluated
    #' just before closing the device
    diag = F, # should diagonal also be plotted? Can also be 'col'
    
    # Node labels (p. 32-33)
    
    nodeNames = items,
    
    
    # Legend
    legend.cex = 0.23
    
  )
```

<img src="{{< blogdown/postref >}}index_files/figure-html/Estimate network-1.png" width="672" />


## References
Costantini, G., Epskamp, S., Borsboom, D., Perugini, M., Mõttus, R., Waldorp, 
L. J., & Cramer, A. O. J. (2015). State of the aRt personality research: 
A tutorial on network analysis of personality data in R. 
*Journal of Research in Personality*, *54*, 13–29. 
https://doi.org/10.1016/j.jrp.2014.07.003

Epskamp, S., Cramer, A. O. J, Waldorp, L. J., Schmittmann, V. D., Borsboom, D. 
(2012). qgraph: Network visualizations of relationships in psychometric data. 
*Journal of Statistical Software*, *48*(4), 1–18. 
https://doi.org/10.18637/jss.v048.i04 
