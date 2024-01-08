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







