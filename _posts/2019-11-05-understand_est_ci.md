---
layout: post
title: How do we understand estimate and confidence interval?
subtitle: Chaoran Hu
date: 2019-11-05
author: Chaoran Hu
header-img: img/post-bg-desk.jpg
catalog: true
tags:
  - statistics
---

As we have mentioned many times in class, both estimate and confidence
interval are random variables. The explanation can be quite simple. Both
estimate and confidence interval are the functions of random sample,
that are actually set of random variables identically and independently
distributed as population distribution. Mathematically, that is
*X*<sub>1</sub>, …, *X*<sub>*n*</sub> ∼ *i*.*i*.*d*.*F*(*θ*) and
estimate of *θ*, $\\hat{\\theta}$ is a function of
*X*<sub>1</sub>, …, *X*<sub>*n*</sub>, that is
$\\hat{\\theta}:(X\_1, \\dots, X\_n) \\rightarrow R$. And, if you check
the confidence interval we have shown in class, you will find that it is
also the function of random sample. So, both of them are random
variables. This article will use simulation to help you to understand
the uncertainty behind estimate and confidence interval.

Understanding they are random variables
---------------------------------------

We are going to focus on the population which follows normal
distribution with mean, *μ* = 5 and variance *σ*<sup>2</sup> = 2. Now,
let us pretend we do not know the true value of mean and we are going to
estimate it with sample. So, first step is generating a sample from this
distribution with sample size 500.

    set.seed(123)
    sample1 <- rnorm(500, 5, sqrt(2)) ## this code will generate 500 random number from N(5, 2)

Before we move to next step, let's plot the estimated density curve of
our sample1. The black line is the density curve that is estimated from
sample1 (so called empirical) and the red line is the density curve
calculated from the probability density function (so called
theoratical).

    plot.new()
    plot(density(sample1), main = "Estimated density curve of sample1",
         sub = "red line is the theoratical density curve of N(5, 2)")
    lines(seq(0, 10, length.out = 100), dnorm(seq(0, 10, length.out = 100), 5, sqrt(2)), col = "red")

