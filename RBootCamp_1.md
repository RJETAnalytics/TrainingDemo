# R Boot Camp Day 1

This class is for people who are new to R but also:

- Have intermediate to expert skills in Excel
- Are familiar with basic to intermediate statistics
- Have basic to intermediate skills in some programming language

We will generally be following "R For Data Science" at: https://r4ds.had.co.nz/ which is a much better comprehensive guide than this class.

This class will skip some concepts and accelerate you through this book covering about 50% of what is there.  I advise going back and skimming through the things that do not look familiar as they will absolutely come up later in your R journey.


R is a statistical based programming language.  
Basic Calculations and Functions can be ran in the console. 

```
1+1
```


```
mean(c(5,5,6))
```


**R Studio IDE 101**

The 4 Windows :

- Source : Code
- Console : Console plus Terminal, Markers, Jobs
- Help : Help plus Plots, Packages, Files and Viewer
- Environment : Environment plus history, Connections and Tutorial

**R Document Types**

There are main two types of documents to run R code in:

**R Scripts :** File extension of .R; only contain code and commented lines of code.  R Scripts are what we use for production and full unattended automation.

**R Notebooks :** File extension of .RMD.  Notebooks are for exploration and publishing, definitely our favored tool for exploratory data analysis when excel is not going to be powerful enough.  Notebooks contain code, charts and formatted comments.  Code is run in small self contained blocks.

There is another type of document:

**R Data Files : ** File extension of .RDATA These are useful for storing either single variables or the entire environment of the current session and recalling it later.  These are the most useful when building the data objects takes a long time and you want to save your work as you go OR for automation purposes it's useful to store complex MODELS of data so that you can recall and run your model without re-creating it.  For long term storage and retrieval of DATA, we obviously prefer to use SQL Server or other database tools as they are more powerful and more widely accessible by other tools like Power BI

**Mini Lessons**

**Straight into Visualization**
First we'll grab the data
```
library(tidyverse)
# Get the default mpg data frame (table)
data(mpg)
View(mpg)

```


Build a simple plot with ggplot
```

ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy))

```

ggplot build graphics in LAYERS.  You can follow this format:

ggplot(data = <DATA>) + 
  <GEOM_FUNCTION>(aes(x = , y = , color =, fill = , size =, shape =, alpha = )) +
  OTHER ELEMENTS
  
Typical GEOM_FUNCTIONS
** geom_point
** geom_line
** geom_col
** geom_histogram

Here is the full manual on ggplot along with the first of many "Cheat sheets":
https://ggplot2.tidyverse.org/index.html

Let's change some of these elements for our geom_point chart using class and cyl
```

ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, color = class, size = cyl))  

```

You can also "hard code" these display elements by leaving them outside of the aes() assignment
```

ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy), color = "light grey", size = 3)  

```

Regarding colors.  Most common colors are there along with "dark" and "light" prefixes but for truely custom color you can use a custom color like "#FF33FF"

```

ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy), color = "#FF33FF", size = 3) 

```

These custom colors are RGB Hexadecimal.  First two positions are Red, 2nd two are Green, 3rd two are Blue.  Values are on the hexadecimal scale from 0 to F (16 possible values per position). 
FFFFFF is pure white 
000000 is pure black 

Good Visual Resources For Choosing Colors 
https://color.adobe.com/create/color-wheel 
https://htmlcolorcodes.com/color-picker/ 


GGPlot has a very cool feature I like to over-use called faceting

```
# facet_grid will build a mini-plot (facet) for every intersecting value of X and Y
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ cyl)

# facet_wrap will build a mini-plot (facet) only for those elements that have values
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(drv ~ cyl)


```

Using the + method to add layers you can include interesting features like a fitted line
```

# Notice how the aesthetics are included in the primary plot call
ggplot(data = mpg, aes(x = displ, y = hwy)) +
  geom_point(color = "dark blue", size = 3) +
  geom_smooth(method = "lm", formula = 'y ~ x', se = FALSE, color = "dark red") +
  theme_minimal() +
  labs(title = "This is a Demo Plot",
       subtitle = "More text",
       caption = "Even more text",
       y = "Highway Miles",
       x = "Displacement in Liters")
# We can change the line type, exclude the st
```

**Creating variables**
```
# Scalar (Single Value)
x <- 5 

# Creating a vector variable
v <- c(5,5,6)
v = c(5,5,6)
Students <- c("Larry", "Moe", "Curly")

# Choose the nth element of a vector
Students[3]

```


**Auto creating numeric vectors**
```

Numbers001To100 <- seq(1, 100)

OddNumbers001To100 <- seq(1, 100, by = 2)

RepeatedNumbers <- rep(1:10, 10)
```


