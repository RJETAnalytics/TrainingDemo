# R Boot Camp Session 4

This class is for people who are new to R but also:

- Have intermediate to expert skills in Excel
- Are familiar with basic to intermediate statistics
- Have basic to intermediate skills in some programming language

We will generally be following "R For Data Science" at: https://r4ds.had.co.nz/ which is a much better comprehensive guide than this class.

This class will skip some concepts and accelerate you through this book covering about 50% of what is there.  I advise going back and skimming through the things that do not look familiar as they will absolutely come up later in your R journey.


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
library(lubridate)

```

Let's load up some publicly available data

```

GIT_CharTable = "https://raw.githubusercontent.com/RJETAnalytics/TrainingDemo/main/CharTable.RDATA"
download.file(GIT_CharTable,"CharTable.RDATA", method="curl")
CharTable <- readRDS("CharTable.RDATA")

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

1. Billboard

    a. Join our rank and features data and filter to relevant columns
    
    b. Explore the data and visualize
    
    c. Build a linear model
    
    d. See if the linear model is predictive using test and train splits
    
    
2. Discuss Other Model Types

    a. AOV
    
    b. Classification versus Regression
    
    c. Logistic Regression
    
    d. Tree Models and other more complex models
    
    
3. Missed Concepts

    a. If ... Then and other flow control concepts


```

Billboard_DF = demo_BillboardRank %>%
  dplyr::select(-c(song, performer)) %>%
  inner_join(demo_BillboardFeatures, by = c("song_id" = "song_id")) %>%
  filter(!is.na(spotify_track_popularity))

NumberOnes_DF = Billboard_DF %>%
  filter(week_position == 1) %>%
  group_by(song_id, performer, song, spotify_track_popularity, spotify_genre, mode, danceability, valence) %>%
  summarize(WeeksAt1 = n(), .groups = "drop")

NumberOnes_LM = lm(WeeksAt1 ~ danceability, data = NumberOnes_DF)

```

```
NumberOnes_LM

# What this means
# WeeksAt1 = 2.279 X danceability + 1.485

```

```

summary(NumberOnes_LM)

```

Let's explore the relationship between our target, predictors and the regression model we've built.

```

NumberOnes_DF %>%
  ggplot(aes(danceability, WeeksAt1)) +
    geom_point() +
    theme_classic() +
    labs(title = "Billboard Weeks at #1 vs Spotify Danceability")


```

Now add the data from the model object

```

NumberOnes_DF %>%
  ggplot(aes(danceability, WeeksAt1)) +
    geom_point() +
    geom_line(aes(danceability, NumberOnes_LM$fitted.values), color = "red", size = 2) +
    geom_smooth(method = "lm", formula = "y ~ x", se = FALSE, size = 1, color = "pink") +
    theme_classic() +
    labs(title = "Billboard Weeks at #1 vs Spotify Danceability")

```

We can store the model object
```

SaveFile = "C:\\Users\\Chris.Woolery\\OneDrive - Republic Airways\\Documents\\2022\\NumberOnes_LM.RDATA"

saveRDS(NumberOnes_LM, SaveFile)

x = readRDS(SaveFile)

```



We can plot the residuals (error between predicted and actual) ourselves to see what the bias in the variance is

```

hist(NumberOnes_LM$residuals, breaks = 30)

```

An R also has some nice default plots for most models

```

plot(NumberOnes_LM)

```

```

library(caret)

set.seed(46143)
PartIndex = createDataPartition(NumberOnes_DF$spotify_track_popularity, times = 1,
                                 p = 0.7, list = FALSE)

Popularity_DF = NumberOnes_DF %>%
  mutate(RowID = row_number(),
         SetName = as.factor(if_else(RowID %in% PartIndex, "Train", "Test"))) %>%
  dplyr::relocate(c(RowID, SetName), .before = everything()) %>%
  filter(complete.cases(.))

Popularity_DF %>%
  filter(SetName == "Train") %>%
  pull(spotify_track_popularity) %>%
  summary()

```

```
Popularity_DF %>%
  filter(SetName == "Test") %>%
  pull(spotify_track_popularity) %>%
  summary()
```


```

Popularity_DF_Train = Popularity_DF %>%
  filter(SetName == "Train")

Popularity_DF_Test = Popularity_DF %>%
  filter(SetName == "Test")

```

Let's introduce the concept of [cross validation](https://en.wikipedia.org/wiki/Cross-validation_%28statistics%29#/media/File:K-fold_cross_validation_EN.svg) for our training data


```

set.seed(46143)
pop.cv.folds <- createMultiFolds(Popularity_DF_Train$spotify_track_popularity, k = 5, times = 4)

pop.cv.cntrl <- trainControl(method = "repeatedcv", number = 5,
                         repeats = 4, index = pop.cv.folds)

Popularity_LM <- train(spotify_track_popularity ~ mode + danceability + valence, 
                           data = Popularity_DF_Train,
                           method="lm",
                           trControl = pop.cv.cntrl)

```

```

summary(Popularity_LM$finalModel)

```

```
hist(Popularity_LM$finalModel$residuals, breaks = 30)
```

```
x = predict(Popularity_LM$finalModel, Popularity_DF_Test)

Popularity_DF_Test = Popularity_DF_Test %>%
      mutate(Pred = predict(Popularity_LM$finalModel, Popularity_DF_Test),
             Err = Pred - spotify_track_popularity,
             AbsErr = abs(Err))

Popularity_DF_Train = Popularity_DF_Train %>%
      mutate(Pred = Popularity_LM$finalModel$fitted.values,
             Err = Pred - spotify_track_popularity,
             AbsErr = abs(Err))

ResultDF = bind_rows(Popularity_DF_Test, Popularity_DF_Train)

```

```
  Popularity_DF_Test$Pred %>% summary()

```


```

Popularity_DF_Test %>%
  ggplot(aes(Pred, spotify_track_popularity)) +
    geom_point(aes(color = AbsErr)) + 
    scale_color_gradient(low = "grey", high = "red") +
#    scale_x_continuous(limits = c(1,100)) +
    theme_classic()

```


```

Summary_PopTest = Popularity_DF_Test %>%
  group_by(SetName) %>%
  summarize(MAE = mean(abs(Err)),
            .groups = "drop")

Summary_PopTest  

```
```

ScoobyAOV = aov(imdb ~ monster_real + monster_amount + monster_type,
                           data = demo_ScoobyDoo %>%
                              mutate(monster_type = factor(monster_type)))

ScoobyAOV %>% summary()


```

Flow Control if ... else

```
Cheater = "Larry"

if (Cheater == "Moe") {
  print("Moe Cheated")
} else {
  if (Cheater == "Larry") {
    print ("Larry Cheated") 
} else {
    print ("It was some other stoodge")
  }
}
```

Variable assignment with case ... when
```
TestDF = tibble(
  Note = case_when(
    Cheater == "Moe" ~ "Moe Cheated",
    Cheater == "Larry" ~ "Larry Cheated",
    TRUE ~ "It was some other stoodge"
  )
)

TestDF

```

END
