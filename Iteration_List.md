Iteration and List Columns
================
Te-Hsuan Huang
2025-11-10

# Loading

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
set.seed(1)
```

# List

u can put anything in the list

``` r
bunch=list(
vec_numeric = 5:8,
vec_char = c("My", "name", "is", "Jeff"),
vec_logical = c(TRUE, TRUE, TRUE, FALSE),
mat=matrix(1:8, nrow=4, ncol=2),
summary=summary(rnorm(100))
)

bunch
```

    ## $vec_numeric
    ## [1] 5 6 7 8
    ## 
    ## $vec_char
    ## [1] "My"   "name" "is"   "Jeff"
    ## 
    ## $vec_logical
    ## [1]  TRUE  TRUE  TRUE FALSE
    ## 
    ## $mat
    ##      [,1] [,2]
    ## [1,]    1    5
    ## [2,]    2    6
    ## [3,]    3    7
    ## [4,]    4    8
    ## 
    ## $summary
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -2.2147 -0.4942  0.1139  0.1089  0.6915  2.4016

# output the dataset

``` r
bunch$vec_numeric
```

    ## [1] 5 6 7 8

``` r
bunch[[1]]
```

    ## [1] 5 6 7 8

``` r
bunch[["vec_numeric"]]
```

    ## [1] 5 6 7 8

``` r
bunch[["vec_numeric"]][1:2]
```

    ## [1] 5 6

``` r
mean(bunch[["vec_numeric"]])
```

    ## [1] 6.5

# for loops

step1: create a new list

``` r
list_norms = 
  list(
    a = rnorm(20, 3, 1),
    b = rnorm(20, 0, 5),
    c = rnorm(20, 10, .2),
    d = rnorm(20, -3, 1)
  )
```

step2: build the function

``` r
mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) == 1) {
    stop("Cannot be computed for length 1 vectors")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)

  tibble(
    mean = mean_x, 
    sd = sd_x
  )
}
```

step3: I can apply that function to each list element.

``` r
mean_and_sd(list_norms[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.11 0.814

``` r
mean_and_sd(list_norms[[2]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -1.09  3.34

step3: let’s use alternative way, for loop

``` r
output = vector("list", length = 4)

for (i in 1:4) {
  output[[i]] = mean_and_sd(list_norms[[i]])
}
```
