# Reproducible Research: Peer Assessment 2


# **Title**

## Types of weather events that are most harmful with respect to population health and with respect to economic consequences

# **Synopsis**

In this article we want to study what types of weather events that are most harmful with respect to population health and with respect to economic consequences. First, we'll download the NOAA Storm Data CSV file from his website for processing. This processing involves an initial exploratory analysis that checks what variables are more relevant for us, and discarding the other variables that are not as relevant to our study. Also, the cleaning process check the event type variable of the data and grouping the similar events types to get a more uniform dataset. Then, we'll do a summary of our health and economic datasets and present the results showing the data of our datasets in text format and plotting them into 2 plots within a grid panel style. Finally, we'll present a final conclusion for our health and economic results.

The following is an index of the steps that we have taken:

              # Title
              # Synopsys
              # Data Processing
                * Loading and preprocessing the data
                  - 1. Downloading/loading "Storm Data" file and reading the CSV file
                  - 2. Exploratory analysis
                  - 3. Cleaning the Data
                  - 4. Summarizing the Data
              # Results
                * Show the final data and plotting Health results and conclusions
                * Show the final data and plotting Economic results and conclusions
                    
# **Data Processing**


## Loading and preprocessing the data
  
### 1. Downloading/loading "Storm Data" file and reading the CSV file


```r
# suppressPackageStartupMessages(library(data.table))

## I need to change my LOCALE (spanish) to English...
Sys.setlocale('LC_TIME', 'en_US.UTF-8')
```

```
## [1] "en_US.UTF-8"
```

```r
compressedFileName <- "repdata_data_StormData.csv.bz2"

URL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"


# If file doesn't exist in current working directory, I try to download from website
if(!file.exists(compressedFileName))
    download.file(URL, compressedFileName)

# If the file doesn't exist in current working directory after downloading, there are any
# problem downloading this file, or there are any other problem...aborting
if(!file.exists(compressedFileName))    
    stop("Required file doesn't exist in the current working directory! Aborting...")
    
# loading data
data <- read.csv(compressedFileName)

# Get the column names and check if they are properly formatted
colnames(data) <- make.names(colnames(data))
```


```r
head(data)
```

```
##   STATE__           BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE
## 1       1  4/18/1950 0:00:00     0130       CST     97     MOBILE    AL
## 2       1  4/18/1950 0:00:00     0145       CST      3    BALDWIN    AL
## 3       1  2/20/1951 0:00:00     1600       CST     57    FAYETTE    AL
## 4       1   6/8/1951 0:00:00     0900       CST     89    MADISON    AL
## 5       1 11/15/1951 0:00:00     1500       CST     43    CULLMAN    AL
## 6       1 11/15/1951 0:00:00     2000       CST     77 LAUDERDALE    AL
##    EVTYPE BGN_RANGE BGN_AZI BGN_LOCATI END_DATE END_TIME COUNTY_END
## 1 TORNADO         0                                               0
## 2 TORNADO         0                                               0
## 3 TORNADO         0                                               0
## 4 TORNADO         0                                               0
## 5 TORNADO         0                                               0
## 6 TORNADO         0                                               0
##   COUNTYENDN END_RANGE END_AZI END_LOCATI LENGTH WIDTH F MAG FATALITIES
## 1         NA         0                      14.0   100 3   0          0
## 2         NA         0                       2.0   150 2   0          0
## 3         NA         0                       0.1   123 2   0          0
## 4         NA         0                       0.0   100 2   0          0
## 5         NA         0                       0.0   150 2   0          0
## 6         NA         0                       1.5   177 2   0          0
##   INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP WFO STATEOFFIC ZONENAMES
## 1       15    25.0          K       0                                    
## 2        0     2.5          K       0                                    
## 3        2    25.0          K       0                                    
## 4        2     2.5          K       0                                    
## 5        2     2.5          K       0                                    
## 6        6     2.5          K       0                                    
##   LATITUDE LONGITUDE LATITUDE_E LONGITUDE_ REMARKS REFNUM
## 1     3040      8812       3051       8806              1
## 2     3042      8755          0          0              2
## 3     3340      8742          0          0              3
## 4     3458      8626          0          0              4
## 5     3412      8642          0          0              5
## 6     3450      8748          0          0              6
```

### 2. Exploratory analysis 

