# R Boot Camp Session 2

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

**Load up our prior lessons**
```

SaveFile = "C:\\Users\\Chris.Woolery\\OneDrive - Republic Airways\\Documents\\2022\\R Boot Camp 2022 V2.RDATA"
load(SaveFile)

```

```

CarsDF_4Cyl = CarsDF %>%
  filter(cyl == 4)

```

**Save a single variable**
```

SaveFile = "C:\\Users\\Chris.Woolery\\OneDrive - Republic Airways\\Documents\\2022\\R Boot Camp 2022 V1 - 4CYL.RDATA"

saveRDS(CarsDF_4Cyl, SaveFile)

rm(CarsDF_4Cyl, CarsDF)

CarsDF_4Cyl = readRDS(SaveFile)

```

**Row Names versus columns**
```

data(mtcars)
rm(mtcars)

CarsDF <- mtcars

```

```
# Usually we don't use the row.name functionality in data frames
# You can get rid of it like this
CarsDF <- CarsDF %>%
  mutate(model = row.names(.)) %>%
  select(model, everything())

```

```
# But dplyr added a specific function to make it easy

CarsDF <- CarsDF %>%
  rownames_to_column("model")

```

```
# Let's also discuss how we can address specific rows and columns by position if needed
# Dataframe[Row, Column] will give you a specific "Cell"
# You can use ranges for Row or Column like 1:10 or leave it blank so Dataframe[1:10,] will give you all columns but just the first 10 rows

CarsDF[2,2]

CarsDF[1:10,]

CarsDF[,3]

CarsDF$cyl[3]

```

One note about tidyverse data frames.  They are tidy tables or tibbles
According to tidyverse.org
"Tibbles are data frames that are lazy and surly"
https://tibble.tidyverse.org/
They don't automatically change data types and complain more when things are wrong
If you test the data class on an object and it's a "tbl" or "tbl_df you'll know why
tibbles are syntactically easier and work best inside the tidyverse
back to a data frame which is very simple with tibble(), data.frame()

```

x <- tibble(CarsDF)
class(x)
x <- data.frame(x)
class(x)

```

In this context let's talk about loading data in from four common sources

**Excel**
I'll send you this spreadsheet of Lift Landings for Winter Operations
```

library(readxl)

LandingsFile = "C:\\Users\\Chris.Woolery\\OneDrive - Republic Airways\\Documents\\2022\\VUJ Stanley County Landings.xlsx"
# VUJLandings
LandingsDF = read_excel(LandingsFile, sheet = "VUJLandings")
```


**CSV**
```

demo_Hotels <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-02-11/hotels.csv')

```


**TSV**
```

demo_Chopped <- readr::read_tsv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-08-25/chopped.tsv')

CharTable = tibble::tribble(
   ~Source,  ~Translated,
     "Ã", "A",
     "Ã", "A",
     "Ã", "A",
     "Ã", "A",
     "Ã", "A",
     "Ã", "A",
     "Ã", "C",
     "Ã", "E",
     "Ã", "E",
     "Ã", "E",
     "Ã", "E",
     "Ã", "I",
     "Ã", "I",
     "Ã", "I",
     "Ã", "I",
     "Ã", "G",
     "Ã", "N",
     "Ã", "O",
     "Ã", "O",
     "Ã", "O",
     "Ã", "O",
     "Ã", "O",
     "Ã", "X",
     "Ã", "T",
     "Ã", "U",
     "Ã", "U",
     "Ã", "U",
     "Ã", "U",
     "Ã", "Y",
     "Ã", "b",
     "Ã", "b",
     "Ã ", "a",
     "Ã¡", "a",
     "Ã¢", "a",
     "Ã£", "a",
     "Ã¤", "a",
     "Ã¥", "a",
     "Ã¦", "a",
     "Ã§", "c",
     "Ã¨", "e",
     "Ã©", "e",
     "Ãª", "e",
     "Ã«", "e",
     "Ã¬", "i",
     "Ã­", "i",
     "Ã®", "i",
     "Ã¯", "i",
     "Ã°", "o",
     "Ã±", "n",
     "Ã²", "o",
     "Ã³", "o",
     "Ã´", "o",
     "Ãµ", "o"
     )

CharTable = tibble(
  Source = paste(CharTable$Source, collapse = ""),
  Translated = paste(CharTable$Translated, collapse = ""),
)

demo_Chopped = demo_Chopped %>%
  mutate(air_date = as.Date(air_date, format = "%B %d, %Y")) %>%
  mutate_if(is.character, function(x) chartr(CharTable$Source, CharTable$Translated, x))

```


