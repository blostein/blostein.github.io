---
layout: post
title: "Code Tips and Tricks - Running Log"
date: 2025-10-21
---

# Code tips and hacks
2025-10-21

``` r
library(pacman)
```

# R

## Plotting

### [shadowtext](https://cran.r-project.org/web/packages/shadowtext/index.html) Plot with outline text in different colors

See here:
https://cran.r-project.org/web/packages/shadowtext/vignettes/shadowtext.html
OR

ggfx: https://ggfx.data-imaginist.com/

``` r
p_load(shadowtext)
```

or use

### Make a blank plot object

``` r
p_load(ggplot2, patchwork)

blank_plot <- wrap_elements(
    ggplot() + geom_blank() +
    theme(
        panel.border = element_blank(),
        plot.background = element_blank()
    )
)
```

or

``` r
blank_plot <- wrap_elements(ggplot() + theme_void())
```

## Descriptive

### Make quick and beautiful tables with gtsummary

``` r
p_load(gtsummary)
```

### Summarize a dataset quickly with skimr

``` r
p_load(skimr)
```

## Modeling

### Easy summary of statistical test

https://easystats.github.io/report/

``` r
p_load(report)
```

### Modeling with constraints:

In case anyone needs to do this, I figured out how to fit a linear model
with constraints on the coefficients. The nls function for non-linear
least squares fitting supports setting bounds on the coefficients. For
example, let’s say we have a simple linear model y as a function of x:

    library(parameters)

    x <- rnorm(1000)
    y <- x + rnorm(1000)
    fit <- lm(y ~ x)
    parameters(fit)

First, we rewrite this model using nls. The formula is a bit different
because we need to name the coefficients explicitly, and supply starting
values for the iterative optimization. It’s called non-linear least
squares because it allows non-linear functions, but you’re still allowed
to specify a linear relationship. (In general, it’s important to choose
an appropriate starting point, but in the case of a linear model I’m
pretty sure the optimization is convex, so any starting value will
eventually converge to the global optimum.)

    nlfit <- nls(
      y ~ a * x + b,
      start = c(a = 0, b = 0)
    )
    parameters(nlfit)

In this case, we get the same result as `lm`, because there’s no
constraints. Next, let’s say that for some reason, we want to slope (a)
to be exactly 0.9, and then we want to estimate the intercept (b)
conditional on the slope being 0.9. We specify the start point as well
as the lower and upper bounds to be 0.9 for a, and for b we specify the
bounds as +/-Inf since we want it to be unbounded. We also have to set
algorithm = “port” because this is the algorithm that supports
coefficients bounds.

    nlfit <- nls(
      y ~ a * x + b,
      start = c(a = 0.9, b = 0),
      lower = c(a = 0.9, b = -Inf),
      upper = c(a = 0.9, b = Inf),
      algorithm = "port"
    )
    parameters(nlfit)

One important note: I don’t know if you can use the p-values for any
coefficients with bounds, because I’m pretty sure the significance is
still computed while ignoring the bounds. So the p-value for a in this
model is meaningless. However, I believe the p-value for b is still
valid, but it is conditional on the bounds of a.Lastly, if you want
confidence intervals on the fitted line (e.g. for plotting in the style
of geom_smooth), you can get them with `investr::predFit`:

    library(investr)
    nlpr <- predFit(nlfit, newdata = data.frame(x = sort(x)), interval = "confidence")
    head(nlpr)

(Normally you would get confidence intervals with `predict`, but the nls
method for this function does not support confidence
intervals.) (edited) 

Slightly related to this, I also learned recently it is possible to set
monotonic constraints in XGBoost - useful for clinical models where you
expect risk to increase monotonically with risk factor
<https://xgboost.readthedocs.io/en/stable/tutorials/monotonic.html>

# Bash

### retcon a screen session

Cool minerva trick of the day: Have you ever accidentally started a
long-running command outside of a screen session? It turns out there’s a
command you can use to transfer an already running command to a new
shell. Just do `module load reptyr` and then use `reptyr PID` inside the
screen session to transfer your running command into that screen window.
PID is the process ID of the running command. You can get it using `ps`
if you know how to use that, or else just do `module load htop; htop`
and then scroll through the list of commands with the arrow keys until
you find the command you want to transfer.

# Python

# Genetics

## [Pull SRA sequences faster with xsra](https://github.com/arcInstitute/xsra)

## 