![](/understand_est_ci_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Recall that, the popular estimator of population mean, *μ* is sample
mean, $\\bar{X} = 1/n\\sum\_{i=1}^n X\_i$. Now, let us calulate the
sample mean based on sample1 and use this sample mean as the estimator1
of population mean. As you can see, estimator1 of population mean,
$\\hat{\\mu}\_1$, is 5.0489 which is quite close to the true value 5.
However, it is not equal to the true value!

    mean(sample1)

    ## [1] 5.048918

Now, let us calculate the 95% confidence interval for population mean
*μ* based on sample1. Because the random sample follows normal
distribution and the variance is known as 2, the formula for confidence
interval is
$$(\\bar{X} - z\_{\\frac{\\alpha}{2}} \\frac{\\sigma}{\\sqrt{n}}, \\bar{X} + z\_{\\frac{\\alpha}{2}} \\frac{\\sigma}{\\sqrt{n}}),$$
 where $\\bar{X}=5.0489$, *α* = 0.05, z-critical value
$z\_{\\frac{\\alpha}{2}}=1.96$, $\\sigma = \\sqrt{2}$ and sample size
*n* = 500. So, it can be evaluated by following code. Then, we will have
95% confidence interval based on sample1 as (4.925, 5.173). Let us name
this interval as CI1. I want to mention that this interval, CI1, does
cover the true value of population mean, 5.

    CI95norm <- function(sample) {
      lb <- mean(sample)-1.96*(sqrt(2/500))
      ub <- mean(sample)+1.96*(sqrt(2/500))
      c(lb, ub)
    }
    CI95norm(sample1)

    ## [1] 4.924957 5.172880

Now, let us summary what we have done until now as following list: 1.
Generate random sample with sample size 500 from N(5, 2). (that is
sample1) 2. Calculate sample mean as estimator of population mean based
on sample gotten in step 1. (that is estimator1, $\\hat{\\mu}\_1$) 3.
Calcualte 95 confidence interval based on sample gotten in step 1. (that
is CI1) I want to mention that step 1 can be considered as we process an
experiment one time and collect sample from this experiment. Now, let us
replicate this procedure. Then, we can calculate the sample mean from
sample2 and it is our estimator2 of population mean,
$\\hat{\\mu}\_2=4.982$. This number is very close to the true value 5
and is not equal to the $\\hat{\\mu}\_1$, because they are calculated
from different samples. Furthermore, the CI2 from sample2 is
(4.858, 5.106), which also covers the true value but not equal to CI1.
The reason is random sample has uncertainty, that induces both estimator
and confidence interval have uncertainty. They are random variables!

    set.seed(432)
    sample2 <- rnorm(500, 5, sqrt(2)) ## this code will generate 500 random number from N(5, 2)
    mean(sample2)

    ## [1] 4.98243

    CI95norm(sample2)

    ## [1] 4.858468 5.106391

Understanding the meaning of unbiased estimator
-----------------------------------------------

The definition of unbiased estimator is $E(\\hat{\\theta}) = \\theta$.
In our example, $E(\\hat{X}) = \\mu$. So, $\\hat{X}$ is the unbiased
estimator of *μ*. Even if each estimator, $\\hat{X}$ can not target the
true value 5, $\\hat{X}$ can target the true value 5 **on average**.

Let us replicate the above procedure 1000 times. That will gives us 1000
different samples from N(5, 2). Each of them will have 500 observations.
Then, 1000 estimators of population mean and 1000 95% CI can be
evaluated from these samples. Let us do it!

    set.seed(837)
    result <- matrix(NA, ncol = 4, nrow = 1000)
    for (i in 1:1000) {
      sample <- rnorm(500, 5, sqrt(2))
      result[i, 1] <- i
      result[i, 2] <- mean(sample)
      result[i, 3:4] <- CI95norm(sample)
    }
    colnames(result) <- c("ID", "sample_mean", "95CI_lower", "95CI_upper")
    head(result)

    ##      ID sample_mean 95CI_lower 95CI_upper
    ## [1,]  1    4.930987   4.807026   5.054949
    ## [2,]  2    5.128847   5.004885   5.252808
    ## [3,]  3    5.034153   4.910192   5.158115
    ## [4,]  4    4.929134   4.805173   5.053095
    ## [5,]  5    5.054067   4.930106   5.178028
    ## [6,]  6    5.006233   4.882272   5.130194

The result is shown as above table. (Only first 6 rows are shown.) Each
row records the result of one experiment. `sample_mean` column records
the estimator, $\\hat{\\mu}\_j$, *j* = 1, …, 1000. `95CI_lower` and
`95CI_upper` record the 95% confidence interval's lower bound and upper
bound. Now, the average of first *m* estimators is
$1/m \\sum\_{j=1}^m \\hat{\\mu}\_j$. $\\hat{\\mu}$ is unbiased basically
tells us as *m* → ∞, this average will process to 5. Let us plot this
average vs *m* and you will find it is the case.

    aveEst <- numeric(1000)
    for (i in 1:1000) {
      aveEst[i] <- mean(result[1:i, 2])
    }
    plot(1:1000, aveEst, type = "l", xlab = "m", ylab = "average of first m estimators")
    abline(h = 5, col = "red")

![](/understand_est_ci_files/figure-markdown_strict/unnamed-chunk-7-1.png)

Understanding the confidence level in confidence interval
---------------------------------------------------------

We have calculated 1000 95% confidence intervals based on 1000 samples
we simulated. For a 95% CI, the confidence level is 95% and significant
level is (1 − 95%) = 5%. The 95% confidence level here means the
probability of 'confidence interval covers the true population mean 5'
is 95% theoratically. Practically, it means within 1000 confidence
intervals we got from simulation, about 95% of them will cover the true
value 5. Let us check whether it is the case. As shown in the following
code, the proportion of intervals cover the true value 5 is 94.8%, that
is very close to 95%.

    ## show the proportion of intervals covering 5
    sum(result[, 3] < 5 & result[, 4] > 5) / 1000

    ## [1] 0.948
