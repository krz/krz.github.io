---
layout: post
title: Comparing the seaborn objects interface with ggplot2
categories: [r,python]
---

The [Seaborn objects interface](https://seaborn.pydata.org/tutorial/objects_interface.html), introduced in version 0.12.0, is a new system based on the Grammar of Graphics, similar to R's ggplot2. 
It offers a more consistent and flexible API, comprising a collection of composable classes for transforming and plotting data. 
This interface allows for end-to-end plot specification and customization without dropping down to the matplotlib level, making it suitable for more complex plots with multiple layers and mark types. 
While the interface is still experimental and incomplete, it provides a modular and Pythonic API that is informed by ggplot2's design philosophy.
In this post, I replicate the plots from the [ggplot2 book](https://ggplot2-book.org/getting-started) using the seaborn objects interface.
This is a work in progress.

# Getting started

```python
# load libraries and import mpg data
import seaborn.objects as so
import polars as pl

mpg = pl.read_csv("https://raw.githubusercontent.com/tidyverse/ggplot2/main/data-raw/mpg.csv")
```

```python
(
    so.Plot(mpg, x="displ", y="hwy")
    .add(so.Dot())
)
```
