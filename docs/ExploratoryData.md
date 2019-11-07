Exploratory Data
================
Nick chapman
(November 05, 2019)

## Introduction

This notebook contains the code to download demographic data from
`tidycensus` for the State of Missouri, a `.shp` file of the counties of
Missouri from `tigris`, read .csv files from Missouri’s County Health
Rankings, and Mophims and to clean the data from each of them.

## Dependencies

This notebook requires a few packages:

``` r
# tidyverse packages
library(tidycensus)
library(tigris)
```

    ## To enable 
    ## caching of data, set `options(tigris_use_cache = TRUE)` in your R script or .Rprofile.

    ## 
    ## Attaching package: 'tigris'

    ## The following object is masked from 'package:graphics':
    ## 
    ##     plot

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(readr)
library(here)
```

    ## here() starts at C:/Users/nickc/OneDrive/Documents/GitHub/Chapman-Fall-2019-Sociology-Capstone

``` r
library(naniar)
library(janitor)
```

    ## 
    ## Attaching package: 'janitor'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
library(sf)
```

    ## Linking to GEOS 3.6.1, GDAL 2.2.3, PROJ 4.9.3
