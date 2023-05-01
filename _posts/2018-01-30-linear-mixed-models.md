---
layout: post
title: Linear Mixed Effects Models
categories: [r, bayes]
---
Have you ever had to analyze data with a complex structure, such as nested or hierarchical data, and found yourself struggling to find a suitable statistical model? If so, you might want to consider using linear mixed effects models.

Linear mixed effects models are a statistical technique that allows researchers to analyze data with both fixed and random effects. They're commonly used in fields like biology, psychology, and social sciences to analyze data that has a hierarchical or nested structure.

But what exactly are fixed and random effects? In simple terms, fixed effects are factors that are constant across all groups in the data, while random effects are factors that vary across groups. For example, if you were studying the effects of a new medication on patients in different hospitals, the hospital would be a random effect since patients within the same hospital may have similar outcomes. In contrast, the medication would be a fixed effect since it is constant across all hospitals.

Linear mixed effects models allow researchers to model both fixed and random effects, which is essential when analyzing data with a complex structure. By accounting for the correlation between data points within each group or cluster, these models provide a flexible framework for modeling complex data structures.

Furthermore, linear mixed effects models can be used in both experimental and observational studies. They allow researchers to examine how various factors influence the outcome of interest while controlling for other factors. This way, they can extract insights from data that might be difficult to analyze with other statistical models.

Overall, linear mixed effects models are a powerful tool for researchers who need to analyze data with a complex structure. They provide a way to extract meaningful insights from data while accounting for the correlation between data points within each group or cluster.

While linear mixed effects models are a powerful tool for analyzing complex data structures, they have certain limitations. For instance, they assume that the errors are normally distributed and have constant variance, which may not always be the case in practice. Additionally, estimating the parameters of linear mixed effects models can be challenging, particularly when the sample size is small or the number of predictors is large.

Bayesian linear mixed effects models offer an alternative approach to these challenges. Unlike traditional linear mixed effects models, which rely on frequentist statistics, Bayesian models use Bayesian statistics to estimate the model parameters. This means that Bayesian models do not require the normality assumption, and they can incorporate prior knowledge about the model parameters into the analysis.

One of the key advantages of Bayesian linear mixed effects models is their ability to handle missing data. Since they estimate the posterior distribution of the parameters, Bayesian models can handle missing data by imputing the missing values through a process called data imputation. This can lead to more accurate estimates and more robust conclusions.

Another advantage of Bayesian linear mixed effects models is their ability to incorporate complex hierarchical structures. Bayesian models allow researchers to specify complex prior distributions over the parameters of the model, which can capture complex relationships between the fixed and random effects.

Let's fit both a regular (frequentist) linear mixed model and a Bayesian one. I will use `lme4` for the frequentist, and `brms` for the Bayesian model.
As the data, I use the `sleepstudy` data set in the `lme4` package.
The dataset consists of individuals who participated in a sleep derivation study, where they had their normal amount of sleep on Day 0, and then were restricted to only 3 hours of sleep per night. The response variable, `Reaction`, measures the average reaction times in milliseconds (ms) on a series of tests given to each subject every day throughout the study.

Interestingly, the results of the study showed that each subject's reaction time increased approximately linearly with the number of sleep-deprived days. However, what was even more interesting was that subjects varied in the slopes and intercepts of these relationships. This suggests that a model with random slopes and intercepts would be more appropriate for analyzing this dataset.

```r
library(lme4)
library(brms)

data(sleepstudy)
#'data.frame':	180 obs. of  3 variables:
# $ Reaction: num  250 259 251 321 357 ...
# $ Days    : num  0 1 2 3 4 5 6 7 8 9 ...
# $ Subject : Factor w/ 18 levels "308","309","310",..: 1 1 1 1 1 1 1 1 1 1 ...
```

Now we fit the frequentist model using `lme4`:

```r
fm <- lmer(Reaction ~ Days + (Days | Subject), sleepstudy)
summary(fm)

# Linear mixed model fit by REML ['lmerMod']
# Formula: Reaction ~ Days + (Days | Subject)
# Data: sleepstudy
# 
# REML criterion at convergence: 1743.6
# 
# Scaled residuals: 
#   Min      1Q  Median      3Q     Max 
# -3.9536 -0.4634  0.0231  0.4634  5.1793 
# 
# Random effects:
#   Groups   Name        Variance Std.Dev. Corr
# Subject  (Intercept) 612.10   24.741       
# Days         35.07    5.922   0.07
# Residual             654.94   25.592       
# Number of obs: 180, groups:  Subject, 18
# 
# Fixed effects:
#   Estimate Std. Error t value
# (Intercept)  251.405      6.825  36.838
# Days          10.467      1.546   6.771
# 
# Correlation of Fixed Effects:
#   (Intr)
# Days -0.138
```

The same model in `brms`:

```r
bm <- brm(Reaction ~ Days + (Days | Subject), sleepstudy)
summary(bm)

# Family: gaussian 
# Links: mu = identity; sigma = identity 
# Formula: Reaction ~ Days + (Days | Subject) 
# Data: sleepstudy (Number of observations: 180) 
# Draws: 4 chains, each with iter = 2000; warmup = 1000; thin = 1;
# total post-warmup draws = 4000
# 
# Group-Level Effects: 
#   ~Subject (Number of levels: 18) 
# Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
# sd(Intercept)          26.94      6.83    15.62    41.81 1.00     1383     2224
# sd(Days)                6.57      1.50     4.15    10.06 1.00     1573     2277
# cor(Intercept,Days)     0.09      0.30    -0.47     0.69 1.00      959     1647
# 
# Population-Level Effects: 
#   Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
# Intercept   251.32      7.33   236.36   265.43 1.00     1657     2049
# Days         10.45      1.69     7.00    13.80 1.00     1425     1770
# 
# Family Specific Parameters: 
#   Estimate Est.Error l-95% CI u-95% CI Rhat Bulk_ESS Tail_ESS
# sigma    25.93      1.58    23.16    29.27 1.00     3153     2881
# 
# Draws were sampled using sampling(NUTS). For each parameter, Bulk_ESS
# and Tail_ESS are effective sample size measures, and Rhat is the potential
# scale reduction factor on split chains (at convergence, Rhat = 1).
# 
```

Note that the fixed effects are basically identical in both models, while the random effects are close.





