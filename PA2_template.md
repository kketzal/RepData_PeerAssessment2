# Reproducible Research: Peer Assessment 2


# **Title**

## Types of weather events that are most harmful with respect to population health and with respect to economic consequences

# **Synopsis**


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

### 2. Storm Data - Exploratory analysis 

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
* PROPDMGEXP (value for adjusting PROPDMG value)
* CROPDMG (numerical value for crop damage)
* CROPDMGEXP (value for adjusting CROPDMG value)

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

### 3. Storm Data - Cleaning the Data

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

toMatch <- c("^THUNDERSTORM", "^TSTM", "^SEVERE THUNDERSTORM", "^THUNDERTORM WINDS", "^TSTM WIND ")
sub_data <- groupingFactorLevels(sub_data, c("THUNDERSTORM WIND"), toMatch)

toMatch <-c("^MARINE TSTM WIND", "^MARINE THUNDERSTORM WIND" )
sub_data <- groupingFactorLevels(sub_data, c("MARINE THUNDERSTORM WIND"), toMatch)

toMatch <-c("HURRICANE", "TYPHOON", "HURRICANE/TYPHOON")
sub_data <- groupingFactorLevels(sub_data, c("HURRICANE/TYPHOON"), toMatch)

toMatch <-c("RIVER FLOOD", "URBAN/SMALL STREAM FLOOD", "URBAN FLOODING", "MAJOR FLOOD", 
            "URBAN FLOOD", "URBAN/SML STREAM FLD", "FLOOD/RAIN/WINDS", "^FLOODS$")
sub_data <- groupingFactorLevels(sub_data, c("FLOOD"), toMatch)

toMatch <-c("FLASH FLOOD", "FLASH FLOODING", "FLOODING", "FLASH FLOODS")
sub_data <- groupingFactorLevels(sub_data, c("FLASH FLOOD"), toMatch)

toMatch <-c("LIGHT FREEZING RAIN", "FREEZE", "FREEZING RAIN", "FROST/FREEZE", 
           "AGRICULTURAL FREEZE", "FREEZING RAIN/SNOW", "DAMAGING FREEZE", "FREEZING DRIZZLE", 
           "FROST", "SNOW FREEZING RAIN")
sub_data <- groupingFactorLevels(sub_data, c("FROST/FREEZE"), toMatch)

toMatch <-c("^COLD$", "^WIND CHILD$", "$COLD/WIND CHILL$")
sub_data <- groupingFactorLevels(sub_data, c("COLD/WIND CHILL"), toMatch)

toMatch <-c("SNOW") 
sub_data <- groupingFactorLevels(sub_data, c("HEAVY SNOW"), toMatch)

toMatch <-c("HAIL") 
sub_data <- groupingFactorLevels(sub_data, c("HAIL"), toMatch)

# This new data type is to summarize... 
toMatch <-c("FOG") 
sub_data <- groupingFactorLevels(sub_data, c("DENSE/FREEZING FOG"), toMatch)

toMatch <-c("HEAT") 
sub_data <- groupingFactorLevels(sub_data, c("HEAT/EXCESSIVE HEAT"), toMatch)

