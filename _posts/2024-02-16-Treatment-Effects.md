---
layout: post
title: Treatment Effects
categories: [r,python]
---

## Potential Outcomes Notation

To generalize our understanding, let's introduce notation for the potential outcomes framework:

* \\(Z\\) denotes the treatment/exposure condition, where \\(Z = 1\\) represents treatment and \\(Z = 0\\) represents control.
* \\(Y\\) represents the observed outcome variable.

In this framework, we explore how observed \\(Y\\) values would vary with different treatments:

* For binary \\(Z\\), we denote potential outcomes as \\(Y_1\\) (under treatment, \\(Z = 1\\)) and \\(Y_0\\) (under control, \\(Z = 0\\)).

In reality, individuals are assigned to only one treatment group at a time, so we can't know both potential outcomes for an individual simultaneously. We'll address this later; for now, assume we know both \\(Y_0\\) and \\(Y_1\\) values.

| Z | Y¹ | Y° | Y¹ - Y° |
|---|----|----|---------|
| 1 | 18 | 23 | -5      |
| 0 | 17 | 22 | -5      |
| 1 | 15 | 23 | -8      |
| 1 | 16 | 24 | -8      |
| 0 | 17 | 23 | -6      |
| 1 | 15 | 21 | -6      |
| 0 | 9  | 14 | -5      |
| 1 | 8  | 15 | -7      |
| 1 | 10 | 14 | -4      |
| 0 | 8  | 13 | -5      |
| 0 | 8  | 14 | -6      |
| 1 | 10 | 15 | -5      |
| Average | 12.6 | 18.4 | -5.8 |
