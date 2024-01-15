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
first plot

**dplyr**:
```r
ggplot(mpg, aes(displ, hwy)) + 
  geom_point()
```

**seaborn**:
```python
(
    so.Plot(mpg, x="displ", y="hwy")
    .add(so.Dot())
)
```
![](/images/output1.png)

map the class variable to colour

**dplyr**:
```r
ggplot(mpg, aes(displ, hwy, colour = class)) + 
  geom_point()
```

**seaborn**
```python
(
    so.Plot(mpg, x="displ", y="hwy", color="class")
    .add(so.Dot())
)
```
![](/images/output2.png)

map the class variable to pointsize

**dplyr**:
```r
ggplot(mpg, aes(displ, hwy, size = class)) + 
  geom_point()
```

**seaborn**:
```python
(
    so.Plot(mpg, x="displ", y="hwy", pointsize="class")
    .add(so.Dot())
)
```
![](/images/output3.png)

map the class variable to alpha

**dplyr**:
```r
ggplot(mpg, aes(displ, hwy, alpha = class)) + 
  geom_point()
```

**seaborn**:
```python
(
    so.Plot(mpg, x="displ", y="hwy", alpha="class")
    .add(so.Dot())
)
```
![](/images/output4.png)

map the class variable to shape

**dplyr**:
```r
ggplot(mpg, aes(displ, hwy, shape = class)) + 
  geom_point()
```

**seaborn**:
```python
(
    so.Plot(mpg, x="displ", y="hwy", marker="class")
    .add(so.Dot())
)
```
![](/images/output5.png)