**SQL Server Database**
```

library(odbc)
library(DBI)

DBADEV2016 <- dbConnect(odbc(), 
                        Driver = "SQL Server", 
                        Server = "DBADEV2016", 
                        Database = "finanalytics", 
                        Trusted_Connection = "True")

# dbGetQuery returns the results from a query to the connection you specify
demo_ScoobyDoo = dbGetQuery(DBADEV2016,
"
SELECT * FROM demo_ScoobyDoo
"
)
# The tab and spacing format is not required; this could have been written as
# demo_ScoobyDoo = dbGetQuery(DBADEV2016, "SELECT * FROM demo_ScoobyDoo")
# but I like to use this format in the case of really long SQL Statements



```


```

# Now that you have a data frame we can do some summary type things with it
# Summarize it
summary(LandingsDF)

# Look at count of unique values
unique(LandingsDF$FullName) %>% length()
unique(LandingsDF$State) %>% length()
unique(LandingsDF$FullLessonName) %>% length()


```


```
# Dates
# Date and POSIXct (date time)

DateFromCharacter = c("2021-01-01", "2021-07-01", "2022-01-01")
DateFromCharacter = as.Date(DateFromCharacter)

# Quick Date Math
x = difftime(DateFromCharacter[2], DateFromCharacter[1], unit = "days") %>% as.numeric()
difftime(DateFromCharacter[1], DateFromCharacter[2], unit = "days")

class(x)

```


```
# Emma; how can I get diff in months or quarters?
# Chris Answer : I think the lubridate package will do it
#   https://www.rstudio.com/resources/cheatsheets/

library(lubridate)
quarter(DateFromCharacter)
# More research needed but it's definitely possible
# From : https://stackoverflow.com/questions/38042910/adding-quarters-to-r-date
# You can't add quarters but you can add months.  This will probably address most use cases
StartingDate = as.Date("2018-01-15")
# Add 7 quarters?  That's essentially 7 * 3 = 21 months!
EndingDate = StartingDate + months(21)
EndingDate
# What if you really wanted the beginning of the quarter that is 7 quarters from now?
PeriodRequested = "month"
floor_date(StartingDate + months(22), unit = PeriodRequested)
# What about the beginning of the next quarter
ceiling_date(StartingDate + months(21), unit = "month")

```


```

# Logan; how does R store dates
as.numeric(as.Date("1970-01-01"))

```


```

# Current Date
Sys.Date()
# Current Time
Sys.time()

```

**Factors**
```
# Factors
FruitTypes <- c("banana", "apple", "watermellon", "pear") %>%
  factor()

# Levels default to alphabetical order
levels(FruitTypes)
FruitTypes[2] %>% as.numeric()


FruitPreference = tibble(
  Name = c("Chris", "Andrew", "Tommy", "Kat"),
  Preference = c("apple", "apple", "watermellon", "pear") %>% factor(levels = FruitTypes)
)


```


**Lists**
Are combinations of other data types and objects
They don't have to be the same type to be a part of the list
They don't have to be the same length to be a part of the list
https://r4ds.had.co.nz/vectors.html?q=lists#visualising-lists

```

FruitTypes
DateFromCharacter

ListOfStuff = list(FruitTypes, DateFromCharacter, CarsDF)
ListOfStuff[[1]]
ListOfStuff[[2]]
ListOfStuff[[3]]
class(ListOfStuff)

GimmeTheDF = ListOfStuff[[3]] %>%
  as.data.frame()

```

**This can be useful in loops**
```

for (i in 1:nrow(CarsDF)) {
  print(CarsDF$model[i])
}

```
