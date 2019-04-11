
# gbifimagedata

An R package to get gbif image stats from the api in order to look for good datasets for machine learning. 


# Installation  

Need to install both of these R packages using devtools. 

```
install.packages("devtools")

devtools::install_github("jhnwllr/gbifapi") # depends on some functions here
devtools::install_github("jhnwllr/gbifimagedata")

install.packages("tidyverse") # will probably avoid some dependecy issues
```

# Usage 

The following should return a table of **frog image data stats**. 

## Simple example for testing 

Increase the `Step` and `maxPages` arguments in order to get more occurrences (more image data). Limited to 200,000 records. There is information on larger groups [here](https://jhnwllr.github.io/charts/percentCoverageTable). 

```
library(gbifimagedata)
library(dplyr)

getImageData(friendlyName="frogs",friendlyKey="952",Step=10,maxPages=2) %>% 
filter(taxonomicStatus == "ACCEPTED") %>% # get only ACCEPTED species
filter(rank == "SPECIES") %>% # only get for Rank SPECIES
addLicenseTranslation() %>%
summariseTable() %>%
filter(imageCount >= 1) %>% # only get species with more than n images
groupSummarise() %>%
filter(country == "world") %>% 
addPercentCoverage(globalOnly=TRUE) %>%
addWorldPercentage() %>% # adds through a different api
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  
arrange(-percentCoverage,license)

```

This should return a table looking something like this... 

Since we only dowloaded 30 records, this table will be not very useful. 

**percentCoverage** = ( totalSpeciesWithImages / totalSpeciesInCountry ) * 100

**country** can be the world as it is below. 

```
 friendlyName country basisOfRecord    license                  percentCoverage
  <chr>        <chr>   <chr>            <chr>                              <dbl>
1 frogs        world   HUMAN_OBSERVATI~ only non-commercial use~           0.313
2 frogs        world   HUMAN_OBSERVATI~ total                              0.313

```

## Real example

Below I do a real analysis: 

This will take probably **5-10 minutes to download the data**. But you can save the data at each step just in case something goes wrong using `saveData`. 

```
library(gbifimagedata)
library(dplyr)

saveDir = "C:/Users/ftw712/Desktop/image data friendly taxa/data/frogs/"

getImageData(friendlyName="frogs",friendlyKey="952",Step=100,maxPages=2000) %>% 
saveData(saveDir,fileName="imageData.rda") %>% # save the data from the most expensive step
loadData(saveDir=saveDir,fileName="imageData.rda") %>% # load that data from disk
filter(taxonomicStatus == "ACCEPTED") %>% # get only ACCEPTED species
filter(rank == "SPECIES") %>% # only get for Rank SPECIES
addLicenseTranslation() %>%
summariseTable() %>%
filter(imageCount >= 1) %>% # only get species with more than n images
groupSummarise() %>%
filter(country == "world") %>% 
addPercentCoverage(globalOnly=TRUE) %>%
addWorldPercentage() %>% # adds through a different api
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  
arrange(-percentCoverage,license)
```

Here I am skipping the expensive species counts for each country (in `addpercentCoverage` ) by using `globalOnly=TRUE`. 

This should produce a table looking close to this: 

```
  friendlyName country basisOfRecord     license                percentCoverage
   <chr>        <chr>   <chr>             <chr>                            <dbl>
 1 frogs        world   PRESERVED_SPECIM~ total                          23.4   
 2 frogs        world   HUMAN_OBSERVATION total                          18.6   
 3 frogs        world   HUMAN_OBSERVATION only non-commercial u~         17.6   
 4 frogs        world   PRESERVED_SPECIM~ commercial use allowed         13.4   
 5 frogs        world   PRESERVED_SPECIM~ only non-commercial u~         11.0   
 6 frogs        world   HUMAN_OBSERVATION commercial use allowed          6.18  
 7 frogs        world   UNKNOWN           only non-commercial u~          0.122 
 8 frogs        world   UNKNOWN           total                           0.122 
 9 frogs        world   FOSSIL_SPECIMEN   commercial use allowed          0.0136
10 frogs        world   FOSSIL_SPECIMEN   total                           0.0136

```

* 23% frogs have 1 image or more (preserved specimen)
* 13% allow for commercial use (preserved specimen)

# TODO 

* make work for GENUS level or higher
* lists of taxonKeys. 
* make other groupings easier 

# Hummingbirds analysis

[hummingbird images on GBIF](https://www.gbif.org/occurrence/gallery?media_type=StillImage&taxon_key=5289&advanced=1)


```
saveDir = "C:/Users/ftw712/Desktop/image data friendly taxa/data/hummingbirds/"

getImageData(friendlyName="hummingbirds",friendlyKey="5289",Step=100,maxPages=2000) %>% 
saveData(saveDir,fileName="imageData.rda") %>% # save the data from the most expensive step
loadData(saveDir=saveDir,fileName="imageData.rda") %>% # load that data from disk
filter(taxonomicStatus == "ACCEPTED") %>% # get only ACCEPTED species
filter(rank == "SPECIES") %>% # only get for Rank SPECIES
addLicenseTranslation() %>%
summariseTable() %>%
filter(imageCount >= 1) %>% # only get species with more than n images
groupSummarise() %>%
filter(country == "world") %>% 
addPercentCoverage(globalOnly=TRUE) %>%
addWorldPercentage() %>% # adds through a different api
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  
arrange(-percentCoverage,license)

```

```

  friendlyName country basisOfRecord     license                 percentCoverage
  <chr>        <chr>   <chr>             <chr>                             <dbl>
1 hummingbirds world   HUMAN_OBSERVATION total                             53.3 
2 hummingbirds world   HUMAN_OBSERVATION only non-commercial us~           51.2 
3 hummingbirds world   PRESERVED_SPECIM~ total                             42.9 
4 hummingbirds world   PRESERVED_SPECIM~ commercial use allowed            39.9 
5 hummingbirds world   HUMAN_OBSERVATION commercial use allowed            23.0 
6 hummingbirds world   PRESERVED_SPECIM~ only non-commercial us~            5.23
```

* %53 of hummingbirds have at least 1 image (human obs)
* %23 allow for commercial usage (human obs)

