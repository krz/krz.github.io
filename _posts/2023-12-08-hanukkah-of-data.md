---
layout: post
title: Hanukkah of Data 2023
categories: [r,julia]
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

here's another solution in `Julia`

```R
using CSV, DataFrames

noahs_customer = CSV.File("../5784/noahs-customers.csv") |> DataFrame
transform!(noahs_customer, :name => ByRow(x -> join(split(x, " ")[2:end], " ")) => :last_name)

transform!(noahs_customer, :last_name => ByRow(x -> replace(x, " III" => "", " IV" => "", " Jr." => "")) => :last_name)

function replace_chars(input_string)
    letters = join(Char.(97:122))
    mapping = Dict(zip(letters, ["2", "2", "2", "3", "3", "3", "4", "4", "4", "5", "5", "5", "6", "6", "6", "7", "7", "7", "7", "8", "8", "8", "9", "9", "9", "9"]))
    for (k, v) in mapping
        input_string = replace(lowercase(input_string), k => v)
    end
    return input_string
end

transform!(noahs_customer, :last_name => ByRow(x -> replace_chars(x)) => :last_name_transformed)

transform!(noahs_customer, :phone => ByRow(x -> replace(x, "-" => "")) => :phone)

filter(row -> row.phone == row.last_name_transformed, noahs_customer)
```

## Puzzle 2

`R` solution:
```R
noahs_customers <- read.csv("../5784/noahs-customers.csv")
noahs_orders <- read.csv("../5784/noahs-orders.csv")
noahs_orders_items <- read_csv("../5784/noahs-orders_items.csv")
noahs_products <- read_csv("../5784/noahs-products.csv")

noahs_customers |>
  mutate(name = str_remove_all(name, " Jr.")) |>
  mutate(name = str_remove_all(name, " III")) |>
  mutate(name = str_remove_all(name, " II")) |>
  mutate(name = str_remove_all(name, " IV")) |>
  extract(name, into = c('FirstName', 'LastName'), '(.*)\\s+([^ ]+)$') |>
  mutate(initials = str_c(str_sub(FirstName, 1, 1), str_sub(LastName, 1, 1))) |>
  left_join(noahs_orders, by="customerid") |>
  left_join(noahs_orders_items, by="orderid") |>
  left_join(noahs_products, by="sku") |>
  mutate(order_year = year(shipped)) |>
  filter(initials == "JP" & order_year == 2017 & str_detect(desc, "Bagel")) |>
  select(phone)
```

## Puzzle 3

`R` solution:
```R
noahs_customers <- read.csv("../5784/noahs-customers.csv")

noahs_customers |>
  # year of rabbit
  filter(year(birthdate) %in% c(1927, 1939, 1951, 1963, 1975, 1987, 1999, 2011, 2023)) |>
  # cancer
  filter(month(birthdate) %in% c(6,7)) |>
  # address of person from puzzle 2
  filter(str_detect(citystatezip, "Jamaica, NY 11435"))
# select first one because male
```

`Julia` solution:
```R
using CSV, DataFrames, DataFramesMeta, Dates

noahs_customers = CSV.File(".../5784/noahs-customers.csv") |> DataFrame

@chain noahs_customers begin
    @rsubset(year(:birthdate) in [1927, 1939, 1951, 1963, 1975, 1987, 1999, 2011, 2023])
    @rsubset(month(:birthdate) in [6, 7])
    @rsubset occursin.("Jamaica, NY 11435", :citystatezip)
 end
```

## Puzzle 4

This one was a bit tricky, the result still contains a list of potential candidates that I tried out manually.

`R` solution:
```R
noahs_customers <- read_csv("../5784/noahs-customers.csv") 
noahs_orders <- read_csv("../5784/noahs-orders.csv") 
noahs_orders_items <- read_csv("../5784/noahs-orders_items.csv") 
noahs_products <- read_csv("../5784/noahs-products.csv") 

noahs_customers |>
  left_join(noahs_orders, by="customerid") |>
  left_join(noahs_orders_items, by="orderid") |>
  left_join(noahs_products, by="sku") |>
# filter bakery items by SKU number
  filter(str_detect(sku, "BKY")) |>
# the person likes to shop early
  filter(hour(ordered) < 5 & hour(shipped) < 5) |>
# I assumed that the person is not too old because she likes to bike a lot
  filter(year(birthdate) > 1970) |>
# I assumed the person lives in a neighbouring are of Jamaica, NY, because she biked there
  filter(str_detect(citystatezip, "Brooklyn") | str_detect(citystatezip, "Queens"))
```

