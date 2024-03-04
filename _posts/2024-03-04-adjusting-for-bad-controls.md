---
layout: post
title: Adjusting for "bad controls"
categories: [r, python]
---

In every statistical analysis, the selection of covariates in regression models is akin to navigating a labyrinth. 
One common heuristic advises controlling for as many variables as possible, under the presumption that it yields more conservative tests of hypotheses. 
However, this approach is not without pitfalls. 
Adding wrong or extraneous variables can distort results, introducing spurious effects and even reversing the signs of actual causal relationships.
In the following, I will provide an example of how this can happen.

