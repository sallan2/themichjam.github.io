Please install R in advance by following instructions for [installing R and RStudio on your own machine](https://rstudio-education.github.io/hopr/starting.html)

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
We can also interact with files outside of our R script, like loading data. Such as the data that details the percentage of women in parliaments around the world [download here]

```{r, echo=FALSE, message=FALSE}
wip_data <- read_csv('WB-WiP.csv')
```
