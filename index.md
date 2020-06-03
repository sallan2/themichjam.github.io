# Before We Begin

1. Please install R in advance by following instructions for [installing R and RStudio on your own machine](https://rstudio-education.github.io/hopr/starting.html)

2. The data needed for this workshop can be [downloaded here](https://social.sgsss.ac.uk/groups/a-visual-introduction-to-r/files/)

# Welcome

Coding is for everyone! If you think it isn't for you, give it a reconsider, because I truly believe everyone can learn to code and be a data scientist! Coding skills provide tools to enhance communication and showcase your data and ideas to a wide range of audiences and disciplines in an accessable way - which is a plus for everyone! Showcasing your data in these new ways could help you look at your data from a different angle and lead to a deeper understanding of your findings. With the open science movement gaining traction in all disiplines, more data will become freely accessible, and learning data science and visualization skills will empower you to take advantage of that! R is incredibly useful (and cutting-edge) in all disiplines, and this workshop will help provide the basics of a solid, rigerous, and transparant workflow.

This workshop is sponsored by the [Scottish Graduate School of Social Science](https://www.sgsss.ac.uk/summer-school-2020/).


# Part 1: Setting up and reading in data

## Introduction

This is the most basic introduction to R that his meant to be a jumping off point in your journey! This is only a short workshop after all! Some of this work has been influenced by the [PsyTeachR](https://psyteachr.github.io/) and [Q-Step](https://www.gla.ac.uk/schools/socialpolitical/q-stepcentre/) iniatives at the University of Glasgow, and Saghir Bashir's work with the [Women in Parliament dataset](https://github.com/saghirb/WiP-tidyverse).

Please take a look at these resources in your own time!

## Setup

First things first, we have to set our *Working Directory* to the place our file is. In RStudio we do this by hitting 'Session > Set Working Directory > Source File Location'. This lets us interact with other files that are outside of our current script (and keeps our project nice and tidy).

In RStudio in Rmd (spoken: "R Markdown") files, you can run either one section of code by hitting 'Run > Run current chunk' or you can run every section of code by hitting 'Run > Run all'.

A Chunk of code is defined as everything within the three ` marks, like this
    ```{r}`r ''`
    # The ``` means 'format this section in a certain way'
    # the {r} means 'interpret this section as R code, not as plain text'
    # the second ``` means the section is over
    
    # Because we're in an Rmd file and we're in an {r} chunk, all writing is going to 
    # go in comments. Any line that starts with a # is a comment. 
    # In RStudio, these are conveniently green-ed out
    
    ```

You'll learn much more about formatting Rmd files, 

First we load in libraries (also known as packages). These are things that we've either already installed, or come installed in R or RStudio. Functionally, this is just creating an environment with a bunch of code that someone else has written for us. 

```{r}
install.packages("tidyverse")
install.packages("countrycode")
library(countrycode)
library(tidyverse)

set.seed(1234)
```
When we do things that require "random" or stochastic functions, we set the seed first for reproducibility. This way someone else can run our code and get the same result, even though the function uses "randomness"

## Using the Console, Variables, and Data Types

### Console and Variables
RStudio generally has four panels: Current file, Console, Environment, and Viewer. You can think of the console as a place to try things out, and the file to write down ideas you want to stick around. Go to the console and type 

```{r}
x <- 1 + 5
```
Notice how now the environment shows we have a Value `x` that is 6. Congrats, we just created our first  *variable*! 
In the above, we would say "the variable x is assigned to 1 + 5" or "x gets 1 + 5"

We can do this within the script by simply having a chunk like so: 
```{r}
y <- 2 + 4
```
And running the chunk. Notice we now have `y` up there hanging out with `x` too.


### Data Types

Both `x` and `y` in this case are numbers. We can see this by typing 
```{r}
typeof(x)
```
in the console. It will print "double" which, for all intents and purposes, is a regular old number.

We can create variables of any type
```{r}
first_string <- "Hello, World"
first_vector <- c(2, 4, 6)
first_integer <- 4L
first_logical <- TRUE
```

Notice that the first two variables here are really a collection of other variables -- 
`first_string` is a *vector* of *characters*, and `first_vector` is a *vector* of *numerics*. 
Because of this property, we can *index* into them:
```{r}
second_value <- first_vector[2]
```
This creates a new variable with the value of the second item in `first_vector`.

### Functions
We can also take advantage of everything we loaded into the library by using *functions*.

For instance, since we loaded in the `cowsay` library, we can write
```{r}
say("This is using a function!")
```

When we run this line we see it produce output. This is because we *called* the function `say`
Calling a function is as easy as that! Here's the basic anatomy of a function call:
  ```
function_name(argument1, argument2, ...)
```
where the arguments are the *parameters* of the function -- the things the function needs to do its thing. 

### Loading Data
We can also interact with files outside of our R script, like loading data. Such as the data that details the percentage of women in parliaments around the world which you downloaded earlier. 

```{r, echo=FALSE, message=FALSE}
wip <- read_csv(("WB-WiP.csv"), skip = 4) 
```
Now when we click on `wip_data` in our environment section, we can see the data we've imported! And as we'll go into in the next section, there is a problem with this data just now!

# Part 2: Exploring Data with Tidyverse

When we read our data in at the end of Part 1, this error message appeared: 

```{r}
# Warning: Missing column names filled in: 
# 'X64' [64]
```

We have suppressed some of the messages but left the one about variable X64 (we will come back to it below).

Exercise:

Check what you have read by typing in the Console window:
```{r}
wip
```

What do you see?

Now type:
```{r}
class(wip)

glimpse(wip)
```

Does this conﬁrm that “wip” is of class “tbl_df”?

“Fix” column names. Some of the column names contain spaces while others are numeric:
```{r}
head(names(wip)) 

tail(names(wip)) 
```
By using the make.names() function we don’t need to use back ticks (`) around the column names (e.g. `col name`). 

```{r}
names(wip) <- make.names(names(wip)) 
head(names(wip))
tail(names(wip))
```

## Data Wrangling 

We can simplify the production of summaries and plots by restructuring the current wip dataset (which has 64 columns) to a table format with 'Country', 'Year', and 'pctWIP' (pctWiP refers to the percentage of women in parliament).

These three columns will contain the same information as the wip dataset but in a more usable format. We will also add a variable for the ratio of male to female MPs.

### Superﬂuous Columns

We will start by removing columns 'X65', 'Indicator.Name' and 'Indicator.Code'. There are years without any data but they will be removed automatically later (when restructuring from “wide” to “long” format). Column X65 is created automatically due to an extra comma at the end of the column names (ﬁfth) line of WB-WiP.csv: 

... ,"2016","2017","2018","2019",

Before removing it check that all values are NA. 

```{r}
wip %>% 
  pull(X64) %>% 
  is.na(.) %>% 
  all(.) 
```

Column Indicator.Name has the unique value “Proportion of seats held by women in national parliaments (%)” and in Indicator.Code it is “SG.GEN.PARL.ZS”. As there is only one indicator in this dataset we will remove these two columns.

Exercise. Conﬁrm that both Indicator.Name and Indicator.Code have the same values for all observations. Hint: Use either count() or distinct() functions.

Removing columns.The indicator and X64 columns can be removed. We will also rename “Country.Name” as “Country” and “Country.Code” as “Code”. 

```{r}
wip2 <- wip %>% select(-Indicator.Name, -Indicator.Code, -X64) %>% rename(Country=Country.Name, Code=Country.Code) 
head(names(wip2)) 
tail(names(wip2))
```

Reshape to long format. We want to transform the data so that for each country the year (column) data becomes a row. At the same time we will remove the missing data (with the na.rm option). We will also create a numeric Year variable and a Ratio of men to women in parliament. 

```{r}
WP <- wip2 %>% 
  pivot_longer(starts_with("X"), 
               names_to = "YearC", 
               values_to = "pctWiP", 
               values_drop_na = TRUE) %>% 
  mutate(Year = parse_number(YearC), 
         Ratio = (100-pctWiP)/pctWiP) %>% 
  select(Country, Code, Year, pctWiP, Ratio) %>% 
  arrange(Country, Year) 

# Look at the contents of WP glimpse(WP) 
glimpse(WP)
```

## Questions

The objective is to look at the geographical and time trends in the data. We will answer the following questions.

1. What are the time trends for Portugal?
2. How does Portugal compare to other countries?
3. Which countries have the highest percentage of women in parliament by year?
4. How do continents compare?
5. What are the global trends over time?

Exercise - Without Programming.

1. Which country do you think has the highest percentage of women in parliament?
2. In each continent (i.e. Africa, Americas, Asia, Europe and Oceania), which country has the highest percentage of women in parliament?
3. What is the world percentage of women in parliament in 2019?


## Exploratory Analysis 

Select a country.This guide explores how Portugal performs over time and compared to other countries. Before continuing, select another country for yourself to repeat the examples and do the exercises.

Time trends for Portugal. First look at the raw data. 
```{r}
# Reset tibble print option to see more rows
options(tibble.print_max = 25) 
WP %>% filter(Country=="Portugal") 
```

Visualisation. It is easier to ﬁnd trends within a plot.
```{r}
WP %>% filter(Country=="Portugal") %>% 
  ggplot(aes(Year, pctWiP)) + 
  geom_line() + geom_point() + 
  scale_y_continuous(limits=c(0, 50)) + 
  ylab("% Women in Parliament")
```

Interpretation. In 1990 Portugal had 7.6% women in parliament (i.e. 12.2 men for each woman), which increased to 34.8% (i.e. 1.87 men for each woman) in 2018. This still falls short of 50% (i.e. point of gender parity in parliament).

Exercise.For your chosen country look at the time trend data and the plot. What is your interpretation? How does it compare to Portugal?

Portugal versus European Union(EU) countries. We selected six EU countries (due to space limitations) for comparison. It would be better to compare all EU and/or all European countries. 

```{r}
WP %>% 
  filter(Country %in% c("Portugal", "Sweden", 
                        "Spain", "Hungary", "Romania", "Finland",
                        "Germany", "European Union")) %>%
  ggplot(aes(Year, pctWiP, colour=Country)) + 
  geom_line() + 
  geom_point() + 
  scale_x_continuous(breaks=seq(1990, 2020, 5)) +
  scale_y_continuous(limits=c(0, 50), breaks=seq(0, 50, by=10)) +
  ggtitle("Women in Parliament: EU Countries") + 
  ylab("% Women in Parliament")
```

Interpretation. Since 2007 Portugal ha shad more women in parliament than the European Union average. Hungary and Romania both had a higher percentage of women in parliament in 1990 (around the end of the Col dWar) than they have had since. The key point to note is that none of these countries reaches equality between males and females in parliament, although Sweden and Finland come closest

A couple of points to note.

“Germany”. In October 1990, the process of “German reuniﬁcation” lead to the creation of Germany, which united the former “German Democratic Republic”(East Germany) and the“Federal Republic of Germany” (West Germany). Therefore, since reuniﬁcation, the data is presented for the reuniﬁed “Germany” only. Careful thought should be given to handling, analysing and interpreting any pre-reuniﬁcation data (if available).

“European Union”. The “European Union” has changed over time (unlike the “continent of Europe”). It started in the 1950s as a block of six European countries (known as the “European Community”) and has expanded over the years to 28 countries (with the United Kingdom about to depart). This raises the question of how the European Union average is calculated. For a given year, is it calculated based on the actual member states in that year or on all of the current member states?

Exercises:

1. Compare the country of your choice to four or ﬁve other countries by plotting a line graph similar to the one above.

Countries with the highest percentage of women in parliament. A quick answer can be obtained by looking at the highest percentages. 

```{r}
WP %>% 
  arrange(-pctWiP) %>% 
  head(10) 
```

Highest percentage by year. Which countries have the highest percentage of women in parliament by year? 

```{r}
WP %>% 
  group_by(Year) %>% 
  arrange(Year, -pctWiP) %>% 
  filter(row_number()==1)
```

Merging continent. The variable Country in the WP dataset is a mix of countries and regions (e.g. “European Union”, “South Asia”and“World”). To present the highest percentages grouped by continent we need to add it. Luckily, given the large number of R packages available, we can merge the “continent” from the “codelist” dataset in the “countrycode” package

```{r}
cl <- codelist %>% 
  select(continent, wb) %>% 
  rename(Code = wb, Continent = continent) cWP <- WP %>% left_join(cl, by = "Code")

```

Highest percentages by year and continent. Which countries have the highest percentages in 1990 and 2018? 

```{r}
cWP %>% 
  filter(Year %in% c(1990, 2018) & !is.na(Continent)) %>%
  group_by(Continent, Year) %>% 
  arrange(Continent, Year, -pctWiP) %>% 
  filter(row_number()==1) %>% 
  select(Continent, Country, Year, pctWiP, Ratio) 
```

Decline in percentage. Which countries have had a decline in percentage since their ﬁrst measurement (not always 1990)? 

```{r}
dWP <- cWP %>% 
  group_by(Country) %>% 
  arrange(Country, Year) %>% 
  filter(row_number()==1 | row_number()==n()) %>%mutate(pctDiff = pctWiP lag(pctWiP, order_by=Country)) %>% 
  filter(pctDiff<0 & !is.na(Continent)) %>% 
  arrange(pctDiff) dWP %>% 
  select(Country, pctDiff) 
```

Visualisation. We will plot the trend lines for countries with at least a 5% decline. Note that the “5%” is arbitrarily selected. 

```{r}
# Select the countries to plot 
dclpct <- dWP %>% 
  filter(!is.na(Continent) & pctDiff <= -5) %>% 
  pull(Country)
WP %>% 
  filter(Country %in% dclpct) %>% 
  ggplot(aes(Year, pctWiP, colour=Country)) + geom_line() +
  geom_point() + scale_x_continuous(breaks=seq(1990, 2020, 5)) +
  scale_y_continuous(limits=c(0, 40), breaks=seq(0, 40, by=10)) +
  ggtitle("Women in Parliament: Decline >=5%") + ylab("% Women in Parliament")
```


Interpretation. There is a consistent decline between 1990 and 1997 that should be investigated in collaboration with a subject matter expert to understand the potential causes.

Ranked status.Another way to look at the data is to look at the rankingofcountries, whichcouldbedoneatagloballevelorby continent. Nonetheless, the results should be interpreted with caution and an understanding of the actual percentages. For example,ifmostcountrieswerearoundthe50%mark,rankings could be misleading and subject to random ﬂuctuations.

Global ranks by year.We will rank the countries by year based on the percentage of women in parliaments. The countries with the highest percentage will be ranked ﬁrst and the lowest last. A total for the number of countries with data is included as it varies by year. 

```{r}
cWPrankG <- cWP %>% 
  filter(!is.na(Continent)) %>% 
  group_by(Year) %>% 
  mutate(RankG = rank(-pctWiP), TotalG = n())
```

Global ranking – Portugal

```{r}
cWPrankG %>% 
  filter(Country=="Portugal") %>% 
  select(Country, Year, pctWiP, Ratio, RankG, TotalG) %>% arrange(Year)
```

Interpretation. Portugal has generally been ranked in the ﬁrst quartile (25%) of countries in the world, with the ﬂuctuations of its ranking most likely due to random variation.
Exercise. For your chosen country, interpret its ranking over the years. How does it compare to Portugal?
Continent ranks by year.We will rank the countries by year within a continent based on the percentage of women in parliaments. The countries with th ehighest percent age will be ranked ﬁrst and the lowest last. A total for the number of countries with data, within each continent, is included as it varies by year.

```{r}
cWPx <- cWPrankG %>% 
  filter(!is.na(Continent)) %>% 
  group_by(Continent, Year) %>% 
  mutate(RankC = rank(-pctWiP), TotalC = n())
```

Portugal’s ranking in Europe.

```{r}
cWPx %>% 
  ungroup() %>% 
  filter(Country=="Portugal") %>% 
  select(Country, Year, pctWiP, Ratio, RankC, TotalC) %>% arrange(Year)
```


Plot of Portugal’s ranking in Europe.Below we reproduce the percentage plot to show how Portugal ranks in relation to six other European countries. Note that the highest percentage is ranked ﬁrst and the lowest last.

```{r}
cWPx %>% 
  filter(Country %in% c("Portugal", "Sweden", "Spain", "Hungary", "Romania", "Finland", "Germany")) %>% 
  ggplot(aes(Year, RankC, colour=Country)) + geom_line() +
  geom_point() + scale_x_continuous(breaks=seq(1990, 2020, 5)) +
  scale_y_continuous(limits=c(0, 45), breaks=seq(0, 45, by=10)) +
  ggtitle("Women in Parliament: Ranked") + ylab("Rank in Europe")
```

Interpretation. A total of 28 European countries had data in 1990, 39 in 1997 and 43 in 2018. Within Europe, Portugal was typically ranked in the second quartile (25-50%) with the ﬂuctuations of its ranking most likely due to random variation.

Exercise. How does your chosen country rank within its continent?

Highest rank by year and continent.Which countries have the highest rank in 1990 and 2018? The answer will coincide with the highest percentages (see above).

```{r}
cWPx %>% 
  filter(Year %in% c(1990, 2018) & RankC==1) %>% 
  arrange(Continent, Year) %>% 
  select(Continent, Year, Country, pctWiP, Ratio) 
```

Overallpicture.Whatarethetrendsglobally? Therearevarious regions deﬁned in the World Bank data. We can plot them and highlight the world “average”.

```{r}
cWP %>% 
  filter(is.na(Continent)) %>% 
  ggplot(aes(Year, pctWiP, group=Country)) + 
  geom_line() + gghighlight(Country=="World", use_direct_label =
                              FALSE, use_group_by = FALSE) +
  scale_x_continuous(breaks=seq(1990, 2020, 5)) +
  scale_y_continuous(limits=c(0, 40), breaks=seq(0, 40, by=10)) +
  ggtitle("Women in Parliament: Global Trends") + ylab("% Women in Parliament")
```

Interpretation. The grey lines show that regardless of how we deﬁne region the general trends are upwards. The “World” percentage(blackline)increasedbetween1997and2018. In2018, women in parliament represented 24% (i.e. a ratio of 3.17 men to each woman), which is still less than half the level before gender parity can be claimed.

## Conclusion 

This guide presented an analysis of the percentage of women in parliament as a real-life case study for some of the tidyverse package. Although the format limited what could be presented, we can conclude that the percentage of women in parliament is increasing but that gender parity in parliaments is still far-off. There is a lot more that can be said and discussed about the limitations, interpretation and potential impact of this data whichtheWorldBankhasnicelysummarised.7 Youarestrongly encouraged to read their discussion for a more complete understanding.