summary(sub_data$EVTYPE)
```

```
##        THUNDERSTORM WIND                  TORNADO                     HAIL 
##                   119665                    39970                    26166 
##              FLASH FLOOD                LIGHTNING                    FLOOD 
##                    21748                    13293                    11245 
##                HIGH WIND              STRONG WIND               HEAVY SNOW 
##                     5522                     3372                     1880 
##             WINTER STORM               HEAVY RAIN      HEAT/EXCESSIVE HEAT 
##                     1508                     1105                      980 
##                 WILDFIRE                ICE STORM               HIGH WINDS 
##                      857                      708                      657 
##           TROPICAL STORM           WINTER WEATHER              RIP CURRENT 
##                      416                      407                      400 
##         WILD/FOREST FIRE                AVALANCHE                  DROUGHT 
##                      388                      268                      266 
##                 BLIZZARD             RIP CURRENTS             FROST/FREEZE 
##                      253                      241                      240 
##        HURRICANE/TYPHOON             EXTREME COLD                LANDSLIDE 
##                      233                      199                      193 
##       DENSE/FREEZING FOG              STORM SURGE            COASTAL FLOOD 
##                      189                      177                      168 
## MARINE THUNDERSTORM WIND       WINTER WEATHER/MIX                HIGH SURF 
##                      142                      139                      136 
##          COLD/WIND CHILL  EXTREME COLD/WIND CHILL               DUST STORM 
##                      128                      111                      103 
##               DUST DEVIL                     WIND           DRY MICROBURST 
##                       95                       84                       78 
##             STRONG WINDS     HEAVY SURF/HIGH SURF         STORM SURGE/TIDE 
##                       52                       50                       47 
##               WATERSPOUT       MARINE STRONG WIND      TROPICAL DEPRESSION 
##                       47                       46                       35 
##                    OTHER              GUSTY WINDS               HEAVY SURF 
##                       34                       32                       32 
##                      ICE                ICY ROADS                    GLAZE 
##                       24                       22                       21 
##              HEAVY RAINS        EXTREME WINDCHILL         MARINE HIGH WIND 
##                       20                       19                       19 
##      MIXED PRECIPITATION                    WINDS                  TSUNAMI 
##                       18                       18                       14 
##             FUNNEL CLOUD               GUSTY WIND                   SEICHE 
##                       13                       13                        9 
##              WIND DAMAGE   ASTRONOMICAL HIGH TIDE                HEAVY MIX 
##                        9                        8                        8 
##                HIGH SEAS          LOW TEMPERATURE        UNSEASONABLY WARM 
##                        8                        7                        7 
##              WATERSPOUT-            GRADIENT WIND     HYPOTHERMIA/EXPOSURE 
##                        7                        6                        6 
##             MIXED PRECIP                 MUDSLIDE                     RAIN 
##                        6                        6                        6 
##                 GUSTNADO          HIGH WINDS/COLD          LAKESHORE FLOOD 
##                        5                        5                        5 
##              RECORD COLD            COASTAL STORM             COLD WEATHER 
##                        5                        4                        4 
##               HIGH WATER               MICROBURST        UNSEASONABLY COLD 
##                        4                        4                        4 
##               WILD FIRES               WINTRY MIX               BRUSH FIRE 
##                        4                        4                        3 
##               LANDSLIDES                MUD SLIDE               ROUGH SEAS 
##                        3                        3                        3 
##           WET MICROBURST                WHIRLWIND                WILDFIRES 
##                        3                        3                        3 
##    ASTRONOMICAL LOW TIDE         COLD TEMPERATURE                DAM BREAK 
##                        2                        2                        2 
##       EROSION/CSTL FLOOD       FLOOD & HEAVY RAIN                GLAZE ICE 
##                        2                        2                        2 
##          GROUND BLIZZARD               HEAVY SEAS              HIGH SWELLS 
##                        2                        2                        2 
##                  (Other) 
##                      147
```

The most important climatic events are summarized in the above list, and for the purpouse of this article, it is enough.

### 4. Storm Data - Summarizing the Data

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

# Preview
my_health_summary_fatalities
```

```
## Source: local data frame [124 x 2]
## 
##                 EVTYPE SUM_FATALITIES
##                 (fctr)          (dbl)
## 1              TORNADO           5661
## 2  HEAT/EXCESSIVE HEAT           3138
## 3          FLASH FLOOD           1045
## 4            LIGHTNING            816
## 5    THUNDERSTORM WIND            711
## 6                FLOOD            503
## 7          RIP CURRENT            368
## 8            HIGH WIND            248
## 9            AVALANCHE            224
## 10        WINTER STORM            206
## ..                 ...            ...
```

```r
my_health_summary_injuries
```

```
## Source: local data frame [124 x 2]
## 
##                 EVTYPE SUM_INJURIES
##                 (fctr)        (dbl)
## 1              TORNADO        91407
## 2    THUNDERSTORM WIND         9508
## 3  HEAT/EXCESSIVE HEAT         9224
## 4                FLOOD         6871
## 5            LIGHTNING         5230
## 6            ICE STORM         1975
## 7          FLASH FLOOD         1810
## 8                 HAIL         1371
## 9    HURRICANE/TYPHOON         1333
## 10        WINTER STORM         1321
## ..                 ...          ...
```

```r
my_health_total_summary
```

```
## Source: local data frame [124 x 4]
## 
##                 EVTYPE SUM_FATALITIES SUM_INJURIES TOTAL
##                 (fctr)          (dbl)        (dbl) (dbl)
## 1              TORNADO           5661        91407 97068
## 2  HEAT/EXCESSIVE HEAT           3138         9224 12362
## 3    THUNDERSTORM WIND            711         9508 10219
## 4                FLOOD            503         6871  7374
## 5            LIGHTNING            816         5230  6046
## 6          FLASH FLOOD           1045         1810  2855
## 7            ICE STORM             89         1975  2064
## 8         WINTER STORM            206         1321  1527
## 9    HURRICANE/TYPHOON            135         1333  1468
## 10                HAIL             15         1371  1386
## ..                 ...            ...          ...   ...
```



