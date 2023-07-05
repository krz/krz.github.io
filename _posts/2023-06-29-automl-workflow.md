---
layout: post
title: AutoML full workflow with tidymodels
categories: [r, machine learning]
---

AutoML is the process of automating the tasks of applying machine learning to real-world problems. I often use this approach as it requires very little investment in tuning models and the results are usually quite competitive.
The following is a notebook for a recent [kaggle competition](https://www.kaggle.com/code/christph/automl-full-workflow).


```R
suppressMessages(library(tidyverse))
suppressMessages(library(tidymodels))
suppressMessages(library(h2o))
suppressMessages(library(agua))
tidymodels_prefer()
```

## Overview
In this notebook I will use automated machine learning using the `h2o` package wrapped in a `tidymodels` workflow for preprocessing.

__AutoML__ is the process of automating the tasks of applying machine learning to real-world problems.
H2O AutoML trains a random grid of algorithms such as GBMs, DNNs, GLMs, etc. using a carefully chosen hyper-parameter space. It also trains two stacked ensembles using all the models from the grid.

### Import data
First, we read in the data and drop the `Id` variable.


```R
dat <- read_csv("/kaggle/input/icr-identify-age-related-conditions/train.csv") %>%
    mutate(
        Class = factor(Class),
        EJ = factor(EJ)
    ) %>%
    select(-Id)
```

### Initialize H2O
Next, we initialize `h2o` with 25G of RAM. By default we use all available cores.


```R
h2o.init(max_mem_size = "25G")
```

### Define the model
we try 30 different models (set higher to maybe get better results).
Note that we also set `balance_classes` to TRUE because the `Class` variable is highly unbalanced.


```R
auto_mod <- auto_ml() %>%
  set_engine("h2o",
    max_models = 30,
    stopping_metric = "logloss",
    sort_metric = "logloss",
    balance_classes = T,
    seed = 42
  ) %>%
  set_mode("classification") %>%
  translate()
```

### Feature Engineering
Now we define the recipe. We impute missing values and delete variables with zero variance.
There are other steps that can be included to improves results, for example:
* step_normalize(all_numeric_predictors) to normalize variables
* step_pca(all_numeric_variables, keep_original_cols=T) to include principal components
* step_poly(all_numeric_variables) to generate poly splines of variables

See https://recipes.tidymodels.org/reference/index.html for a list of all steps.

In this example we only impute missing variables with a bagged tree and delete highly correlated variables, variables that are linear combinations of each other and variables with zero variance


```R
auto_recipe <-
  recipe(Class ~ ., data = dat) %>%
  step_impute_bag(all_predictors()) %>%
  step_corr(all_numeric_predictors()) %>%
  step_lincomb(all_numeric_predictors()) %>%
  step_zv(all_predictors()) 
auto_recipe
```

### Workflow
This is the full workflow with pre-processing and training. `h2o`
internally evaluates the models in 10-fold cross-validation.


```R
auto_workflow <-
  workflow() %>%
  add_model(auto_mod) %>%
  add_recipe(auto_recipe)

auto_res <-
  auto_workflow %>%
  fit(data = dat)
```

### Results
The best model is a stacked ensemble of all 30 models with a logloss of __0.16__. Nice.


```R
print(auto_res)
```

### Predict
Finally we predict on the test data and create a submission file. The preprocessing workflow will be automatically applied to the test data.


```R
test <- read_csv("/kaggle/input/icr-identify-age-related-conditions/test.csv") %>%
    mutate(
        EJ = factor(EJ)
    )
```


```R
preds <- predict(auto_res, test, type="prob")
preds
```


```R
submission <- tibble(Id = test$Id,
                 class_0 = preds$.pred_p0,
                 class_1 = preds$.pred_p1)
submission
```


```R
write_csv(submission, file="submission.csv")
```
