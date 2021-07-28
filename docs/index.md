---
title: "Missing Values in R"
author: "Fang Fang"
knit: (function(input_file, encoding) {
  out_dir <- 'docs';
  rmarkdown::render(input_file,
 encoding=encoding,
 output_file=file.path(dirname(input_file), out_dir, 'index.html'))})
output: 
  html_document: 
    keep_md: yes
---
# Missing values basics

R supports different type of missing values: 1) NULL; 2) NA; 3) NaN; 4)Inf / -Inf.

- **NULL** : undefined value. 

- **NA**: logical constant of length 1, which indicate a missing value. NA stands for not available. 

- **NaN**: Not a number, logical vector of length 1 for numerical values. For example: 0 divided by 0 is not a number. 

  

```r
0/0
```

```
## [1] NaN
```

- **Inf/-Inf** : Large number or a product that is a result of division by zero. For example, below we have 1 divided by 0 is **inf**. 


```r
1/0
```

```
## [1] Inf
```


# Missing value functions

One of the common tasks for data manipulation in R is handling missing values. It is also very common to have missing values in your datasets. Here we will go over a few options to handle missing values. 

## 1. Identify missing valus

It is time-consuming to do a visual check on missing value identification. You can test if the value is a missing value using the `is.na()` function. 

For example, there are two missing values in x. `is.na` will return you a logical variable, where 'TRUE' means it is a missing value, 'FALSE' means it is not a missing value.


```r
x <- c(1,NA,2, NA)
is.na(x)
```

```
## [1] FALSE  TRUE FALSE  TRUE
```

You can use this logical variable to create an updated variable without missing values. The `!` will take the opposite results, so `!is.na()` will help to preserve values that are not missing. 


```r
update_x <- x[!is.na(x)]
print(update_x)
```

```
## [1] 1 2
```


## 2. Exclude missing values

There are a few options to exclude missing values for further calculation. 

1. Remove NAs: `na.rm=T`

Usually we use it within a mathematical function. For example, to calculate the average of x, `mean(x)` will return you NA since there are missing values. 
The `na.rm=T` means remove NA is true. So let's try to put it inside the `mean` function, the missing values will be excluded for calculation. 


```r
# mean(x) returns NA
mean(x)
```

```
## [1] NA
```

```r
# Exclude NA inside the mean function
mean(x, na.rm = TRUE)
```

```
## [1] 1.5
```



2. Drop rows: `drop_na()`

Alternatively, in a data frame you can drop rows which have missing values using the `drop_na()` function. Please note you need to load the `tidyverse` package before using the function

For example, the data frame below has missing value on the 3rd row. `drop_na` will drop the 3rd row in the dataset. 


```r
library(tidyverse)
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.5     v purrr   0.3.4
## v tibble  3.1.2     v dplyr   1.0.7
## v tidyr   1.1.3     v stringr 1.4.0
## v readr   1.4.0     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
example <- tibble(x = c(1, 2, NA), y = c("a", "b", "c"))
print(example)
```

```
## # A tibble: 3 x 2
##       x y    
##   <dbl> <chr>
## 1     1 a    
## 2     2 b    
## 3    NA c
```

```r
example %>% drop_na()
```

```
## # A tibble: 2 x 2
##       x y    
##   <dbl> <chr>
## 1     1 a    
## 2     2 b
```
3. Replace missing values

Usually we use `NA` to label a missing value. However, sometimes in the raw dataset, you might see missing values labeled as `?` or 'No data' instead of `NA`s. In this case, you can manually convert these values into `NA`s. 


```r
example2 <- tibble(x = c(1, 2, "?"), y = c("a", "?", "c"))
print(example2)
```

```
## # A tibble: 3 x 2
##   x     y    
##   <chr> <chr>
## 1 1     a    
## 2 2     ?    
## 3 ?     c
```

```r
example2[example2=="?"] <- NA
print(example2)
```

```
## # A tibble: 3 x 2
##   x     y    
##   <chr> <chr>
## 1 1     a    
## 2 2     <NA> 
## 3 <NA>  c
```

4. Replace missing values with previous or next value: `fill()`

You can use the `fill()` function in the `tidyverse` package to fill the missing values using the next or previous entry. For example Value (State) is recorded only when it changes. The 4th and 5th row should be "AL" and the last two row should be "IL". 


```r
pop <- tibble::tribble(
  ~Year, ~State, ~million_population,
  2000,    "AL",    4.38,
  2001,    "AL",    4.4,
  2002,    NA,    4.47,
  2003,    NA,    4.49,
  2000,    "IL",    12.19,
  2001,    "IL",    12.51,
  2002,    NA,    12.56,
  2003,    NA,    12.6
)

# Fill missing values downward by previous value. 
pop %>% fill(State, .direction = "down")
```

```
## # A tibble: 8 x 3
##    Year State million_population
##   <dbl> <chr>              <dbl>
## 1  2000 AL                  4.38
## 2  2001 AL                  4.4 
## 3  2002 AL                  4.47
## 4  2003 AL                  4.49
## 5  2000 IL                 12.2 
## 6  2001 IL                 12.5 
## 7  2002 IL                 12.6 
## 8  2003 IL                 12.6
```




