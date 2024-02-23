---
layout: post
title: More polars
categories: [r, python]
---

After reading Rasmus Bååth's blog [Why pandas feels clunky when coming from R](https://www.sumsar.net/blog/pandas-feels-clunky-when-coming-from-r/), 
I decided to practice more polars and translate his `dplyr` examples to `polars`.

Reading the data is pretty straightforward:

```python
import polars as pl
purchases = pl.read_csv("purchases.csv")
```

get the sum of `amount` and name it `total`:

```python
# dplyr:
# purchases$amount |> sum()
# actually the "pure" tidy approach would be purchases |> select(amount) |> sum 😉

purchases.select("amount").sum()
```

same by country:
```python
# dplyr:
# purchases |>
#  group_by(country) |>
#  summarize(total = sum(amount))

(
    purchases
    .group_by("country")
    .agg(pl.sum("amount").alias("total"))
)
```
deduct the `discount`:
```python
# dplyr:
# purchases |> 
#  group_by(country) |> 
#  summarize(total = sum(amount - discount))

(
    purchases
    .group_by("country")
    .agg(pl.sum("amount") - pl.sum("discount")
         .alias("total"))
)
```
Note that `polars` does not automatically sort the `country` column (dplyr does).
If you want the sorting, add `.sort("country")`

Now we remove everything 10x larger than the median:
```python
# dplyr:
# purchases |>
#  filter(amount <= median(amount) * 10) |>
#  group_by(country) |> 
#  summarize(total = sum(amount - discount))

(
    purchases
    .filter(pl.col("amount") < pl.median("amount") * 10)
    .group_by("country")
    .agg(pl.sum("amount") - pl.sum("discount")
         .alias("total"))
)
```
Finally, we should use the median within each country:
```python
# dplyr:
# purchases |>
#  group_by(country) |>                     
#  filter(amount <= median(amount) * 10) |> 
#  summarize(total = sum(amount - discount))

(
    purchases
    .filter(pl.col("amount") < (pl.median("amount") * 10).over("country"))
    .group_by("country")
    .agg(pl.sum("amount") - pl.sum("discount")
         .alias("total"))
    .sort("country")
)
```
The last one was a bit tricky. Simply switching the `group_by` and `filter` lines like in dplyr does not work in polars.






