---
layout: post
title: Hanukkah of Data 2023
categories: [r]
---

Are you ready for a thrilling data adventure? [Noah’s Rug](https://hanukkah.bluebird.sh/5784/) is an awesome data game that challenges you to solve puzzles using a fictional dataset. Every day of Hanukkah, you can light up a candle and discover a piece of a mysterious tapestry. After the last day, you can test your skills in a speedrun mode. You can use any tools you want to explore the data and find the answers.
This is a collection of my solution with some explanations.

## Puzzle 1

read in the data (available on the challenge website)
```R
library(tidyverse)
noahs_customers <- read_csv("../noahs-csv/5784/noahs-customers.csv")
```

clean the data, especially the names, then split into first and last name
```R
noahs_customers <- noahs_customers |>
  mutate(name = str_remove_all(name, " Jr.")) |>
  mutate(name = str_remove_all(name, " III")) |>
  mutate(name = str_remove_all(name, " II")) |>
  mutate(name = str_remove_all(name, " IV")) |>
  mutate(phone = str_remove_all(phone, "-")) |>
  extract(name, into = c('FirstName', 'LastName'), '(.*)\\s+([^ ]+)$')
```

function to map letters to their phone buttons
```R
replace_chars <- function(input_string) {
  mapping <- letters
  replacement <- c("2", "2", "2", "3", "3", "3", "4", 
                   "4", "4", "5", "5", "5", "6", "6", "6", "7", "7", 
                   "7", "7", "8", "8", "8", "9", "9", "9", "9")
  for (i in seq_along(mapping)) {
    input_string <- gsub(mapping[i], replacement[i], tolower(input_string))
  }
  return(input_string)
}
```
apply this function
```R
noahs_customers$LastName_Number <- sapply(noahs_customers$LastName, replace_chars)
```
find the name where phone number and last name matches
```R
noahs_customers |> filter(LastName_Number == phone)
```