**Mathematical Operators**
```
# The usual suspects
1 + 1

10 - 9

2 * 2

14 / 8

# Integer division (truncate or floor the decimals)
14 %/% 8

# Modulus or remainder
14 %% 8

# Exponents
2^3

```


**Linear Algebra Matrix'**

This section w
```
# Creating a matrix
Matrix3By2 <- matrix(c(1,2,
                       3,4,
                       5,6), 
                     nrow = 3, ncol = 2, byrow = TRUE)


Matrix3By3 <- matrix(c(1,2,3,
                       4,5,6,
                       7,8,9), 
                     nrow = 3, ncol = 3, byrow = TRUE)
Matrix3By3

```


```

# Matrix multiplication
Matrix3By3 %**% matrix(c(1,10,100), 
                      nrow = 3, ncol = 1)

# Check Answer
matrix(c(1**1 + 10**4 + 7**100,
         2**1 + 10**5 + 8**100,
         3**1 + 10**6 + 9**100), 
         nrow = 3, ncol = 1)

# Linear Algebra Flashback; what's going to happen
Matrix3By3 %**% matrix(c(1,0,0,
                        0,1,0,
                        0,0,1), 
                      nrow = 3, ncol = 3)

Matrix3By3 %**% Matrix3By3


```


**Knowing Variable Types**
**What is it?  Use str(), class() and summary()**
```
str(v)
class(v)
class(mpg)
```

**Assign Variable Types**
```

# most follow the form of as.

# A character vector
CharacterVector = c("1", "3", "5", "7")
# Convert to numeric
NumericVector = as.numeric(CharacterVector)
sum(NumericVector)

# Convert back to character
CharacterVector = as.character(NumericVector)

```

**Data Frames**
Data frames are a collection of rows and columns.  whereas a matrix consists of only one data type, a data frame can have columns that contain multiple data types.

R handles data frames naturally but it handles them even better if we load the tidyverse library.  Here is how you load a library
```

library(tidyverse)

```


```
# Note that the mpg dataset is included with R
data(mpg)
mpg = mpg

```

**Using the Tidyverse packages**

**Filtering**
```
CarsDF_6Cyl <- filter(mpg, cyl == 6)

```

**The Pipe %>%**
Write using less repeated code
It "pipes" (like a baking piping bag) the result into the first value of the next function

```

# Method 1 : Repetitive lines
CarsDF_6Cyl <- filter(mpg, cyl == 6)
CarsDF_6Cyl <- filter(CarsDF_6Cyl, c >= 20)

# Method 2 : Pipe
CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6) %>%
  filter(hwy >= 20)

# Logical Operators
# Operators are AND = &, OR = |, NOT = !

# Method 3 : Pipe plus logical operators

CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 & !hwy >= 20) 

CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) 

```


**Sorting with Arrange**
```

CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) %>%
  arrange(cyl, desc(hwy))

```


**Selecting and de-selecting columns**
```

CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) %>%
  arrange(cyl, desc(hwy)) %>%
  select(hwy, cyl, drv)

# This is the function we all wish SQL had
CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) %>%
  arrange(cyl, desc(hwy)) %>%
  select(-c(trans, fl))

# Might need to explicitly name the library that houses the function
CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) %>%
  arrange(cyl, desc(hwy)) %>%
  dplyr::select(-c(trans, fl))

```

**Select helpers** 
Can make selecting multiple columns easier
https://dplyr.tidyverse.org/reference/select.html
```

CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) %>%
  arrange(cyl, desc(hwy)) %>%
  dplyr::select(contains(c("m", "c")))

CarsDF_6Cyl = filter(mpg, cyl == 6 | hwy >= 20)
CarsDF_6Cyl = arrange(CarsDF_6Cyl, cyl, desc(hwy))
CarsDF_6Cyl = select(CarsDF_6Cyl, contains(c("m", "c")))


```

**Relocating Columns**
Want to move columns around but don't want to name them all in the select statement?
```

# Using Select with Everything to rearrange columns
CarsDF <- mpg %>%
  select(class, drv, everything())

# Or just picking one column with relocate
CarsDF <- mpg %>%
  relocate(class, .before = everything())

# Need help on a function
?dplyr::relocate()

```


**Renaming columns**
```
CarsDF_6Cyl <- mpg %>%
  filter(cyl == 6 | hwy >= 20) %>%
  arrange(cyl, desc(hwy)) %>%
  dplyr::select(contains(c("m", "c"))) %>%
  rename(CityMiles = cty,
         VehicleClass = class,
         Cylinders = cyl)
```

