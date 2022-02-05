---
title: "bmemEDIT, bmemTOOLS and my honours project"
categories:
  - Blog
  - Project
tags:
  - Honours
  - R
  - multiple imputation
  - regression
---

## Introduction
This blog post provides some background behind the two R packages I developed
during my honours year - bmemEDIT and bmemTOOLS. While far from the polished packages
one often sees on github, I think it'd still be worthwhile documenting why and how
I dipped my toes into package development. One of the key takeaways of my honours year, and
as my supervisor would often remind me, is that nothing can be perfect. Though there are sections of
code uncommented and features left unimplemented, I am choosing to leave them that way because
the code should serve the end user (me) and their goal (to complete my thesis, now completed),
and not the other way around. That being said,
I will try my best to explain the packages' core components, should
the reader want to use their functions or develop a similar package.

Before diving in, I must acknowledge that the packages (as their names suggest)
are built around Zhiyong Zhang's [bmem](https://cran.r-project.org/web/packages/bmem/index.html).
My packages also draw functions from several other packages, which I reference throughout the post,
and will include in a list at the bottom of the post.

## To impute or not to impute?
One of the first obstacles that a researcher faces when analysing a lab's data for the first time is how to deal with missing data. Missing data comes in several flavours, and can be missing

* truly without any pattern (i.e. **missing completely at random (MCAR)**)
* according to an _observable_ pattern (i.e. **missing at random (MAR)** e.g. all women are missing age data, while all men have age data) or
* according to an _unobservable_ pattern (i.e. **missing not at random (MNAR)** e.g. men and women are missing age data because those who are older tend not to fill in age data)

Depending on the type of missing data, one would choose an appropriate technique to deal with the missing data. [Little's Missing Completely at Random test](https://stats.stackexchange.com/questions/105233/interpretation-of-littles-mcar-test/105238) is a quick way to determine the missingness pattern. In a nutshell, I used Little's test to show that there was insufficient evidence to reject the null hypothesis that my data was MCAR (in other words, there was no _apparent_ pattern to missingness in my data). Note that this result does not rule out the possibility of an _unobserved_ pattern in the missing data, though I assume that my data is MCAR for the remainder of the post.

Assuming the missingness in my dataset to be MCAR, I was then faced with a decision: to conduct list-wise deletion (i.e. to delete every observation with missing data in any of its variables) or to conduct multiple imputation. List-wise deletion is a common way to deal with missing data, primarily due to its ease of implementation (simply delete any observation with missing values). However, this simplicity also comes with severe pitfalls, such as producing biased estimates and (more importantly for small datasets) reducing the power to detect effects, by virtue of reducing the sample size. Multiple imputation (MI) is a relatively modern technique which addresses both of these shortcomings. In lay terms, it achieves this by generating multiple datasets, each of which has its missing values (in the original dataset) replaced with an estimation. However, multiple datasets means that parameters of interest (regression coefficients, standard errors, confidence intervals) would have to be calculated separately for each dataset, and then combined (a process known as "pooling"). While statistical packages in R and SPSS offer pooling for some parameter estimates, pooling of other estimates (such as $$R^2$$) are not natively supported.

## The fruits of multiple imputation
