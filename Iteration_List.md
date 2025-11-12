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

step3: let’s use alternative way, map

``` r
output2 = map(list_norms, mean_and_sd)
```

### What if u want to do different function

``` r
output3 = map(list_norms, median)
```

### Make a dataframe

Purpose of .id: It is typically used with functions that return a data
frame (map_dfr()) to create a new column in the resulting data frame.
This column records the name or index of the element from the original
input list (list_norms) that produced that row of data.

``` r
output4 = map_dbl(list_norms, median, .id = "input")
output5 = map_dfr(list_norms, mean_and_sd, .id = "input")
output6 = map_df(list_norms, mean_and_sd)
output7 = map_df(list_norms, mean_and_sd, .id = "ID")
```

# List columns and operations

``` r
listcol_df = 
  tibble(
    name = c("a", "b", "c", "d"),
    samp = list_norms
  )
```

``` r
listcol_df |> pull(name)
```

    ## [1] "a" "b" "c" "d"

``` r
listcol_df |> pull(samp)
```

    ## $a
    ##  [1] 2.379633 3.042116 2.089078 3.158029 2.345415 4.767287 3.716707 3.910174
    ##  [9] 3.384185 4.682176 2.364264 2.538355 4.432282 2.349304 2.792619 2.607192
    ## [17] 2.680007 2.720887 3.494188 2.822670
    ## 
    ## $b
    ##  [1] -2.5297873  6.7151941 -1.0728970 -0.8977827 -0.5009537  3.5633315
    ##  [7] -0.3678220 -0.1881709 -3.4083024 -1.6213514  0.3008022 -2.9444724
    ## [13]  2.6574810 -7.5919704  1.5327893 -7.6822491 -1.5048806 -2.6413995
    ## [19] -3.2604739 -0.2844839
    ## 
    ## $c
    ##  [1]  9.617128 10.235317  9.667006  9.907294  9.776816  9.849836 10.417433
    ##  [8] 10.003479  9.742740  9.671879 10.090037  9.996288  9.936386  9.814128
    ## [15]  9.702508  9.784962 10.200006  9.875747  9.723115 10.373858
    ## 
    ## $d
    ##  [1] -2.5748996 -3.2386471 -1.9415170 -2.1135773 -3.6192430 -0.7938975
    ##  [7] -3.2550270 -4.4244947 -3.1443996 -2.7924617 -0.6920216 -2.8941976
    ## [13] -2.5430012 -3.0771529 -3.3340008 -3.0347260 -2.2123604 -0.9247550
    ## [19] -1.9726076 -1.7920916

# Operations

``` r
pull(listcol_df, samp)[[1]]
```

    ##  [1] 2.379633 3.042116 2.089078 3.158029 2.345415 4.767287 3.716707 3.910174
    ##  [9] 3.384185 4.682176 2.364264 2.538355 4.432282 2.349304 2.792619 2.607192
    ## [17] 2.680007 2.720887 3.494188 2.822670

``` r
mean_and_sd(pull(listcol_df, samp)[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.11 0.814

``` r
median(pull(listcol_df, samp)[[1]])
```

    ## [1] 2.807644

Use alternative way to calculate the whole list, map

``` r
map(pull(listcol_df, samp), mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.11 0.814
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -1.09  3.34
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.92 0.237
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.52 0.974

I would like to add output into my dataset

``` r
listcol_df = 
  listcol_df |> 
  mutate(summary = map(samp, mean_and_sd))

listcol_df
```

    ## # A tibble: 4 × 3
    ##   name  samp         summary         
    ##   <chr> <named list> <named list>    
    ## 1 a     <dbl [20]>   <tibble [1 × 2]>
    ## 2 b     <dbl [20]>   <tibble [1 × 2]>
    ## 3 c     <dbl [20]>   <tibble [1 × 2]>
    ## 4 d     <dbl [20]>   <tibble [1 × 2]>
