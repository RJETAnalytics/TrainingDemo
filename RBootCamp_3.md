# R Boot Camp Session 3

This class is for people who are new to R but also:

- Have intermediate to expert skills in Excel
- Are familiar with basic to intermediate statistics
- Have basic to intermediate skills in some programming language

We will generally be following "R For Data Science" at: https://r4ds.had.co.nz/ which is a much better comprehensive guide than this class.

This class will skip some concepts and accelerate you through this book covering about 50% of what is there.  I advise going back and skimming through the things that do not look familiar as they will absolutely come up later in your R journey.

**Clear Memory and Load Packages**
```
# Optional memory clear
rm(list=ls())
# Disable Scientific Notation in printing
options(scipen=999)
# Unload All Packages
lapply(names(sessionInfo()$otherPkgs), function(pkgs)
  detach(
    paste0('package:', pkgs),
    character.only = T,
    unload = T,
    force = T
  ))

library(tidyverse)
```
**Load Publicly Available Data**
```
# A Character table of unwanted character values we want to convert
# I made this in Excel and then copy/pasted it in as constants using
# the datapasta package
CharTable = tibble::tribble(
   ~Source,  ~Translated,
     "Á", "A",
     "Â", "A",
     "Ã", "A",
     "Ä", "A",
     "Å", "A",
     "Æ", "A",
     "Ç", "C",
     "È", "E",
     "É", "E",
     "Ê", "E",
     "Ë", "E",
     "Ì", "I",
     "Í", "I",
     "Î", "I",
     "Ï", "I",
     "Ð", "G",
     "Ñ", "N",
     "Ò", "O",
     "Ó", "O",
     "Ô", "O",
     "Õ", "O",
     "Ö", "O",
     "×", "X",
     "Ø", "T",
     "Ù", "U",
     "Ú", "U",
     "Û", "U",
     "Ü", "U",
     "Ý", "Y",
     "Þ", "b",
     "ß", "b",
     "à", "a",
     "á", "a",
     "â", "a",
     "ã", "a",
     "ä", "a",
     "å", "a",
     "æ", "a",
     "ç", "c",
     "è", "e",
     "é", "e",
     "ê", "e",
     "ë", "e",
     "ì", "i",
     "í", "i",
     "î", "i",
     "ï", "i",
     "ð", "o",
     "ñ", "n",
     "ò", "o",
     "ó", "o",
     "ô", "o",
     "õ", "o"
     )

# Turning it from a 50 x 2 table into a 2 variable table 
CharTable = tibble(
  Source = paste(CharTable$Source, collapse = ""),
  Translated = paste(CharTable$Translated, collapse = ""),
)

# A custom function to convert a column to a logical variable type while handling 
# character values of "NULL"
as.nalogi = function(CharVec) {
  CharVec = if_else(CharVec == "NULL", as.character(NA), CharVec)
  CharVec = as.logical(CharVec)
  return(CharVec)
}

as.nanumeric = function(CharVec) {
  CharVec = if_else(CharVec == "NULL" | is.na(CharVec), as.character("0"), CharVec)
  CharVec = as.numeric(CharVec)
  return(CharVec)
}

# Read a Billboard table where each row is a song and its' rank in the top 100 for every week since the 1950's
demo_BillboardRank <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-09-14/billboard.csv')

# Read a table with song attributes from the Spotify database with an ID that allows us to link them to the Billboard rank table
demo_BillboardFeatures <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-09-14/audio_features.csv')

# Clean up date fields
demo_BillboardRank = demo_BillboardRank %>%
  mutate(week_id = as.Date(week_id, format = "%m/%d/%Y"))

# Clean up character fields for unwanted values
demo_BillboardFeatures = demo_BillboardFeatures %>%
    mutate_if(is.character, function(x) chartr(CharTable$Source, CharTable$Translated, x))

# Read a table where every row is an episode of the cooking competition show Chopped
demo_Chopped <- readr::read_tsv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-08-25/chopped.tsv')

# Fix data type for date and clean up character fields for unwanted values
demo_Chopped = demo_Chopped %>%
  mutate(air_date = as.Date(air_date, format = "%B %d, %Y")) %>%
  mutate_if(is.character, function(x) chartr(CharTable$Source, CharTable$Translated, x))

# Read a table where every row is an episode of the Scooby Doo cartoon show
demo_ScoobyDoo <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-07-13/scoobydoo.csv')

# Fix data types for numeric and binary fields
demo_ScoobyDoo = demo_ScoobyDoo %>%
  mutate(across(contains(c("imdb", 
                           "number_of_snacks", "split_up", "another_mystery",
                           "set_a_trap", "jeepers", "jinkies", "my_glasses",
                           "just_about_wrapped_up", "zoinks", "groovy",
                           "scooby_doo_where_are_you", "rooby_rooby_roo")),
              ~ as.nanumeric(.))) %>%
  mutate(across(contains(c("caught_fred", "caught_daphnie", "caught_velma",
                           "caught_shaggy", "caught_scooby", "captured_fred", 
                           "captured_daphnie", "captured_velma", "captured_shaggy", 
                           "captured_scooby", "snack_fred", "snack_daphnie", 
                           "snack_velma", "snack_shaggy", "snack_scooby", 
                           "unmask_fred", "unmask_daphnie", "unmask_velma", 
                           "unmask_shaggy", "unmask_scooby")),
              ~ as.nalogi(.))) %>%
  mutate(across(contains(c("trap_work_first", "non_suspect", "arrested")),
              ~ as.nalogi(.)))
```

AGENDA

1. Chopped

    a. Explore ratings by year and visualize
  
    b. Reshape data for ratings analysis
  
    c. Explore re
  
2. In-Class Assignment : using Scooby table

    a. Explore ratings by year and visualize
  
    b. Compare ratings by real versus fake Monsters with both a summary bar and box plot
  
    c. Pick some features and explore them

3. Billboard

    a. Join our rank and features data and filter to relevant columns
  
    b. Explore the data and visualize
  
    c. Build a linear model
  
    d. See if the linear model is predictive using test and train splits
