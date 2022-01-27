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
One of the earliest problems I faced during my honours year, as many researchers do when they
see a dataset for the first time, is missing data. Without going into too much detail,
the missing data points can be missing

* without any pattern (i.e. **missing completely at random (MCAR)**)
* according to an _observable_ pattern (i.e. **missing at random (MAR)** e.g. all women are missing age data, while all men have age data) or
* according to an _unobservable_ pattern (i.e. **missing not at random (MNAR)** e.g. men and women are missing age data because those who are older tend not to fill in age data)


Using these classifications, I then ran [Little's Missing Completely at Random test](https://stats.stackexchange.com/questions/105233/interpretation-of-littles-mcar-test/105238).
(_Sidenote_: I originally thought that this test would be a litmus test for which of the
three missing data mechanisms my data obeyed - but alas, it was not so simple.
Unlike the name suggests, the MCAR test only determines whether or not there is an _observable_ pattern to the missing data.
If there is no pattern, then it could be there is truly no pattern in the missing data (MCAR),
OR that there IS a pattern in the missing data that we cannot observe (MNAR))

I found no evidence to reject the assumption that the data was missing completely at random (though
see the sidenote above). This meant choosing between two main paths to deal with my missings: to
delete every observation with missing data in any of its variables (~ 30% of the 140 observations), or
to conduct multiple imputation, a relatively modern technique which (most of the time) generates
multiple "complete" datasets from which parameter inferences are made. Both techniques had their downsides -
deleting all observations with ANY missing data felt harsh, and would have drastically
reduced the power to detect effects in subsequent analyses. Multiple imputation, however, meant multiple datasets. Any
parameter estimates (regression coefficients, standard errors, confidence intervals) would have to be generated
separately for each dataset, and then combined (a process known as "pooling"). As I would soon learn,
pooling was not yet an established practice for some of the analyses I had planned on doing, and would require a further investment of my time.

## The fruits of multiple imputation
