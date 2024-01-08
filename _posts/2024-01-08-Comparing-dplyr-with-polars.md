---
layout: post
title: Comparing dplyr with polars
categories: [r,python]
---

Data manipulation is one of the most common and essential tasks in data analysis. 
Whether you are working with tabular data, time series, spatial data, or any other kind of data, you need to be able to perform operations such as filtering, grouping, aggregating, joining, reshaping, and more. 
In the R world, the **dplyr** package is one of the most popular and powerful tools for data manipulation. 
It provides a consistent and expressive syntax based on the **tidyverse** principles, which make it easy to write readable and maintainable code. 
Many other packages extend its functionality, allowing you to work with large and diverse data sources.

However, if you are a Python user, you may not have a clear and easy choice for data manipulation. The (by far) most popular package is **pandas** but I always found it's syntax confusing, especially if you have strong dplyr background. 

That is, until now. After reading [Emily Riederer's excellent blog](https://www.emilyriederer.com/post/py-rgo/) I discovered **polars**, a new Python library for data manipulation that aims to fill the gap between dplyr and pandas. 
Polars is a fast and expressive library that offers a syntax similar to the tidyverse, while being extremely fast and scalable. 
It leverages Apache Arrow as its underlying data structure and is written in Rust, which enables efficient memory management and interoperability with other tools. 
It also supports lazy evaluation, parallel processing, and query optimization, which make it suitable for working with large and complex data sets.
[According to some benchmarks](https://duckdblabs.github.io/db-benchmark/), polars is one of the fastest tools for handling and manipulating data.

In this blog post, we will compare the dplyr and polars libraries and see how they can help us perform common data manipulation tasks. 
We will use a simple data set of the **palmerpenguins** package, the [new Iris data set](https://allisonhorst.github.io/palmerpenguins/articles/intro.html). We will see how to load, filter, group, join, and reshape the data using both libraries, and compare their syntax, performance, and output. 
We will also discuss some of the advantages and disadvantages of each library, and provide some tips and resources for further learning.

# Reading data
Reading a csv file in **polars** is extremely easy (and similar to pandas).
The following code reads in the *palmerpenguins* data set (from a github page).
In polars, **NA** values are **null** so it's important to specify this using the **null_values** parameter.

```python
import polars as pl

df = pl.read_csv("https://gist.githubusercontent.com/slopp/ce3b90b9168f2f921784de84fa445651/raw/4ecf3041f0ed4913e7c230758733948bc561f434/penguins.csv", null_values="NA")
```

polars also supports a [scan_csv](https://docs.pola.rs/py-polars/html/reference/api/polars.scan_csv.html) method for lazy loading, this is extremely useful for large datasets.

In **R**, for convenience, we use the **palmerpenguins** package that already contains the penguis data set
```R
library(tidyverse)
library(palmerpenguins)
attach(penguins)
```

# Basic commands

by entering `df` we get the first few rows of the data set, similar to the tidyverse
```python
> df
shape: (344, 9)
┌───────┬───────────┬───────────┬────────────────┬───┬───────────────────┬─────────────┬────────┬──────┐
│ rowid ┆ species   ┆ island    ┆ bill_length_mm ┆ … ┆ flipper_length_mm ┆ body_mass_g ┆ sex    ┆ year │
│ ---   ┆ ---       ┆ ---       ┆ ---            ┆   ┆ ---               ┆ ---         ┆ ---    ┆ ---  │
│ i64   ┆ str       ┆ str       ┆ f64            ┆   ┆ i64               ┆ i64         ┆ str    ┆ i64  │
╞═══════╪═══════════╪═══════════╪════════════════╪═══╪═══════════════════╪═════════════╪════════╪══════╡
│ 1     ┆ Adelie    ┆ Torgersen ┆ 39.1           ┆ … ┆ 181               ┆ 3750        ┆ male   ┆ 2007 │
│ 2     ┆ Adelie    ┆ Torgersen ┆ 39.5           ┆ … ┆ 186               ┆ 3800        ┆ female ┆ 2007 │
│ 3     ┆ Adelie    ┆ Torgersen ┆ 40.3           ┆ … ┆ 195               ┆ 3250        ┆ female ┆ 2007 │
│ 4     ┆ Adelie    ┆ Torgersen ┆ null           ┆ … ┆ null              ┆ null        ┆ null   ┆ 2007 │
│ …     ┆ …         ┆ …         ┆ …              ┆ … ┆ …                 ┆ …           ┆ …      ┆ …    │
│ 341   ┆ Chinstrap ┆ Dream     ┆ 43.5           ┆ … ┆ 202               ┆ 3400        ┆ female ┆ 2009 │
│ 342   ┆ Chinstrap ┆ Dream     ┆ 49.6           ┆ … ┆ 193               ┆ 3775        ┆ male   ┆ 2009 │
│ 343   ┆ Chinstrap ┆ Dream     ┆ 50.8           ┆ … ┆ 210               ┆ 4100        ┆ male   ┆ 2009 │
│ 344   ┆ Chinstrap ┆ Dream     ┆ 50.2           ┆ … ┆ 198               ┆ 3775        ┆ female ┆ 2009 │
└───────┴───────────┴───────────┴────────────────┴───┴───────────────────┴─────────────┴────────┴──────┘
```
The following table compares the main functions of polars with the R package dplyr:
 
|  | dplyr | polars |
| :---         |     :---      |          :--- |
| first `n` rows   |  `head(df, n)`     | `df.head(n)`    |
| pick column   | `select(df, x)` | `df.select(pl.col("x"))`   |
| pick multiple columns | `select(df, x, y)` | `df.select(pl.col("x", "y"))` |
| pick rows | `filter(df, x > 4)` | `df.filter(pl.col("x") > 4 )` |
| sort column | `arrange(df, x)` | `df.sort("x")` |

You see, these commands are basically the same between dplyr and polars.

**For example**, we want to get the `bill_length_mm` of all penguins with `body_mass_g` below 3800:

```python
> df.filter(pl.col("body_mass_g") < 3800).select(pl.col("bill_length_mm"))
shape: (129, 1)
┌────────────────┐
│ bill_length_mm │
│ ---            │
│ f64            │
╞════════════════╡
│ 39.1           │
│ 40.3           │
│ 36.7           │
│ 39.3           │
│ …              │
│ 45.7           │
│ 43.5           │
│ 49.6           │
│ 50.2           │
└────────────────┘
```

# Advanced `filter` and `select`

Like in dplyr, polars filter and select have many more capabilities:

|  | dplyr | polars |
| :---         |     :---      |          :--- |
| select all columns except x   |  `select(df, -x)`  | `df.select(pl.exclude("x"))`  |
| select all columns that start with "str" | `select(df, starts_with("str"))` | `df.select(pl.col("^bill.*$"))` or `df.select(cs.starts_with("str"))`[1] |
| select numeric columns | `select(df, where(is.numeric))` | `df.select(cs.float(), cs.integer())`[1] |
| filter range of values | `filter(df, between(x, lo, hi))` | `df.filter(pl.col("x").is_between(lo, hi))` |

[1] requires `import polars.selectors as cs`
Please note that you can also [cast()](https://docs.pola.rs/user-guide/expressions/casting/#numerics) columns from one type to another (e.g. Float to Int).

**For example**, return all columns starting with "bill" for the penguin species "Gentoo":

```python
> df.filter(pl.col("species") == "Gentoo").select(pl.col("^bill.*$"))
shape: (124, 2)
┌────────────────┬───────────────┐
│ bill_length_mm ┆ bill_depth_mm │
│ ---            ┆ ---           │
│ f64            ┆ f64           │
╞════════════════╪═══════════════╡
│ 46.1           ┆ 13.2          │
│ 50.0           ┆ 16.3          │
│ 48.7           ┆ 14.1          │
│ 50.0           ┆ 15.2          │
│ …              ┆ …             │
│ 46.8           ┆ 14.3          │
│ 50.4           ┆ 15.7          │
│ 45.2           ┆ 14.8          │
│ 49.9           ┆ 16.1          │
└────────────────┴───────────────┘
```

# Creating and renaming new columns

One of the most used commands in my dplyr workflow is the `mutate` function for creating columns.
The polars equivalent is called `with_column` and works similarly

|  | dplyr | polars |
| :---         |     :---      |          :--- |
| create new column |  `mutate(df, x_mean = mean(x)`  | `df.with_columns(pl.col("x").mean().alias("x_mean"))`  |
| rename column | `rename(df, new_x = x)` | `df.rename({"x": "new_x"})` |

**For example**, let's create a new variable with the bill/flipper ratio called `bill_flipper_ratio`:

```python
> df.with_columns((pl.col("bill_length_mm") / pl.col("flipper_length_mm")).alias("bill_flipper_ratio"))
shape: (344, 10)
┌───────┬───────────┬───────────┬────────────────┬───┬─────────────┬────────┬──────┬────────────────────┐
│ rowid ┆ species   ┆ island    ┆ bill_length_mm ┆ … ┆ body_mass_g ┆ sex    ┆ year ┆ bill_flipper_ratio │
│ ---   ┆ ---       ┆ ---       ┆ ---            ┆   ┆ ---         ┆ ---    ┆ ---  ┆ ---                │
│ i64   ┆ str       ┆ str       ┆ f64            ┆   ┆ i64         ┆ str    ┆ i64  ┆ f64                │
╞═══════╪═══════════╪═══════════╪════════════════╪═══╪═════════════╪════════╪══════╪════════════════════╡
│ 1     ┆ Adelie    ┆ Torgersen ┆ 39.1           ┆ … ┆ 3750        ┆ male   ┆ 2007 ┆ 0.216022           │
│ 2     ┆ Adelie    ┆ Torgersen ┆ 39.5           ┆ … ┆ 3800        ┆ female ┆ 2007 ┆ 0.212366           │
│ 3     ┆ Adelie    ┆ Torgersen ┆ 40.3           ┆ … ┆ 3250        ┆ female ┆ 2007 ┆ 0.206667           │
│ 4     ┆ Adelie    ┆ Torgersen ┆ null           ┆ … ┆ null        ┆ null   ┆ 2007 ┆ null               │
│ …     ┆ …         ┆ …         ┆ …              ┆ … ┆ …           ┆ …      ┆ …    ┆ …                  │
│ 341   ┆ Chinstrap ┆ Dream     ┆ 43.5           ┆ … ┆ 3400        ┆ female ┆ 2009 ┆ 0.215347           │
│ 342   ┆ Chinstrap ┆ Dream     ┆ 49.6           ┆ … ┆ 3775        ┆ male   ┆ 2009 ┆ 0.256995           │
│ 343   ┆ Chinstrap ┆ Dream     ┆ 50.8           ┆ … ┆ 4100        ┆ male   ┆ 2009 ┆ 0.241905           │
│ 344   ┆ Chinstrap ┆ Dream     ┆ 50.2           ┆ … ┆ 3775        ┆ female ┆ 2009 ┆ 0.253535           │
└───────┴───────────┴───────────┴────────────────┴───┴─────────────┴────────┴──────┴────────────────────┘
```

# Grouping an aggregating

Aggregating and grouping data are essential skills for data analysis, as they allow you to summarize, transform, and manipulate data in meaningful ways.














