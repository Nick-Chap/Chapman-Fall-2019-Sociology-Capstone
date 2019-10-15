Create Clean Data
================
Nick chapman
(October 07, 2019)

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

## Load Data

This notebook requires data downloaded, saved to the `data/raw/` folder,
as well as data downloaded from `tigris` and `tidycensus`:

### Load Population Data

`B99187_001` is the variable for: “Estimate\!\!Total”, I will use this
to get a Population for each county in Missouri, as well as a Population
Density:

``` r
MO_population <- get_acs(geography = "county", state = "MO", year = 2015, variable = "B99187_001", survey = "acs5")
```

    ## Getting data from the 2011-2015 5-year ACS

### Load Income Data

`B19326_001` is the variable for: “Median income in the past 12 months
(in 2017 inflation-adjusted
dollars)”:

``` r
MO_Income <- get_acs(geography = "county", state = "MO", year = 2015, variable = "B19326_001", survey = "acs5")
```

    ## Getting data from the 2011-2015 5-year ACS

### Load Heart Attack Data

This heart attack data was downloaded from
Mophim:

``` r
Heart_Attack_MO<- read_csv(here("data", "raw", "Heart_Attack_Raw_MOcounty.csv"))
```

    ## Warning: Missing column names filled in: 'X6' [6]

    ## Parsed with column specification:
    ## cols(
    ##   Geography = col_character(),
    ##   `FIPS Code` = col_character(),
    ##   Rank = col_character(),
    ##   `Mapped Value` = col_character(),
    ##   Count = col_character(),
    ##   X6 = col_logical()
    ## )

### Load County Health Ranking Data

``` r
county_rankings<- read_csv(here("data", "raw", "county_rankings.csv"))
```

    ## Warning: Missing column names filled in: 'X1' [1], 'X2' [2], 'X3' [3],
    ## 'X5' [5], 'X6' [6], 'X7' [7], 'X8' [8], 'X9' [9], 'X10' [10], 'X12' [12],
    ## 'X13' [13], 'X14' [14], 'X16' [16], 'X17' [17], 'X18' [18], 'X20' [20],
    ## 'X21' [21], 'X22' [22], 'X24' [24], 'X25' [25], 'X26' [26], 'X27' [27],
    ## 'X28' [28], 'X29' [29], 'X30' [30], 'X32' [32], 'X33' [33], 'X34' [34],
    ## 'X36' [36], 'X37' [37], 'X38' [38], 'X40' [40], 'X42' [42], 'X43' [43],
    ## 'X44' [44], 'X46' [46], 'X48' [48], 'X49' [49], 'X50' [50], 'X52' [52],
    ## 'X53' [53], 'X54' [54], 'X55' [55], 'X56' [56], 'X58' [58], 'X59' [59],
    ## 'X61' [61], 'X62' [62], 'X63' [63], 'X64' [64], 'X65' [65], 'X66' [66],
    ## 'X68' [68], 'X69' [69], 'X70' [70], 'X71' [71], 'X73' [73], 'X74' [74],
    ## 'X75' [75], 'X77' [77], 'X78' [78], 'X79' [79], 'X81' [81], 'X82' [82],
    ## 'X83' [83], 'X85' [85], 'X86' [86], 'X87' [87], 'X88' [88], 'X90' [90],
    ## 'X91' [91], 'X92' [92], 'X93' [93], 'X95' [95], 'X96' [96], 'X97' [97],
    ## 'X98' [98], 'X100' [100], 'X101' [101], 'X103' [103], 'X104' [104],
    ## 'X105' [105], 'X106' [106], 'X107' [107], 'X109' [109], 'X110' [110],
    ## 'X111' [111], 'X113' [113], 'X114' [114], 'X115' [115], 'X116' [116],
    ## 'X117' [117], 'X118' [118], 'X120' [120], 'X121' [121], 'X122' [122],
    ## 'X124' [124], 'X125' [125], 'X126' [126], 'X127' [127], 'X128' [128],
    ## 'X130' [130], 'X131' [131], 'X133' [133], 'X134' [134], 'X136' [136],
    ## 'X137' [137], 'X138' [138], 'X139' [139], 'X141' [141], 'X143' [143],
    ## 'X145' [145], 'X146' [146], 'X147' [147], 'X148' [148], 'X149' [149],
    ## 'X150' [150], 'X152' [152], 'X153' [153], 'X154' [154], 'X155' [155],
    ## 'X156' [156], 'X157' [157], 'X159' [159], 'X160' [160], 'X161' [161],
    ## 'X162' [162]

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character()
    ## )

    ## See spec(...) for full column specifications.

## Cleaning Variables

I will first clean up my tables, removing unnecessary variables:

### Clean Heart Attack Data

For the heart attack data,`Mapped Value`, `X6`, and `Rank` are
unnecessary, for I will rank them myself
later:

``` r
Heart_Attack_Clean<- select(Heart_Attack_MO, -"X6", -"Rank", -"Mapped Value" )
```

Now, I want to rename `FIPS Code` to `GEOID` and make it the same value
as the income and population
data:

``` r
Heart_Attack_Clean <- rename(Heart_Attack_Clean, GEOID = "FIPS Code")
```

``` r
Heart_Attack_Clean <-  transform(Heart_Attack_Clean, GEOID = as.numeric(GEOID))
```

``` r
Heart_Attack_Clean <- mutate(Heart_Attack_Clean, GEOID = GEOID +29000)
```

### Clean Income Data

For the Income data, `variable` and `moe` are unnecessary and I will
remove them:

``` r
MO_Income_Clean<- select(MO_Income, -"variable", -"moe")
```

### Clean Population Data

For the population data, `variable` is unnecessary and I will remove
it:

``` r
MO_population_Clean<- select(MO_population, -"variable")
```

### Clean County Ranking Data

``` r
County_Rankings_Clean <- select(county_rankings, "X1", "X3", "Adult smoking", "Adult obesity", "Food environment index", "Physical inactivity", "Access to exercise opportunities")
```

``` r
County_Rankings_Clean <- rename(County_Rankings_Clean, GEOID = "X1")
County_Rankings_Clean <- rename(County_Rankings_Clean, County = "X3")
County_Rankings_Clean <- County_Rankings_Clean[-c(1,2),]
```

Now, all of my tables have the same ‘GEOID’, all of the county level
data could now be joined together.
