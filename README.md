
# gbifimagedata

An R package to get gbif image stats statistics from the api identifying good datasts for machine learning.

# Installation  

Need to install both of these R packages using devtools. 

```
devtools::install_github("jhnwllr/gbifapi") # depends on some functions here
devtools::install_github("jhnwllr/gbifimagedata")

install.packages("tidyverse") # will probably avoid some dependecy issues
```

# Usage 

The following should return a table of frog image data. Increase the `Step` and `maxPages` arguments in order to get more occurrences (more image data). This runs off the api so it is limited to 200,000 records returned. So this tool will only work with groups with a small amount of occurrences. 

```
library(gbifimagedata)
library(dplyr)

getImageData(friendlyName="frogs",friendlyKey="952",Rank="SPECIES",Step=10,maxPages=2) %>% # no steps over 300; no groups over 200,000
addLicenseTranslation() %>%
summariseTable() %>% 
filter(imageCount > 1) %>% # only get species with more than n images
addpercentCoverage(minNumSpeciesWithImages=1,Step=1000,globalOnly=TRUE) %>%
addWorldPercentage() %>% # this is done using "http://api.gbif.org/v1/species/search?rank=SPECIES&status=ACCEPTED&highertaxon_key=1459"
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  # see how good the potential dataset is...
arrange(-percentCoverage)

```

This should return a table looking something like this... Since we only dowloaded 30 records, this table will be not very useful. 

```
  friendlyName country basisOfRecord    license                  percentCoverage
  <chr>        <chr>   <chr>            <chr>                              <dbl>
1 frogs        world   HUMAN_OBSERVATI~ only non-commercial use~           0.204
2 frogs        world   HUMAN_OBSERVATI~ total                              0.204

```

We can run this block of code below. This will take probably 5-10 minutes. 

```
library(gbifimagedata)
library(dplyr)

getImageData(friendlyName="frogs",friendlyKey="952",Rank="SPECIES",Step=100,maxPages=2000) %>% # no steps over 300; no groups over 200,000
addLicenseTranslation() %>%
summariseTable() %>% 
filter(imageCount > 1) %>% # only get species with more than n images
addpercentCoverage(minNumSpeciesWithImages=1,Step=1000,globalOnly=TRUE) %>%
addWorldPercentage() %>% # this is done using "http://api.gbif.org/v1/species/search?rank=SPECIES&status=ACCEPTED&highertaxon_key=1459"
select(friendlyName,country,basisOfRecord,license,percentCoverage) %>%  # see how good the potential dataset is...
arrange(-percentCoverage)
```