There is some documentation of this dataset available [here](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf). 
More information can be obtained from the [NOAA Storm Events Database](http://www.ncdc.noaa.gov/stormevents/ftp.jsp). 

There are **902297 rows** or observations and **37 columns** or variables in this dataset.

Reading these two websites above and, for the topic of this paper, we can subset the *data* CSV file extracting only the following columns or variables:   

* BGN_DATE (event start date)
* STATE (the USA state where event occurs)
* EVTYPE (climatic event)
* FATALITIES (numerical value for an occurrence of death)
* INJURIES (numerical value for non fatal damage, hurt, harm, ...)
* PROPDMG (numerical value for property damage)
* PROPDMGEXP (exponent -symbol/value- for adjusting PROPDMG value)
* CROPDMG (numerical value for crop damage)
* CROPDMGEXP (exponent -symbol/value- for adjusting CROPDMG value)

The others columns or variables aren't important for the analysis in this article.

Now, we are going to subset the dataset.


```r
suppressPackageStartupMessages(library(dplyr))

# select only the variables we are interested in
sub_data <- data %>% select(BGN_DATE, STATE, EVTYPE, FATALITIES, INJURIES, PROPDMG, PROPDMGEXP,CROPDMG, CROPDMGEXP)

# For saving computer resources, we can remove the initial dataset
rm(data)

# now, we have a smaller dataset
head(sub_data)
```

```
##             BGN_DATE STATE  EVTYPE FATALITIES INJURIES PROPDMG PROPDMGEXP
## 1  4/18/1950 0:00:00    AL TORNADO          0       15    25.0          K
## 2  4/18/1950 0:00:00    AL TORNADO          0        0     2.5          K
## 3  2/20/1951 0:00:00    AL TORNADO          0        2    25.0          K
## 4   6/8/1951 0:00:00    AL TORNADO          0        2     2.5          K
## 5 11/15/1951 0:00:00    AL TORNADO          0        2     2.5          K
## 6 11/15/1951 0:00:00    AL TORNADO          0        6     2.5          K
##   CROPDMG CROPDMGEXP
## 1       0           
## 2       0           
## 3       0           
## 4       0           
## 5       0           
## 6       0
```
At this moment, There are **902297 rows** or observations and **9 columns** or variables in *sub_data* dataset.
For this analysis we want to study types of weather events that are most harmful with respect to population health and with respect to economic consequences, so we want dataset observations with values over "0" (zero) in the following variables: FATALITIES, INJURIES, PROPDMG and CROPDMG.


```r
sub_data <- sub_data %>% filter(FATALITIES > 0 | INJURIES > 0 | PROPDMG > 0 | CROPDMG > 0)
```

Now, there are **254633 rows** or observations and **9 columns** or variables in *sub_data* dataset. We have reduced the data size significantly.


```r
#summary of our dataset
summary(sub_data)
```

```
##               BGN_DATE          STATE                      EVTYPE     
##  4/27/2011 0:00:00:   761   TX     : 22144   TSTM WIND        :63234  
##  4/4/2011 0:00:00 :   649   IA     : 16093   THUNDERSTORM WIND:43655  
##  6/21/2011 0:00:00:   511   OH     : 13337   TORNADO          :39944  
##  5/31/1998 0:00:00:   504   MS     : 12023   HAIL             :26130  
##  6/4/2008 0:00:00 :   486   GA     : 11207   FLASH FLOOD      :20967  
##  5/25/2011 0:00:00:   440   AL     : 11121   LIGHTNING        :13293  
##  (Other)          :251282   (Other):168708   (Other)          :47410  
##    FATALITIES          INJURIES            PROPDMG          PROPDMGEXP    
##  Min.   :  0.0000   Min.   :   0.0000   Min.   :   0.00   K      :231428  
##  1st Qu.:  0.0000   1st Qu.:   0.0000   1st Qu.:   2.00          : 11585  
##  Median :  0.0000   Median :   0.0000   Median :   5.00   M      : 11320  
##  Mean   :  0.0595   Mean   :   0.5519   Mean   :  42.75   0      :   210  
##  3rd Qu.:  0.0000   3rd Qu.:   0.0000   3rd Qu.:  25.00   B      :    40  
##  Max.   :583.0000   Max.   :1700.0000   Max.   :5000.00   5      :    18  
##                                                           (Other):    32  
##     CROPDMG          CROPDMGEXP    
##  Min.   :  0.000          :152664  
##  1st Qu.:  0.000   K      : 99932  
##  Median :  0.000   M      :  1985  
##  Mean   :  5.411   k      :    21  
##  3rd Qu.:  0.000   0      :    17  
##  Max.   :990.000   B      :     7  
##                    (Other):     7
```

### 3. Cleaning the Data

In the official documentation, [here](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf), we can read that there are 48 event types (chapter 7, points from 7.1 to 7.48, or the Storm Data Event Table in the page 6 of the above PDF document), and we have 985 event types in our dataset. We'll need to transform and reduce them. 

In first place, we are going to know how many ocurrences of each event type there are in our *sub_data* dataset.


```r
summary(sub_data$EVTYPE)
```

```
##                TSTM WIND        THUNDERSTORM WIND                  TORNADO 
##                    63234                    43655                    39944 
##                     HAIL              FLASH FLOOD                LIGHTNING 
##                    26130                    20967                    13293 
##       THUNDERSTORM WINDS                    FLOOD                HIGH WIND 
##                    12086                    10175                     5522 
##              STRONG WIND             WINTER STORM               HEAVY SNOW 
##                     3370                     1508                     1342 
##               HEAVY RAIN                 WILDFIRE                ICE STORM 
##                     1105                      857                      708 
##     URBAN/SML STREAM FLD           EXCESSIVE HEAT               HIGH WINDS 
##                      702                      698                      657 
##           TSTM WIND/HAIL           TROPICAL STORM           WINTER WEATHER 
##                      441                      416                      407 
##              RIP CURRENT         WILD/FOREST FIRE           FLASH FLOODING 
##                      400                      388                      302 
##        FLOOD/FLASH FLOOD                AVALANCHE                  DROUGHT 
##                      279                      268                      266 
##                 BLIZZARD             RIP CURRENTS                     HEAT 
##                      253                      241                      215 
##             EXTREME COLD         LAKE-EFFECT SNOW                LANDSLIDE 
##                      197                      194                      193 
##              STORM SURGE            COASTAL FLOOD              URBAN FLOOD 
##                      177                      164                      139 
##       WINTER WEATHER/MIX                HIGH SURF                HURRICANE 
##                      139                      130                      129 
##               LIGHT SNOW             FROST/FREEZE  EXTREME COLD/WIND CHILL 
##                      119                      116                      111 
##         MARINE TSTM WIND                      FOG              RIVER FLOOD 
##                      109                      107                      107 
##               DUST STORM          COLD/WIND CHILL               DUST DEVIL 
##                      103                       90                       89 
##                     WIND           URBAN FLOODING           DRY MICROBURST 
##                       83                       80                       78 
##                DENSE FOG        HURRICANE/TYPHOON                 FLOODING 
##                       74                       72                       58 
##         COASTAL FLOODING                     SNOW     HEAVY SURF/HIGH SURF 
##                       55                       52                       50 
##             STRONG WINDS         STORM SURGE/TIDE               WATERSPOUT 
##                       50                       47                       47 
##       MARINE STRONG WIND  THUNDERSTORM WINDS HAIL          TSTM WIND (G45) 
##                       46                       40                       36 
##            FREEZING RAIN      TROPICAL DEPRESSION                HEAT WAVE 
##                       35                       35                       34 
##      THUNDERSTORM WINDSS MARINE THUNDERSTORM WIND                    OTHER 
##                       34                       33                       33 
##                     COLD               HEAVY SURF           EXCESSIVE SNOW 
##                       32                       30                       25 
##                      ICE                ICY ROADS      LIGHT FREEZING RAIN 
##                       24                       22                       22 
##  THUNDERSTORM WINDS/HAIL              GUSTY WINDS       HEAVY SNOW SQUALLS 
##                       22                       21                       21 
##               Light Snow              HEAVY RAINS        EXTREME WINDCHILL 
##                       21                       20                       19 
##                    GLAZE         MARINE HIGH WIND             THUNDERSTORM 
##                       19                       19                       19 
##                    WINDS             EXTREME HEAT        FLASH FLOOD/FLOOD 
##                       18                       17                       16 
##                   FREEZE              SNOW SQUALL       HEAVY SNOW-SQUALLS 
##                       16                       16                       15 
##      MIXED PRECIPITATION                  TSUNAMI             FLASH FLOODS 
##                       15                       14                       13 
##             FUNNEL CLOUD               GUSTY WIND           RIVER FLOODING 
##                       13                       13                       13 
##               SMALL HAIL   DROUGHT/EXCESSIVE HEAT              Gusty Winds 
##                       11                       10                       10 
##                  (Other) 
##                      773
```

In this summary, we can see that THUNDERSTORM WIND and other variations of the same climatic event are the more common (TSTM WIND, THUNDERSTORM WINDS, THUNDERSTORM WINDSS, THUNDERSTORM, TSTM WIND (G45), THUNDERSTORM WINDS/HAIL, WINDS, GUSTY WINDS). The idea is to group these events in one big group with name **THUNDERSTORM WIND**, like this event is named in the official documentation. We'll do the same process for others climatic events with similar variable name in our dataset. This way we reduce the dataset dimensions.

Also, there are duplicate event names, and the same event name in lowercase and in uppercase, so we'll transform all event names to uppercase, for grouping purpouses.


```r
# Transform all event names to UPPERCASE. This transform the column "as.character".
sub_data <- mutate(sub_data, EVTYPE = as.factor(toupper(EVTYPE)))
```


We are going to group similar climatic events that can be repetitive or similar events, like THUNDERSTORM WIND or TSTM WIND, for reduce this dataset. For this purpouse, the following "groupingFactorLevels" function replaces the terms passed in the "arrayNames" parameter with the term passed in the "keyName" parameter.


```r
groupingFactorLevels <- function(mydata, keyName, arrayNames) 
{
# searching by terms inside arrayNAmes parameter
toMatch <- arrayNames

# get the indices where there are matches
indices <- grep(paste(toMatch, collapse="|"), levels(mydata$EVTYPE))


# replacement matches with keyName parameter
levels(mydata$EVTYPE)[c(indices)] <- keyName

return(mydata)

}
```


We are going to use the above function for grouping the most important climatic events, that is, the most frequent climatic events (as you can see in: summary(sub_data$EVTYPE))


```r
toMatch <-c("TORNADO", "TORNADOES")
sub_data <- groupingFactorLevels(sub_data, c("TORNADO"), toMatch)

toMatch <- c("^THUNDERSTORM", "^TSTM", "^SEVERE THUNDERSTORM", "^THUNDERTORM WINDS", "^TSTM WIND", "TSTM WIND")
sub_data <- groupingFactorLevels(sub_data, c("THUNDERSTORM WIND"), toMatch)

toMatch <-c("^MARINE TSTM WIND", "^MARINE THUNDERSTORM WIND" )
sub_data <- groupingFactorLevels(sub_data, c("MARINE THUNDERSTORM WIND"), toMatch)

toMatch <-c("HURRICANE", "TYPHOON", "HURRICANE/TYPHOON")
sub_data <- groupingFactorLevels(sub_data, c("HURRICANE/TYPHOON"), toMatch)

toMatch <-c("TROPICAL STORM")
sub_data <- groupingFactorLevels(sub_data, c("TROPICAL STORM"), toMatch)


toMatch <-c("RIVER FLOOD", "URBAN/SMALL STREAM FLOOD", "URBAN FLOODING", "MAJOR FLOOD", 
            "URBAN FLOOD", "URBAN/SML STREAM FLD", "FLOOD/RAIN/WINDS", "^FLOODS$", 
            "FLOOD & HEAVY RAIN", "RIVER AND STREAM FLOOD")
sub_data <- groupingFactorLevels(sub_data, c("FLOOD"), toMatch)

toMatch <-c("FLASH FLOOD", "FLASH FLOODING", "FLOODING", "FLASH FLOODS", "FLOOD FLASH",
            "FLOOD/FLASH")
sub_data <- groupingFactorLevels(sub_data, c("FLASH FLOOD"), toMatch)

toMatch <-c("COASTAL FLOOD", "COASTAL", "BEACH EROSION", "EROSION/CSTL FLOOD", "CSTL")
sub_data <- groupingFactorLevels(sub_data, c("COASTAL FLOOD"), toMatch)


toMatch <-c("LIGHT FREEZING RAIN", "FREEZE", "FREEZING RAIN", "FROST/FREEZE", 
           "AGRICULTURAL FREEZE", "FREEZING RAIN/SNOW", "DAMAGING FREEZE", "FREEZING DRIZZLE", 
           "FROST", "SNOW FREEZING RAIN", "ICY", "FREEZING SPRAY")
sub_data <- groupingFactorLevels(sub_data, c("FROST/FREEZE"), toMatch)

toMatch <-c("^COLD$", "^WIND CHILD$", "$COLD/WIND CHILL$")
sub_data <- groupingFactorLevels(sub_data, c("COLD/WIND CHILL"), toMatch)

toMatch <-c("^EXTREME WIND CHILL", "RECORD COLD")
sub_data <- groupingFactorLevels(sub_data, c("EXTREME COLD/WIND CHILL"), toMatch)

toMatch <-c("SNOW") 
sub_data <- groupingFactorLevels(sub_data, c("HEAVY SNOW"), toMatch)

toMatch <-c("^HAIL") 
sub_data <- groupingFactorLevels(sub_data, c("HAIL"), toMatch)

toMatch <-c("MARINE HAIL") 
sub_data <- groupingFactorLevels(sub_data, c("MARINE HAIL"), toMatch)

# This new data type is to summarize... 
toMatch <-c("FOG") 
sub_data <- groupingFactorLevels(sub_data, c("DENSE/FREEZING FOG"), toMatch)

toMatch <-c("^HEAT") 
sub_data <- groupingFactorLevels(sub_data, c("HEAT"), toMatch)

toMatch <-c("^EXCESSIVE HEAT", "DROUGHT/EXCESSIVE HEAT", "RECORD HEAT") 
sub_data <- groupingFactorLevels(sub_data, c("EXCESSIVE HEAT"), toMatch)

#toMatch <-c("^DROUGHT") 
#sub_data <- groupingFactorLevels(sub_data, c("DROUGHT"), toMatch)

toMatch <-c("BLIZZARD") 
sub_data <- groupingFactorLevels(sub_data, c("BLIZZARD"), toMatch)

toMatch <-c("^EXTREME COLD", "EXTENDED COLD") 
sub_data <- groupingFactorLevels(sub_data, c("EXTREME COLD/WIND CHILL"), toMatch)

toMatch <-c("^COLD") 
sub_data <- groupingFactorLevels(sub_data, c("COLD/WIND CHILL"), toMatch)

toMatch <-c("^ICE STORM", "BLACK ICE", "GLAZE ICE", "ICE FLOES", "GLAZE/ICE STORM", "GLAZE") 
sub_data <- groupingFactorLevels(sub_data, c("ICE STORM"), toMatch)

toMatch <-c("^HIGH WIND", "^WIND", "GUSTY WIND", "GRADIENT WIND") 
sub_data <- groupingFactorLevels(sub_data, c("HIGH WIND"), toMatch)

toMatch <-c("^STRONG WIND") 
sub_data <- groupingFactorLevels(sub_data, c("STRONG WIND"), toMatch)

toMatch <-c("DUST DEVIL") 
sub_data <- groupingFactorLevels(sub_data, c("DUST DEVIL"), toMatch)

toMatch <-c("^DUST$", "BLOWING DUST") 
sub_data <- groupingFactorLevels(sub_data, c("DUST"), toMatch)

toMatch <-c("^RAIN", "HEAVY PRECIPITATION", "^HEAVY RAIN", "RAINFALL", "UNSEASONAL RAIN", "RAIN", "PRECIP") 
sub_data <- groupingFactorLevels(sub_data, c("HEAVY RAIN"), toMatch)

toMatch <-c("SMOKE") 
sub_data <- groupingFactorLevels(sub_data, c("DENSE SMOKE"), toMatch)

toMatch <-c("AVALACE") 
sub_data <- groupingFactorLevels(sub_data, c("AVALANCHE"), toMatch)



summary(sub_data$EVTYPE)
```

```
##        THUNDERSTORM WIND                  TORNADO                     HAIL 
##                   119779                    39970                    26151 
##              FLASH FLOOD                LIGHTNING                    FLOOD 
##                    21752                    13293                    11249 
##                HIGH WIND              STRONG WIND               HEAVY SNOW 
##                     6362                     3424                     1880 
##             WINTER STORM               HEAVY RAIN                 WILDFIRE 
##                     1508                     1171                      857 
##                ICE STORM           EXCESSIVE HEAT           TROPICAL STORM 
##                      735                      710                      421 
##           WINTER WEATHER              RIP CURRENT         WILD/FOREST FIRE 
##                      407                      400                      388 
##  EXTREME COLD/WIND CHILL                AVALANCHE                  DROUGHT 
##                      317                      268                      266 
##             FROST/FREEZE                 BLIZZARD                     HEAT 
##                      263                      257                      252 
##             RIP CURRENTS        HURRICANE/TYPHOON                LANDSLIDE 
##                      241                      233                      193 
##       DENSE/FREEZING FOG            COASTAL FLOOD              STORM SURGE 
##                      189                      179                      177 
##       WINTER WEATHER/MIX          COLD/WIND CHILL                HIGH SURF 
##                      139                      137                      136 
##               DUST STORM               DUST DEVIL           DRY MICROBURST 
##                      103                       96                       78 
##     HEAVY SURF/HIGH SURF         STORM SURGE/TIDE               WATERSPOUT 
##                       50                       47                       47 
##       MARINE STRONG WIND      TROPICAL DEPRESSION                    OTHER 
##                       46                       35                       34 
## MARINE THUNDERSTORM WIND               HEAVY SURF                      ICE 
##                       33                       32                       24 
##        EXTREME WINDCHILL         MARINE HIGH WIND             EXTREME HEAT 
##                       19                       19                       17 
##                  TSUNAMI             FUNNEL CLOUD               SMALL HAIL 
##                       14                       13                       11 
##                   SEICHE   ASTRONOMICAL HIGH TIDE                HEAVY MIX 
##                        9                        8                        8 
##                HIGH SEAS          LOW TEMPERATURE        UNSEASONABLY WARM 
##                        8                        7                        7 
##              WATERSPOUT-     HYPOTHERMIA/EXPOSURE                 MUDSLIDE 
##                        7                        6                        6 
##                 GUSTNADO          LAKESHORE FLOOD               HIGH WATER 
##                        5                        5                        4 
##               MICROBURST        UNSEASONABLY COLD               WILD FIRES 
##                        4                        4                        4 
##               WINTRY MIX               BRUSH FIRE               LANDSLIDES 
##                        4                        3                        3 
##                MUD SLIDE               ROUGH SEAS           WET MICROBURST 
##                        3                        3                        3 
##                WHIRLWIND                WILDFIRES    ASTRONOMICAL LOW TIDE 
##                        3                        3                        2 
##                DAM BREAK               HEAVY SEAS              HIGH SWELLS 
##                        2                        2                        2 
##                LANDSPOUT              MARINE HAIL            MARINE MISHAP 
##                        2                        2                        2 
##                MUDSLIDES  RIP CURRENTS/HEAVY SURF               ROUGH SURF 
##                        2                        2                        2 
##             VOLCANIC ASH       WINTER WEATHER MIX                        ? 
##                        2                        2                        1 
##            APACHE COUNTY                 AVALANCE                     DUST 
##                        1                        1                        1 
##             COOL AND WET              DENSE SMOKE                DOWNBURST 
##                        1                        1                        1 
##                 DROWNING     DRY MIRCOBURST WINDS    DUST STORM/HIGH WINDS 
##                        1                        1                        1 
##        EXCESSIVE WETNESS             FOREST FIRES              GRASS FIRES 
##                        1                        1                        1 
##                  (Other) 
##                       57
```

The most important climatic events are summarized in the above list, and for the purpouse of this article, it is enough.

### 4. Summarizing the Data

Now, we have two different questions to resolve:

> 1. Weather events that are most harmful with respect to population health
> 2. Weather events that are most harmful with respect to economic consequences

The climatic events that affect the first question may not be the same that affect the second question and vice versa.

For this purpouse, we need to extract these differents observations from the *sub_data* dataset. One dataset for population health, and another dataset for economic consequences.


```r
sub_data_health <- sub_data %>% filter(FATALITIES > 0 | INJURIES > 0)
sub_data_economic <- sub_data %>% filter(PROPDMG > 0 | CROPDMG > 0)

# For saving computer resources, we can remove the initial dataset
rm(sub_data)
```

Now, there are **21929 rows** or observations and **9 columns** or variables in *sub_data_health* dataset, and  **245031 rows** or observations and **9 columns** or variables in *sub_data_economic* dataset.

At this point, we are going to check the more harmful event types in our datasets.


```r
# Group by event type
my_health_group <- group_by(sub_data_health, EVTYPE)

# Get the sum of injuries and fatalities by event type

my_health_summary_fatalities <- summarise(my_health_group, sum(FATALITIES))
my_health_summary_injuries <- summarise(my_health_group, sum(INJURIES))
my_health_total_summary <- summarise(my_health_group, sum(FATALITIES),sum(INJURIES), 
                                     TOTAL = sum(FATALITIES) + sum(INJURIES))

# change tne column names
colnames(my_health_summary_fatalities) <- c("EVTYPE", "SUM_FATALITIES")
colnames(my_health_summary_injuries) <- c("EVTYPE", "SUM_INJURIES")
colnames(my_health_total_summary) <- c("EVTYPE", "SUM_FATALITIES", "SUM_INJURIES", "TOTAL")

# Order the dataset by number of FATALITIES & INJURIES
my_health_summary_fatalities <- arrange(my_health_summary_fatalities, desc(SUM_FATALITIES))
my_health_summary_injuries <- arrange(my_health_summary_injuries, desc(SUM_INJURIES))
my_health_total_summary <- arrange(my_health_total_summary, desc(TOTAL))
```


And now, we are going to check the more harmful event types with respect to economic consequences. Our dataset has four fields:

* PROPDMG (numerical value for property damage)
* PROPDMGEXP (exponent -symbol/value- for adjusting PROPDMG value)
* CROPDMG (numerical value for crop damage)
* CROPDMGEXP (exponent -symbol/value- for adjusting CROPDMG value)

PROPDMGEXP and CROPDMGEXP fields are factor variables with the following values:


```r
levels(sub_data_economic$PROPDMGEXP)
```

```
##  [1] ""  "-" "?" "+" "0" "1" "2" "3" "4" "5" "6" "7" "8" "B" "h" "H" "K"
## [18] "m" "M"
```

```r
levels(sub_data_economic$CROPDMGEXP)
```

```
## [1] ""  "?" "0" "2" "B" "k" "K" "m" "M"
```

We need to tranform the PROPDMGEXP and CROPDMGEXP to a numerical value and to multiply by PROPDMG and CROPDMG values, respectively. In this way, we will have the damage value. The next table shows the levels of the factor variable and their numerical values associated:

                     * ""  = 1 
                     * "-" = 1
                     * "?" = 1
                     * "+" = 1
                     * "0" = 1
                     * "1" = 10
                     * "2" = 100
                     * "3" = 1000
                     * "4" = 10000
                     * "5" = 100000
                     * "6" = 1000000
                     * "7" = 10000000
                     * "8" = 100000000
                     * "9" = 1000000000
                     * "h" = 100
                     * "H" = 100
                     * "k" = 1000
                     * "K" = 1000
                     * "m" = 1000000
                     * "M" = 1000000
                     * "b" = 1000000000
                     * "B" = 1000000000
                     


```r
# > levels(sub_data_economic$PROPDMGEXP)
# [1] ""  "-" "?" "+" "0" "1" "2" "3" "4" "5" "6" "7" "8" "B" "h" "H" "K" "m" "M"

levels(sub_data_economic$PROPDMGEXP) <- c(1,1,1,1,1,
                                          10,100,1000,10000,100000,1000000,
                                          10000000,100000000,1000000000,
                                          100, 100, 1000, 1000000, 1000000)


# > levels(sub_data_economic$CROPDMGEXP)
# [1] ""  "?" "0" "2" "B" "k" "K" "m" "M"

levels(sub_data_economic$CROPDMGEXP) <- c(1,1,1,100,1000000000,1000,1000,1000000,1000000)

# Show the new levels
levels(sub_data_economic$PROPDMGEXP)
```

```
##  [1] "1"     "10"    "100"   "1000"  "10000" "1e+05" "1e+06" "1e+07"
##  [9] "1e+08" "1e+09"
```

```r
levels(sub_data_economic$CROPDMGEXP)
```

```
## [1] "1"     "100"   "1e+09" "1000"  "1e+06"
```

Now, we can calculate the numerical crop and properties damage in dollars.


```r
# Add two new variables with the total damage, PROPDMG_TOTAL and CROPDMG_TOTAL
sub_data_economic <- sub_data_economic %>% 
                     mutate(PROPDMG_TOTAL = PROPDMG * as.numeric(as.character(PROPDMGEXP)), 
                            CROPDMG_TOTAL = CROPDMG * as.numeric(as.character(CROPDMGEXP)))


# Group by event type
my_economic_group <- group_by(sub_data_economic, EVTYPE)

# Get the sum of properties and crop damage by event type
my_economic_summary_propdmg <- summarise(my_economic_group, sum(PROPDMG_TOTAL))
my_economic_summary_cropdmg <- summarise(my_economic_group, sum(CROPDMG_TOTAL))
my_economic_total_summary <- summarise(my_economic_group, sum(PROPDMG_TOTAL),sum(CROPDMG_TOTAL),
                                     TOTAL = sum(PROPDMG_TOTAL) + sum(CROPDMG_TOTAL))

# change tne column names
colnames(my_economic_summary_propdmg) <- c("EVTYPE", "SUM_PROPDMG_TOTAL")
colnames(my_economic_summary_cropdmg) <- c("EVTYPE", "SUM_CROPDMG_TOTAL")
colnames(my_economic_total_summary) <- c("EVTYPE", "SUM_PROPDMG_TOTAL", 
                                          "SUM_CROPDMG_TOTAL", "TOTAL_DAMAGE")

# Order the dataset by number of total PROP. DAMAGE & total CROP DAMAGE
my_economic_summary_propdmg <- arrange(my_economic_summary_propdmg, desc(SUM_PROPDMG_TOTAL))
my_economic_summary_cropdmg <- arrange(my_economic_summary_cropdmg, desc(SUM_CROPDMG_TOTAL))
my_economic_total_summary <- arrange(my_economic_total_summary, desc(TOTAL_DAMAGE))
```

# **Results**

### Show the final data and plotting Health results and conclusions


```r
# Preview
head(my_health_summary_fatalities)
```

```
##              EVTYPE SUM_FATALITIES
## 1           TORNADO           5661
## 2    EXCESSIVE HEAT           1907
## 3              HEAT           1118
## 4       FLASH FLOOD           1045
## 5         LIGHTNING            816
## 6 THUNDERSTORM WIND            720
```

```r
head(my_health_summary_injuries)
```

```
##              EVTYPE SUM_INJURIES
## 1           TORNADO        91407
## 2 THUNDERSTORM WIND         9517
## 3             FLOOD         6871
## 4    EXCESSIVE HEAT         6575
## 5         LIGHTNING         5230
## 6              HEAT         2494
```

```r
head(my_health_total_summary)
```

```
##              EVTYPE SUM_FATALITIES SUM_INJURIES TOTAL
## 1           TORNADO           5661        91407 97068
## 2 THUNDERSTORM WIND            720         9517 10237
## 3    EXCESSIVE HEAT           1907         6575  8482
## 4             FLOOD            504         6871  7375
## 5         LIGHTNING            816         5230  6046
## 6              HEAT           1118         2494  3612
```




```r
suppressPackageStartupMessages(library(ggplot2))
suppressPackageStartupMessages(library(grid))
suppressPackageStartupMessages(library(gridExtra))

#Subsetting the six most relevant events
x <- my_health_summary_fatalities[1:6, ]
y <- my_health_summary_injuries[1:6, ]
z <- my_health_total_summary[1:6, ]

x$EVTYPE <- reorder(x$EVTYPE, x$SUM_FATALITIES)

# Create a first plot for the fatalities dataset 
p <- ggplot(x, aes(x=EVTYPE, y=SUM_FATALITIES, fill=SUM_FATALITIES))
p <- p + geom_histogram(stat='identity') + coord_flip()
p <- p + scale_fill_gradient("Nº Fatalities", 
                             low = "green", 
                             high = "red")
 # p <- p + theme(text = element_text(size=10),axis.text.x = element_text(angle=90, vjust=1))
 p <- p + theme(text = element_text(size=8)) 
 p <- p + xlab("Event Type")
 p <- p + ylab("Total Number of Fatalities")
 p <- p + ggtitle("Total Fatalities By Event Type")
 
 
 y$EVTYPE <- reorder(y$EVTYPE, y$SUM_INJURIES)

# Create a second plot for the injuries dataset 
p2 <- ggplot(y, aes(x=EVTYPE, y=SUM_INJURIES, fill=SUM_INJURIES))
p2 <- p2 + geom_histogram(stat='identity') + coord_flip()
p2 <- p2 + scale_fill_gradient("Nº Injuries", 
                             low = "green", 
                             high = "red")
 # p <- p + theme(text = element_text(size=10),axis.text.x = element_text(angle=90, vjust=1))
 p2 <- p2 + theme(text = element_text(size=8)) 
 p2 <- p2 + xlab("Event Type")
 p2 <- p2 + ylab("Total Number of Injuries")
 p2 <- p2 + ggtitle("Total Injuries By Event Type")
 
 
 z$EVTYPE <- reorder(z$EVTYPE, z$TOTAL)

# Create a third plot for the TOTAL health dataset 
p3 <- ggplot(z, aes(x=EVTYPE, y=TOTAL, fill=TOTAL))
p3 <- p3 + geom_histogram(stat='identity') + coord_flip()
p3 <- p3 + scale_fill_gradient("Nº Total", 
                             low = "green", 
                             high = "red")
 # p <- p + theme(text = element_text(size=10),axis.text.x = element_text(angle=90, vjust=1))
 p3 <- p3 + theme(text = element_text(size=8)) 
 p3 <- p3 + xlab("Event Type")
 p3 <- p3 + ylab("Total Number of Fatalities + Injuries")
 p3 <- p3 + ggtitle("Total Fatalities + Injuries By Event Type")
 
 
 ## Call to multipot function for plotting the 2 plots in one column
 # multiplot(p, p3, p2, cols = 2)
 # multiplot(p, p2, p3, matrix(c(1,2,3,3), nrow=1, byrow=TRUE))

 # Get the gtables
gA <- ggplotGrob(p)
gB <- ggplotGrob(p2)
gC <- ggplotGrob(p3)

grid.arrange(gA, gB, gC, nrow=3)
```

![](PA2_template_files/figure-html/unnamed-chunk-16-1.png) 

As a conclusion we can see clearly the TORNADO as the event type that is most harmful with respect to population health, followed by HEAT/EXCESSIVE HEAT and THUNDERSTORM WIND. 

### Show the final data and plotting Economic results and conclusions



```r
# Preview
head(my_economic_summary_propdmg)
```

```
##              EVTYPE SUM_PROPDMG_TOTAL
## 1             FLOOD      150097910107
## 2 HURRICANE/TYPHOON       85356410010
## 3           TORNADO       58603317926
## 4       STORM SURGE       43323536000
## 5       FLASH FLOOD       17882147618
## 6              HAIL       15977470013
```

```r
head(my_economic_summary_cropdmg)
```

```
##              EVTYPE SUM_CROPDMG_TOTAL
## 1           DROUGHT       13972566000
## 2             FLOOD       10842080550
## 3 HURRICANE/TYPHOON        5516117800
## 4         ICE STORM        5022114300
## 5              HAIL        3026094623
## 6      FROST/FREEZE        1997061000
```

```r
head(my_economic_total_summary)
```

```
##              EVTYPE SUM_PROPDMG_TOTAL SUM_CROPDMG_TOTAL TOTAL_DAMAGE
## 1             FLOOD      150097910107       10842080550 160939990657
## 2 HURRICANE/TYPHOON       85356410010        5516117800  90872527810
## 3           TORNADO       58603317926         417461520  59020779446
## 4       STORM SURGE       43323536000              5000  43323541000
## 5       FLASH FLOOD       17882147618        1546461650  19428609268
## 6              HAIL       15977470013        3026094623  19003564636
```




```r
suppressPackageStartupMessages(library(ggplot2))
suppressPackageStartupMessages(library(grid))
suppressPackageStartupMessages(library(gridExtra))

#Subsetting the six most relevant events
x <- my_economic_summary_propdmg[1:6, ]
y <- my_economic_summary_cropdmg[1:6, ]
z <- my_economic_total_summary[1:6, ]

x$EVTYPE <- reorder(x$EVTYPE, x$SUM_PROPDMG_TOTAL)

# Create a first plot for the properties damage dataset 
p <- ggplot(x, aes(x=EVTYPE, y=SUM_PROPDMG_TOTAL, fill=SUM_PROPDMG_TOTAL))
p <- p + geom_histogram(stat='identity') + coord_flip()
p <- p + scale_fill_gradient("USD", 
                             low = "green", 
                             high = "red")
 # p <- p + theme(text = element_text(size=10),axis.text.x = element_text(angle=90, vjust=1))
 p <- p + theme(text = element_text(size=8)) 
 p <- p + xlab("Event Type")
 p <- p + ylab("Total Property Damage in USD")
 p <- p + ggtitle("Total Property Damage By Event Type")
 
 
 y$EVTYPE <- reorder(y$EVTYPE, y$SUM_CROPDMG_TOTAL)

# Create a second plot for the crop damage dataset 
p2 <- ggplot(y, aes(x=EVTYPE, y=SUM_CROPDMG_TOTAL, fill=SUM_CROPDMG_TOTAL))
p2 <- p2 + geom_histogram(stat='identity') + coord_flip()
p2 <- p2 + scale_fill_gradient("USD", 
                             low = "green", 
                             high = "red")
 # p <- p + theme(text = element_text(size=10),axis.text.x = element_text(angle=90, vjust=1))
 p2 <- p2 + theme(text = element_text(size=8)) 
 p2 <- p2 + xlab("Event Type")
 p2 <- p2 + ylab("Total Crop Damage in USD")
 p2 <- p2 + ggtitle("Total Crop Damage By Event Type")
 
 
 z$EVTYPE <- reorder(z$EVTYPE, z$TOTAL_DAMAGE)

# Create a third plot for the TOTAL economic dataset 
p3 <- ggplot(z, aes(x=EVTYPE, y=TOTAL_DAMAGE, fill=TOTAL_DAMAGE))
p3 <- p3 + geom_histogram(stat='identity') + coord_flip()
p3 <- p3 + scale_fill_gradient("USD", 
                             low = "green", 
                             high = "red")
 # p <- p + theme(text = element_text(size=10),axis.text.x = element_text(angle=90, vjust=1))
 p3 <- p3 + theme(text = element_text(size=8)) 
 p3 <- p3 + xlab("Event Type")
 p3 <- p3 + ylab("Total Damage in USD")
 p3 <- p3 + ggtitle("Total Properties + Crop Damage By Event Type")
 
 
 ## Call to multipot function for plotting the 2 plots in one column
 # multiplot(p, p3, p2, cols = 2)
 # multiplot(p, p2, p3, matrix(c(1,2,3,3), nrow=1, byrow=TRUE))

 # Get the gtables
gA <- ggplotGrob(p)
gB <- ggplotGrob(p2)
gC <- ggplotGrob(p3)

grid.arrange(gA, gB, gC, nrow=3)
```

![](PA2_template_files/figure-html/unnamed-chunk-18-1.png) 

As a conclusion we can see clearly the FLOOD as the event type that is most harmful with respect to economic consequences, followed by HURRICANE/TYPHOON and TORNADO. Although the DROUGHT event is the most harmful with respect to CROP damage.