**Adding new variables with mutate**
```

# Using DF Fields Only
CarsDF <- mpg %>%
  mutate(DispPerCyl = displ / cyl,
         RoundedDispPerCyl = round(DispPerCyl, 1))

# Using DF Fields Plus A Constant
CarsDF <- mpg %>%
  mutate(DispPerCyl = (displ / cyl) ** 10,
         RoundedDispPerCyl = round(DispPerCyl, 1))


# Using DF Fields Plus A Variable
Multiplier = 10
CarsDF <- mpg %>%
  mutate(DispPerCyl = (displ / cyl) ** Multiplier,
         RoundedDispPerCyl = round(DispPerCyl, 1))

# Using DF Fields Plus A Vector is OK only as long as its' a multiple
Vector = c(10, 5, 10)
CarsDF <- mpg %>%
  mutate(DispPerCyl = (displ / cyl) ** Vector,
         RoundedDispPerCyl = round(DispPerCyl, 1))


```


**Groups and aggregation**
Basic Aggregation
```

AGGCarsDF <- mpg %>%
  group_by(cyl) %>%
  summarise(ModelCount = n(),
            AvgMPG = mean(hwy), 
            .groups = "drop")

```

Line versus Aggregate
```

AGGCarsDF <- mpg %>%
  group_by(cyl) %>%
  mutate(AvgMPG = mean(hwy),
         VarvsAvgMPG = hwy - AvgMPG) %>%
  ungroup() %>%
  arrange(cyl, desc(VarvsAvgMPG))

```

**Distinct and Unique**
```

# Unique is for vectors
unique(mpg$cyl)

# Distinct is for data frames
DistinctCarsDF <- mpg %>%
#  select(cyl) %>%
  distinct(manufacturer, cyl)

```

**Save whole environment**
```

SaveFile = "C:\\Users\\Chris.Woolery\\OneDrive - Republic Airways\\Documents\\2022\\R Boot Camp 2022 V2.RDATA"
save.image(SaveFile)

```

**Special For Kat**
```
library(tidyverse)
# Need to install quantmod and lubridate
library(quantmod)
library(lubridate)

# Function to convert yahoo stock quotes to a data frame
StockToDF = function(XTSObject, StockName) {
  data.frame(XTSObject) %>%
      rename(CloseValue = 4) %>%
      mutate(SymbolName = StockName,
             QuoteDate = as.Date(row.names(.), "%Y-%m-%d"),
             WeekBeg = floor_date(QuoteDate, unit = "week")) %>%
      group_by(WeekBeg) %>%
      mutate(MaxDate = max(QuoteDate)) %>%
      ungroup() %>%
      filter(QuoteDate == MaxDate) %>%
      dplyr::select(SymbolName, WeekBeg, CloseValue) %>%
      filter(!is.na(CloseValue))}

# Function to convert FRED bond quotes to a data frame
BondToDF = function(XTSObject, BondName) {
  data.frame(XTSObject) %>%
    rename(CloseValue = 1) %>%
    mutate(SymbolName = BondName,
           QuoteDate = as.Date(row.names(.), "%Y-%m-%d"),
           CloseValue = CloseValue / 100,
           WeekBeg = floor_date(QuoteDate, unit = "week")) %>%
    group_by(SymbolName, WeekBeg) %>%
    summarise(CloseValue = mean(CloseValue, na.rm = TRUE),
              .groups = "drop")}

StockSymbols = c("^GSPC", "EVE")
BondSymbols = c("DFF", "DGS1","DGS2", 
            "DGS5", "DGS10", "DGS20", 
            "DGS30")

# Get the S&P and Individual Stocks from Yahoo
getSymbols(StockSymbols, src='yahoo', from = '1900-01-01', 
           to = Sys.Date(), warnings = TRUE)

# Call use QuantMod's API into FRED
getSymbols(BondSymbols, src = "FRED")

AllSymbolsDF = bind_rows(
  StockToDF(GSPC, "SP500"),
  StockToDF(EVE,"EveEvtol"),
  BondToDF(DFF, "FedFunds"),
  BondToDF(DGS1, "Tres01Yr"),
  BondToDF(DGS2, "Tres02Yr"), 
  BondToDF(DGS5, "Tres05Yr"), 
  BondToDF(DGS10, "Tres10Yr"), 
  BondToDF(DGS20, "Tres20Yr"), 
  BondToDF(DGS30, "Tres30Yr"), 
)

# You can look at the AllSymbolsDF to see Eve plus Treasury benchmarks

```

