---
title: "Clean data with one function"
subtitle: "Personalize your data cleaning with one function"
excerpt: "Instead of running a bunch of lines, run only one."
author: "Gabriel R. R."
date: 2021-09-03
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
## Cleaning data takes a bunch of code

If we're trying to stay organized, somewhere in the middle of this we'll have
a bunch of blank lines. With this, the more we clean, the harder it gets to get
a grasp on what was done. At the same time, it becomes difficult to initiate the
analysis part of our script. When we spend a bunch of lines cleaning, running
all those lines from top to bottom can take some time.

Let's imagine you're working with the
[DASS-42 dataset](https://www.kaggle.com/lucasgreenwell/depression-anxiety-stress-scales-responses).
You want to do a bunch of changes in the dataset:

1. Mantain only DASS responses, age and gender.
2. Rename all items so they start as *dass_*.
3. Fix scoring system.
4. Create scores for depression.
5. Create scores for anxiety.
6. Create scores for stress.
7. Create classifications for depression.
8. Create classifications for anxiety.
9. Create classifications for stress.
10. Change classifications to factor.

To do all of that, we'll use only two packages: *dplyr* and *tidyr*.


```r
load_libraries <- function(){
  if (!require("dplyr"))
    install.packages("dplyr"); library(dplyr) # select() and mutate()
  if (!require("magrittr"))
    install.packages("magrittr"); library(magrittr) # %<>% operator
}

load_libraries()

df <- read.csv('https://raw.githubusercontent.com/GabrielReisR/R/master/estrutura%20de%20dados/dados/dass42_brief.csv')[-1]

names(df) # 10000 lines and 173 columns
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


Let's start our function. The function will receive a dataframe and will 
perform the changes we want within this dataframe.


```r
clean_data <- function(df){
  #' Mantain only DASS responses, age and gender
  df %<>%
    select(age, gender,
           (starts_with("Q") & ends_with("A")))
  
  #' Rename all items so they start as *dass_*
  df %<>%
    select(dass_ = starts_with("Q"))
  
  #' Fix scoring system
  df %<>% select(starts_with("dass_")) %>% -1
  
  #' Create scores for depression
  df %<>%
    mutate(
      depression_score =
        df %>%
        select(dass_2, dass_5, dass_12, dass_13, dass_16, dass_17, dass_20,
               dass_23, dass_25, dass_26, dass_31, dass_32, dass_36, dass_41) %>% 
        rowSums())
  
  #' Create scores for anxiety
  df %<>%
    mutate(
      anxiety_score =
        df %>%
        select(dass_1, dass_6, dass_8, dass_10, dass_11, dass_14, dass_21,
               dass_22, dass_27, dass_28, dass_34, dass_37, dass_40, dass_42) %>% 
        rowSums())
  
  #' Create scores for stress
  df %<>%
    mutate(
      stress_score =
        df %>%
        select(dass_3, dass_4, dass_7, dass_9, dass_15, dass_18, dass_19,
               dass_24, dass_29, dass_30, dass_33, dass_35, dass_38, dass_39) %>% 
        rowSums())
  
  #' Create classification for depression, anxiety and stress
  df %<>%
    mutate(
      # Groupings for depression
      depression_class = 
        case_when(depression_score < 10 ~ "Normal",
                  depression_score >= 10 & depression_score < 14 ~ "Mild",
                  depression_score >= 14 & depression_score < 21 ~ "Moderate",
                  depression_score >= 21 & depression_score < 28 ~ "Severe",
                  depression_score >= 28 ~ "Highly severe"),
      # Groupings for anxiety
      anxiety_class = 
        case_when(anxiety_score < 8 ~ "Normal",
                  anxiety_score >= 8 & anxiety_score < 10 ~ "Mild",
                  anxiety_score >= 10 & anxiety_score < 15 ~ "Moderate",
                  anxiety_score >= 15 & anxiety_score < 20 ~ "Severe",
                  anxiety_score >= 20 ~ "Highly severe"),
      # Groupings for stress
      stress_class = 
        case_when(stress_score < 15 ~ "Normal",
                  stress_score >= 15 & stress_score < 19 ~ "Mild",
                  stress_score >= 19 & stress_score < 25 ~ "Moderate",
                  stress_score >= 25 & stress_score < 34 ~ "Severe",
                  stress_score >= 34 ~ "Highly severe")
    )
  
  #' Change classifications to factor
  severity_level <- c("Normal", "Mild", "Moderate", "Severe", "Highly severe")
  
  df$depression_class %<>% as.factor
  df$depression_class <- factor(df$depression_class,
                                levels = severity_level)
  
  df$anxiety_class %<>% as.factor
  df$anxiety_class <- factor(df$anxiety_class,
                             levels = severity_level)
  
  df$stress_class %<>% as.factor
  df$stress_class <- factor(df$stress_class,
                            levels = severity_level)
  
  return(df)
}
```

There! I know it seems like a lot, but we'll only be seeing this whole function
when we resolve to open it.

## Running the code
As can be seen, the function performs the changes in our dataframe and returns
the altered dataframe to our desired object.


```r
df <- clean_data(df)

glimpse(df)
```

```
## Rows: 10,000
## Columns: 48
## $ dass_1           <dbl> 3, 1, 2, 3, 3, 3, 1, 3, 0, 1, 3, 0, 3, 2, 1, 2, 1, 1,~
## $ dass_2           <dbl> 1, 0, 3, 0, 0, 3, 0, 0, 0, 1, 0, 0, 0, 2, 1, 3, 0, 1,~
## $ dass_3           <dbl> 1, 1, 3, 2, 3, 1, 1, 1, 0, 1, 2, 1, 2, 3, 1, 3, 1, 0,~
## $ dass_4           <dbl> 1, 3, 3, 0, 0, 0, 0, 1, 2, 0, 1, 0, 0, 2, 1, 3, 1, 1,~
## $ dass_5           <dbl> 1, 2, 1, 3, 3, 3, 1, 1, 1, 0, 3, 1, 1, 3, 1, 3, 0, 1,~
## $ dass_6           <dbl> 1, 0, 1, 2, 3, 3, 1, 3, 1, 0, 1, 0, 3, 2, 1, 1, 0, 0,~
## $ dass_7           <dbl> 0, 0, 2, 0, 0, 0, 0, 3, 1, 0, 1, 0, 0, 1, 0, 2, 0, 0,~
## $ dass_8           <dbl> 2, 3, 3, 3, 3, 3, 1, 1, 1, 1, 2, 1, 2, 2, 0, 2, 0, 1,~
## $ dass_9           <dbl> 1, 3, 3, 1, 1, 3, 0, 1, 1, 0, 2, 1, 1, 2, 1, 1, 0, 1,~
## $ dass_10          <dbl> 1, 3, 2, 3, 3, 1, 0, 2, 0, 0, 2, 0, 0, 3, 1, 2, 1, 0,~
## $ dass_11          <dbl> 3, 1, 3, 3, 3, 3, 1, 3, 0, 1, 3, 0, 3, 2, 1, 0, 3, 1,~
## $ dass_12          <dbl> 3, 3, 2, 2, 3, 0, 0, 3, 2, 0, 2, 1, 0, 2, 1, 2, 1, 2,~
## $ dass_13          <dbl> 3, 3, 3, 3, 3, 3, 1, 3, 2, 1, 2, 1, 1, 3, 1, 2, 1, 0,~
## $ dass_14          <dbl> 1, 1, 3, 0, 0, 3, 1, 2, 2, 0, 0, 1, 1, 2, 0, 3, 0, 1,~
## $ dass_15          <dbl> 0, 3, 2, 3, 0, 3, 2, 0, 0, 0, 1, 0, 1, 1, 1, 1, 0, 1,~
## $ dass_16          <dbl> 1, 3, 3, 2, 3, 3, 0, 2, 1, 1, 2, 1, 2, 3, 1, 2, 0, 0,~
## $ dass_17          <dbl> 1, 2, 2, 3, 3, 1, 1, 3, 1, 1, 3, 0, 1, 3, 0, 2, 0, 0,~
## $ dass_18          <dbl> 3, 1, 2, 3, 3, 0, 1, 3, 1, 0, 2, 1, 0, 1, 1, 2, 0, 1,~
## $ dass_19          <dbl> 2, 0, 3, 0, 0, 0, 0, 3, 2, 0, 1, 0, 0, 0, 0, 2, 1, 0,~
## $ dass_20          <dbl> 3, 2, 3, 2, 3, 0, 0, 2, 1, 0, 2, 0, 1, 2, 3, 0, 2, 1,~
## $ dass_21          <dbl> 0, 3, 2, 3, 2, 0, 0, 3, 0, 1, 3, 1, 1, 3, 1, 1, 0, 0,~
## $ dass_22          <dbl> 1, 1, 2, 0, 3, 3, 0, 2, 1, 0, 2, 2, 2, 2, 1, 3, 0, 2,~
## $ dass_23          <dbl> 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 2, 1, 0,~
## $ dass_24          <dbl> 1, 3, 3, 3, 3, 3, 1, 2, 1, 0, 3, 1, 2, 2, 2, 3, 0, 0,~
## $ dass_25          <dbl> 3, 3, 2, 0, 0, 3, 0, 1, 0, 0, 1, 1, 0, 2, 0, 2, 1, 0,~
## $ dass_26          <dbl> 1, 3, 3, 3, 3, 3, 1, 3, 1, 0, 3, 1, 1, 3, 1, 3, 1, 0,~
## $ dass_27          <dbl> 1, 1, 2, 3, 3, 0, 2, 3, 1, 1, 3, 1, 3, 2, 1, 2, 2, 1,~
## $ dass_28          <dbl> 3, 2, 3, 0, 1, 3, 0, 1, 2, 0, 1, 0, 1, 3, 2, 1, 2, 0,~
## $ dass_29          <dbl> 3, 0, 3, 3, 3, 3, 1, 3, 1, 1, 2, 0, 2, 1, 1, 1, 2, 0,~
## $ dass_30          <dbl> 3, 0, 2, 2, 3, 2, 0, 3, 0, 0, 3, 1, 2, 2, 2, 1, 0, 0,~
## $ dass_31          <dbl> 0, 2, 3, 3, 3, 2, 1, 2, 0, 0, 3, 0, 2, 3, 1, 2, 0, 0,~
## $ dass_32          <dbl> 3, 2, 3, 0, 3, 3, 0, 1, 1, 0, 1, 1, 3, 2, 3, 0, 0, 1,~
## $ dass_33          <dbl> 3, 2, 3, 3, 3, 0, 0, 3, 2, 0, 1, 1, 0, 2, 1, 2, 2, 1,~
## $ dass_34          <dbl> 1, 3, 2, 3, 3, 1, 0, 3, 1, 1, 3, 0, 1, 3, 1, 3, 0, 0,~
## $ dass_35          <dbl> 1, 1, 1, 2, 3, 1, 0, 1, 0, 0, 1, 0, 2, 1, 1, 2, 0, 1,~
## $ dass_36          <dbl> 1, 2, 2, 3, 3, 0, 0, 3, 1, 0, 2, 1, 1, 2, 1, 0, 0, 0,~
## $ dass_37          <dbl> 1, 3, 2, 3, 3, 0, 0, 3, 0, 0, 2, 0, 2, 2, 1, 3, 0, 0,~
## $ dass_38          <dbl> 1, 3, 2, 1, 2, 0, 0, 3, 0, 1, 3, 3, 3, 3, 1, 1, 0, 0,~
## $ dass_39          <dbl> 1, 1, 2, 2, 3, 2, 0, 1, 1, 0, 2, 1, 2, 2, 1, 1, 0, 1,~
## $ dass_40          <dbl> 3, 1, 3, 3, 3, 3, 0, 3, 0, 0, 3, 1, 1, 3, 3, 1, 0, 0,~
## $ dass_41          <dbl> 1, 0, 1, 0, 0, 0, 0, 3, 1, 0, 1, 0, 0, 2, 0, 2, 1, 1,~
## $ dass_42          <dbl> 3, 3, 2, 3, 3, 3, 2, 0, 1, 1, 3, 2, 1, 3, 1, 2, 0, 1,~
## $ depression_score <dbl> 22, 27, 32, 24, 30, 24, 5, 28, 12, 4, 25, 8, 13, 32, ~
## $ anxiety_score    <dbl> 24, 26, 32, 32, 36, 29, 9, 32, 10, 7, 31, 9, 24, 34, ~
## $ stress_score     <dbl> 21, 21, 34, 25, 27, 18, 6, 28, 12, 3, 25, 10, 17, 23,~
## $ depression_class <fct> Severe, Severe, Highly severe, Severe, Highly severe,~
## $ anxiety_class    <fct> Highly severe, Highly severe, Highly severe, Highly s~
## $ stress_class     <fct> Moderate, Moderate, Highly severe, Severe, Severe, Mi~
```

Now, whenever we have an additional change we want to make to our dataframe, we
do it directly in the `clean_data()` function.
