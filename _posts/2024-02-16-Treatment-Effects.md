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

* For binary \\(Z\\), we denote potential outcomes as \\(Y^1\\) (under treatment, \\(Z = 1\\)) and \\(Y^0\\) (under control, \\(Z = 0\\)).

In reality, individuals are assigned to only one treatment group at a time, so we can't know both potential outcomes for an individual simultaneously. We'll address this later; for now, assume we know both \\(Y^0\\) and \\(Y^1\\) values.

If we had access to both potential outcomes for each individual, we could derive various statistics to assess treatment effects:

* __Individual Treatment Effect (ITE)__: Calculated as \\(Y^1 - Y^0\\), directly comparing potential outcomes.
* __Average Treatment Effect (ATE)__: Obtained by averaging all individual treatment effects, representing the difference between the averages of \\(Y^1\\) and \\(Y^0\\).

However, a crucial challenge arises: how do we compute ITE or the true ATE when we can't observe the counterfactual outcome? This highlights the core issue of causal inference, which is essentially grappling with missing data. Since we only witness the actual outcome, the counterfactual remains elusive.

The following table displays data for 12 hospital patients who either interacted with a stress-relief toy \\(Z = 1\\)) or did not interact with a stress-relief toy \\(Z = 0\\)). 
The theoretical data contains both potential outcomes, allowing us to compute the true ATE by subtracting the potential outcome averages. The true ATE is -5.8, indicating that interaction with stress-relief toys results in an average decrease in cortisol levels of 5.8 units.

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

Real data includes only one potential outcome per individual. Notably, \\(Y^1\\)) values are absent for individuals in the control group \\(Z = 0\\)), while \\(Y^0\\)) values are absent for those in the treatment group \\(Z = 1\\)). Consequently, in practical scenarios, we consistently lack half of the necessary data to compute the true ATE.

| Z | Y¹ | Y° | Y¹ - Y° |
|---|----|----|---------|
| 1 | 18 | ?  | ?       |
| 0 | ?  | 22 | ?       |
| 0 | ?  | 23 | ?       |
| 1 | 16 | ?  | ?       |
| 0 | ?  | 23 | ?       |
| 1 | 15 | ?  | ?       |
| 0 | ?  | 14 | ?       |
| 1 | 8  | ?  | ?       |
| 1 | 10 | ?  | ?       |
| 0 | ?  | 13 | ?       |
| 0 | ?  | 14 | ?       |
| 1 | 10 | ?  | ?       |
| Average | ? | ?  | ?  |
