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

That is, until now. Meet **polars**, a new Python library for data manipulation that aims to fill the gap between dplyr and pandas. 
Polars is a fast and expressive library that offers a syntax similar to the tidyverse, while being extremely fast and scalable. 
It leverages Apache Arrow as its underlying data structure and is written in Rust, which enables efficient memory management and interoperability with other tools. 
It also supports lazy evaluation, parallel processing, and query optimization, which make it suitable for working with large and complex data sets.
[According to some benchmarks](https://duckdblabs.github.io/db-benchmark/), polars is one of the fastest tools for handling and manipulating data.

In this blog post, we will compare the dplyr and polars libraries and see how they can help us perform common data manipulation tasks. 
We will use a simple data set of the **palmerpenguins** package, the _new Iris data set_. We will see how to load, filter, group, join, and reshape the data using both libraries, and compare their syntax, performance, and output. 
We will also discuss some of the advantages and disadvantages of each library, and provide some tips and resources for further learning.