`Julia` solution:
```R
using CSV, DataFrames, DataFramesMeta, Dates

noahs_customers = CSV.File("../5784/noahs-customers.csv") |> DataFrame
noahs_orders = CSV.File("../5784/noahs-orders.csv") |> DataFrame
noahs_orders_items = CSV.File("../5784/noahs-orders_items.csv") |> DataFrame
noahs_products = CSV.File("../5784/noahs-products.csv") |> DataFrame


@chain noahs_customers begin
    leftjoin(noahs_orders, on=:customerid, matchmissing=:equal)
    leftjoin(noahs_orders_items, on=:orderid, matchmissing=:equal)
    leftjoin(noahs_products, on=:sku, matchmissing=:equal)
    @rsubset occursin.("BKY", coalesce.(:sku, ""))
    @rsubset(hour(DateTime(:ordered, "yyyy-mm-dd HH:MM:SS")) < 5)
    @rsubset(hour(DateTime(:shipped, "yyyy-mm-dd HH:MM:SS")) < 5)
    @rsubset(year(:birthdate) > 1970)
    @rsubset occursin.("Brooklyn", coalesce.(:citystatezip, "")) || occursin.("Manhattan", coalesce.(:citystatezip, ""))
end
# Renee Harmon 607-231-3605
```

## Puzzle 5

`Julia` solution
```R
@chain noahs_customers begin
    leftjoin(noahs_orders, on=:customerid, matchmissing=:equal)
    leftjoin(noahs_orders_items, on=:orderid, matchmissing=:equal)
    leftjoin(noahs_products, on=:sku, matchmissing=:equal)
    @rsubset occursin.("Cat", coalesce.(:desc, ""))
    @rsubset occursin.("Staten Island", coalesce.(:citystatezip, ""))
    @rsubset :qty >= 10
end
# Nicole Wilson 631-507-6048
```

## Puzzle 6

`R` solution
```R
noahs_customers |>
  left_join(noahs_orders, by="customerid") |>
  left_join(noahs_orders_items, by="orderid") |>
  left_join(noahs_products, by="sku") |>
  group_by(orderid) |>
  mutate(total_cost = sum(wholesale_cost),
         profit = total - total_cost,
         negative_profit_count = sum(profit < 0)) |>
  arrange(desc(negative_profit_count)) |>
first()
# Sherri Long 585-838-9161
```

## Puzzle 7

`Julia` solution
```R
df = @chain noahs_customers begin
    leftjoin(noahs_orders, on=:customerid, matchmissing=:equal)
    leftjoin(noahs_orders_items, on=:orderid, matchmissing=:equal)
    leftjoin(noahs_products, on=:sku, matchmissing=:equal)
    @rsubset occursin.("COL", coalesce.(:sku, ""))
    transform(:ordered => ByRow(x -> DateTime(x, dateformat"yyyy-mm-dd HH:MM:SS")) => :ordered)
    # remove colors in parenthesis
    transform(:desc => ByRow(x -> replace(x, r"\(\w+\)" => "")) => :desc_clean)
end

sherri_items = @chain df begin
    subset(:name => ByRow(x -> x == "Sherri Long"))
    _.desc_clean
end

sherri_dates = @chain df begin
    subset(:name => ByRow(x -> x == "Sherri Long"))
    _.ordered
end

@chain df begin
    @rsubset(Date(:ordered) in Date.(sherri_dates))
    @rsubset(:desc_clean in sherri_items)
end
# Carlos Myers 838-335-7157
```

## Puzzle 8

`Julia` solution
```R
@chain noahs_customers begin
    leftjoin(noahs_orders, on=:customerid, matchmissing=:equal)
    leftjoin(noahs_orders_items, on=:orderid, matchmissing=:equal)
    leftjoin(noahs_products, on=:sku, matchmissing=:equal)
    @rsubset occursin.("COL", coalesce.(:sku, ""))
    groupby(:name)
    transform(:name => length)
    @orderby -:name_length
    first()
end
# James Smith 212-547-3518
```
