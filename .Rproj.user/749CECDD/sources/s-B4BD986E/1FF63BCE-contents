
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

Increase the `Step` and `maxPages` arguments in order to get more occurrences (more image data). Limited to 200,000 records. There is information on larger groups [here](https://jhnwllr.github.io/charts/percentCoverageTable). 

```
library(gbifimagedata)
library(dplyr)

getImageData(friendlyName="frogs",friendlyKey="952",Rank="SPECIES",Step=10,maxPages=2) %>%
addLicenseTranslation() %>%
summariseTable() %>% 
filter(imageCount > 1) %>% # only get species with more than n images
addpercentCoverage(minNumSpeciesWithImages=1,Step=1000,globalOnly=TRUE) %>% 
addWorldPercentage() %>% 
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  
arrange(-percentCoverage)

```

This should return a table looking something like this... 

Since we only dowloaded 30 records, this table will be not very useful. 

**percentCoverage** = ( totalSpeciesWithImages / totalSpeciesInCountry ) * 100

**country** can be the world as it is below. 

```
  friendlyName country basisOfRecord    license                  percentCoverage
  <chr>        <chr>   <chr>            <chr>                              <dbl>
1 frogs        world   HUMAN_OBSERVATI~ only non-commercial use~           0.204
2 frogs        world   HUMAN_OBSERVATI~ total                              0.204

```

Below I do a real analysis: 

This will take probably **5-10 minutes to download the data**. But you can save the data at each step just in case something goes wrong using `saveData`. 

```
library(gbifimagedata)
library(dplyr)

saveDir = "C:/Users/ftw712/Desktop/image data friendly taxa/data/frogs/"

getImageData(friendlyName="frogs",friendlyKey="952",Step=100,maxPages=2000) %>% 
saveData(saveDir,fileName="imageData.rda") %>% # save the data from the most expensive step
loadData(saveDir,fileName="imageData.rda") %>% # load that data from disk
glimpse() %>% # take a look at the table
filter(rank == "SPECIES") %>% # only get for Rank GENUS
addLicenseTranslation() %>%
summariseTable() %>% 
filter(imageCount > 1) %>% # only get species with more than n images
addpercentCoverage(minNumSpeciesWithImages=1,Step=1000,globalOnly=TRUE) %>%
addPercentCoverage() %>% 
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  
arrange(-percentCoverage)
```

Here I am skipping expenisve species counts for each country (in `addpercentCoverage` ) by using `globalOnly=TRUE`. 

This should produce a table looking close to this: 

```
  friendlyName country basisOfRecord     license                 percentCoverage
  <chr>        <chr>   <chr>             <chr>                             <dbl>
1 frogs        world   PRESERVED_SPECIM~ total                            30.1  
2 frogs        world   HUMAN_OBSERVATION total                            15.9  
3 frogs        world   PRESERVED_SPECIM~ commercial use allowed           15.7  
4 frogs        world   PRESERVED_SPECIM~ only non-commercial us~          15.3  
5 frogs        world   HUMAN_OBSERVATION only non-commercial us~          14.9  
6 frogs        world   HUMAN_OBSERVATION commercial use allowed            4.79 
7 frogs        world   UNKNOWN           only non-commercial us~           0.122
8 frogs        world   UNKNOWN           total                             0.122

```

* Here we see that globally 30% of frogs have at least 1 image (preserved specimen). 
* 45% of frogs have 1 image (30% + 15%; preserved specimen + human obs). 
* but only 20% of frogs have images with commercial licensing (15% + 5%). 

# TODO 

* make work for GENUS level or higher
* lists of taxonKeys. 



