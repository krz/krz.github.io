---
layout: post
title: Treatment Effects
categories: [r,python]
---

## Potential Outcomes Notation

To generalize our understanding, let's introduce notation for the potential outcomes framework:

* $Z$ denotes the treatment/exposure condition, where $Z = 1$ represents treatment and $Z = 0$ represents control.
* $Y$ represents the observed outcome variable.

In this framework, we explore how observed $Y$ values would vary with different treatments:

* For binary $Z$, we denote potential outcomes as $Y_1$ (under treatment, $Z = 1$) and $Y_0$ (under control, $Z = 0$).

In reality, individuals are assigned to only one treatment group at a time, so we can't know both potential outcomes for an individual simultaneously. We'll address this later; for now, assume we know both $Y_0$ and $Y_1$ values.
