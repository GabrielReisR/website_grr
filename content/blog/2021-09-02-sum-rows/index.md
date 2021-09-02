---
title: "Sum specific columns by rows"
subtitle: ""
excerpt: "Sum scores using dplyr::select()"
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
- dplyr
- tutorials
- function
layout: single # or single-sidebar
---
## Sometimes you have a messy dataset

By that, I mean a dataset with a messy column ordering, uneccessary variables
and so on. That's somewhat the case with the DASS-42 dataset taken from
Kaggle (available 
[here](https://www.kaggle.com/lucasgreenwell/depression-anxiety-stress-scales-responses)).

I've taken the liberty of preserving "only" 10k cases. Let's load the data and
present the problem.


```r
load_libraries <- function(){
  if (!require("dplyr"))
    install.packages("dplyr"); library(dplyr) # select() and mutate()
  if (!require("ggplot2"))
    install.packages("ggplot2"); library(ggplot2) # final plot
  if (!require("magrittr"))
    install.packages("magrittr"); library(magrittr) # %<>% operator

}

load_libraries()

df <- read.csv('https://raw.githubusercontent.com/GabrielReisR/R/master/estrutura%20de%20dados/dados/dass42_brief.csv')[-1]

names(df)
```

```
##   [1] "X"                     "Q1A"                   "Q1I"                  
##   [4] "Q1E"                   "Q2A"                   "Q2I"                  
##   [7] "Q2E"                   "Q3A"                   "Q3I"                  
##  [10] "Q3E"                   "Q4A"                   "Q4I"                  
##  [13] "Q4E"                   "Q5A"                   "Q5I"                  
##  [16] "Q5E"                   "Q6A"                   "Q6I"                  
##  [19] "Q6E"                   "Q7A"                   "Q7I"                  
##  [22] "Q7E"                   "Q8A"                   "Q8I"                  
##  [25] "Q8E"                   "Q9A"                   "Q9I"                  
##  [28] "Q9E"                   "Q10A"                  "Q10I"                 
##  [31] "Q10E"                  "Q11A"                  "Q11I"                 
##  [34] "Q11E"                  "Q12A"                  "Q12I"                 
##  [37] "Q12E"                  "Q13A"                  "Q13I"                 
##  [40] "Q13E"                  "Q14A"                  "Q14I"                 
##  [43] "Q14E"                  "Q15A"                  "Q15I"                 
##  [46] "Q15E"                  "Q16A"                  "Q16I"                 
##  [49] "Q16E"                  "Q17A"                  "Q17I"                 
##  [52] "Q17E"                  "Q18A"                  "Q18I"                 
##  [55] "Q18E"                  "Q19A"                  "Q19I"                 
##  [58] "Q19E"                  "Q20A"                  "Q20I"                 
##  [61] "Q20E"                  "Q21A"                  "Q21I"                 
##  [64] "Q21E"                  "Q22A"                  "Q22I"                 
##  [67] "Q22E"                  "Q23A"                  "Q23I"                 
##  [70] "Q23E"                  "Q24A"                  "Q24I"                 
##  [73] "Q24E"                  "Q25A"                  "Q25I"                 
##  [76] "Q25E"                  "Q26A"                  "Q26I"                 
##  [79] "Q26E"                  "Q27A"                  "Q27I"                 
##  [82] "Q27E"                  "Q28A"                  "Q28I"                 
##  [85] "Q28E"                  "Q29A"                  "Q29I"                 
##  [88] "Q29E"                  "Q30A"                  "Q30I"                 
##  [91] "Q30E"                  "Q31A"                  "Q31I"                 
##  [94] "Q31E"                  "Q32A"                  "Q32I"                 
##  [97] "Q32E"                  "Q33A"                  "Q33I"                 
## [100] "Q33E"                  "Q34A"                  "Q34I"                 
## [103] "Q34E"                  "Q35A"                  "Q35I"                 
## [106] "Q35E"                  "Q36A"                  "Q36I"                 
## [109] "Q36E"                  "Q37A"                  "Q37I"                 
## [112] "Q37E"                  "Q38A"                  "Q38I"                 
## [115] "Q38E"                  "Q39A"                  "Q39I"                 
## [118] "Q39E"                  "Q40A"                  "Q40I"                 
## [121] "Q40E"                  "Q41A"                  "Q41I"                 
## [124] "Q41E"                  "Q42A"                  "Q42I"                 
## [127] "Q42E"                  "country"               "source"               
## [130] "introelapse"           "testelapse"            "surveyelapse"         
## [133] "TIPI1"                 "TIPI2"                 "TIPI3"                
## [136] "TIPI4"                 "TIPI5"                 "TIPI6"                
## [139] "TIPI7"                 "TIPI8"                 "TIPI9"                
## [142] "TIPI10"                "VCL1"                  "VCL2"                 
## [145] "VCL3"                  "VCL4"                  "VCL5"                 
## [148] "VCL6"                  "VCL7"                  "VCL8"                 
## [151] "VCL9"                  "VCL10"                 "VCL11"                
## [154] "VCL12"                 "VCL13"                 "VCL14"                
## [157] "VCL15"                 "VCL16"                 "education"            
## [160] "urban"                 "gender"                "engnat"               
## [163] "age"                   "screensize"            "uniquenetworklocation"
## [166] "hand"                  "religion"              "orientation"          
## [169] "race"                  "voted"                 "married"              
## [172] "familysize"            "major"
```

The thing here is: item responses are stores in the "Q_number_A" columns.
And columns are ordered in a weirdy weird way.
The DASS scale measures depression, anxiety and stress. Say we're interested
in joining these three constructs in an umbrella construct called *neuroticism*.

In order to do that, we'd need to sum all items that start with a Q and ended
with an A. We can do that with *dplyr*.


```r
df %<>% 
  mutate(neuroticism = df %>% select(starts_with("Q") & ends_with("A")) %>%
                  rowSums())
```

What we've done is:

1. Created a column called *neuroticism* using the `mutate` function.
2. Selected all variables in *df* that started with "Q" and ended with an "A".
3. Used `rowSums()` on those variables and stored it in *neuroticism*.

Let's check out our new variable!


```r
df %>%
  ggplot(aes(neuroticism)) +
  geom_histogram(aes(y = ..density..), bins = 40,
                 colour = "#011e5a", fill = "white") +
  stat_function(fun = dnorm,
                args = list(
                  mean = mean(df$neuroticism, na.rm = T),
                  sd = sd(df$neuroticism, na.rm = T)),
                colour = "#011e5a") +
  
  # Rótulos
  xlab('Neuroticism Score') +
  ylab('Density of Scores') +
  
  # Tema
  theme_classic()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/Histogram of neuroticism scores-1.png" width="672" />

It worked!
That's it for today!
