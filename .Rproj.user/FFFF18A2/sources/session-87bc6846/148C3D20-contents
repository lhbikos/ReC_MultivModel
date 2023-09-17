# MODERATION  {-#MOD}

# Simple Moderation in OLS and MLE {#SimpMod}

 [Screencasted Lecture Link](https://spu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?pid=cc098142-3693-4366-bb55-ad1d0175414e) 





The focus of this lecture is an overview of simple moderation.  Sounds simple?  Wait, there's more!  The focus of this lecture is the transition:

* from null hypothesis significance testing (NHST) to modeling
* from *ordinary least squares* (OLS) to *maximum likelihood estimation* (MLE)

In making the transition we will work a moderation/interaction problem from Hayes' text with both *lm()* and *lavvan/sem()* functions. 

## Navigating this Lesson

There is about 1 hour and 10 minutes of lecture.  If you work through the materials with me it would be plan for an additional hour

While the majority of R objects and data you will need are created within the R script that sources the chapter, occasionally there are some that cannot be created from within the R framework. Additionally, sometimes links fail.  All original materials are provided at the [Github site](https://github.com/lhbikos/ReC_MultivModel) that hosts the book. More detailed guidelines for ways to access all these materials are provided in the OER's [introduction](#ReCintro)

### Learning Objectives

Learning objectives from this lecture include the following:

* Distinguish between NHST and model building approaches
* Name the primary characteristics that distinguish ordinary least squares from maximum likelihood approaches to regression.
* Interpret "the usual" things we find in regression:  B/beta weights, R, $R^{2}$.
* Define and interpret simple slopes and probing an interaction, this includes
  - pick-a-point and Johnson-Neyman approaches
  - interpreting interaction plots/figures
* Recognize the path specification in *lavaan*. That is, you should be able to figure out a diagram from the *lavaan* code.  In reverse, you should be able to write (or identify) the proper code in *lavaan*.

### Planning for Practice

Although I provide more complete descriptions at the end of the chapter follow these suggestions, providing an overview of them here may help you plan for what you might want to do as you work through the chapter. As is typical for this OER, the suggestions for homework are graded in complexity.  I recommend you select an option that builds on your confidence but provides a bit of stretch. I also suggest you utilize a dataset that has at least four variables that are suitable for growing into a complex moderation (additive or moderated) or moderated mediation. This will be easiest if the variables are continuous in nature. In these chapters, I do not describe how to use categorical variables in dependent (e.g., consequent or endogenous) roles.  However, dichotomous and ordered factors are suitable as independent variables and covariates.  

* Rework the problem in the chapter by changing the random seed in the code that simulates the data.  This should provide minor changes to the data, but the results will likely be very similar. 
* There are a number of variables in the dataset.  Swap out one or more variables in the simple moderation and compare your solution to the one in the chapter (and/or one you mimicked in the journal article).
* Conduct a simple moderation with data to which you have access. This could include data you simulate on your own or from a published article.

### Readings & Resources

In preparing this chapter, I drew heavily from the following resource(s). Other resources are cited (when possible, linked) in the text with complete citations in the reference list.

Regarding ordinary least squares (OLS) versus maximum likelihood estimation (MLE), these articles are extremely helpful:

* Cohen, J. (2003). Maximum likelihood estimation. Section 13.2.9 (pp. 498-499).  *Applied multiple regression/correlation analysis for the behavioral sciences* (3rd ed.). Erlbaum Associates.
* Cumming, G. (2014). The New Statistics: Why and How. Psychological Science, 25(1), 7–29. https://doi.org/10.1177/0956797613504966
* Myung, I. J. (2003). Tutorial on maximum likelihood estimation. *Journal of Mathematical Psychology, 47*(1), 90–100. https://doi.org/10.1016/S0022-2496(02)00028-7 (skim for big ideas)
* Rodgers, J. L. (2010). The epistemology of mathematical and statistical modeling: A quiet methodological revolution. *American Psychologist, 65*(1), 1–12. https://doi.org/10.1037/a0018326

Regarding the topic of moderation, I drew heavily from these resources.  

* Hayes, A. F. (2018). *Introduction to Mediation, Moderation, and Conditional Process Analysis, Second Edition: A Regression-Based Approach*. Guilford Publications. http://ebookcentral.proquest.com/lib/spu/detail.action?docID=5109647
  - Chapter 7: Fundamentals of Moderation Analysis: This chapter focuses on the basics of moderation analysis.  Our goal is to transfer and apply the knowledge to models we run in lavaan.  An excellent review of centering, visualizations, and probing moderations.
  - Chapter 8:  Extending the Fundamental Principles of Moderation Analysis (pp. 267-301):  Hayes addresses common regression concerns such as (a) hierarchical vs. simultaneous entry and (b) comparison of moderated regression with 2x2 factorial ANOVA.
  - Chapter 9:  Some Myths and Additional Extensions of Moderation Analysis (pp. 303-347). Hayes identifies "truths and myths" about mean centering and standardization. For sure these are important topics and his take on them is clear and compelling.
  - Appendix A:  An essential tool for PROCESS users because, even when we are in the R environment, this is the "idea book." That is, the place where all the path models are presented in figures.

The research vignette for this chapter:

* Kim, P. Y., Kendall, D. L., & Cheon, H.-S. (2017). Racial microaggressions, cultural mistrust, and mental health outcomes among Asian American college students. *American Journal of Orthopsychiatry, 87*(6), 663–670. https://doi-org.ezproxy.spu.edu/10.1037/ort0000203

### Packages

The script below will (a) check to see if the following packages are installed on your computer and, if not (b) install them.

```r
#will install the package if not already installed
if(!require(apaTables)){install.packages("apaTables")}
```

```
Loading required package: apaTables
```

```r
if(!require(lavaan)){install.packages("lavaan")}
```

```
Loading required package: lavaan
```

```
This is lavaan 0.6-16
lavaan is FREE software! Please report any bugs.
```

```r
if(!require(semPlot)){install.packages("semPlot")}
```

```
Loading required package: semPlot
```

```r
if(!require(tidyverse)){install.packages("tidyverse")}
```

```
Loading required package: tidyverse
```

```
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.2     ✔ readr     2.1.4
✔ forcats   1.0.0     ✔ stringr   1.5.0
✔ ggplot2   3.4.3     ✔ tibble    3.2.1
✔ lubridate 1.9.2     ✔ tidyr     1.3.0
✔ purrr     1.0.1     
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
if(!require(psych)){install.packages("psych")}
```

```
Loading required package: psych

Attaching package: 'psych'

The following objects are masked from 'package:ggplot2':

    %+%, alpha

The following object is masked from 'package:lavaan':

    cor2cov
```

```r
if(!require(jtools)){install.packages("jtools")}
```

```
Loading required package: jtools
```

## On *Modeling*:  Introductory Comments on the simultaneously invisible and paradigm-shifting transition we are making

### NHST versus modeling

At least a decade old now, Rogers' [-@rodgers_epistemology_2010] article in the *American Psychologist* is one of my favorites.  In it, he explores the notion of *statistical modeling*.  He begins with criticisms of null hypothesis statistical testing by describing how it has become a awkward and incongruent blend of Fisherian (i.e., R.A. Fisher) and Neyman-Pearson (i.e., Jerzy Neyman and E. S. Pearson) approaches.

**Table 1**

|Contributions of the Fisherian and Neyman-Pearson Approaches to NHST [@rodgers_epistemology_2010]
|:----------------------------------------------------------------------------------------------------------|

|                         
|:----------------------------------------------------:|:--------------------------------------------------:|
|**Fisher**                                            |**Neyman-Pearson**                                  |

|                         
|:-----------------------------------------------------|:---------------------------------------------------|
|Developed NHST to answer scientific questions and evaluate theory. |Sought to draw conclusions in applied settings such as quality control.|
|Took an incremental approach to hypothesis testing that involved replication and (potentially) self-correcting; as such viewed *replication* as a critical element.|Placed emphasis on the importance of each individual decision.|
|Never used the terms, "alternative hypothesis" or "alpha level." Rather, Fisher used the distribution of the null model to examine "whether the data look weird or not."|Designed their approach to detect an "alternative hypothesis."|
|Gave us the null hypothesis and *p* value. |Gave us the alternative hypothesis, alpha level, and power.|

Over time, these overlapping, but inconsistent, approaches became intertwined. Many students of statistics do not recognize the incompatibilities. Undoubtedly, it makes statistics more difficult to learn (and teach). Below are some of the challenges that Rodgers [-@rodgers_epistemology_2010] outlined.

* Rejecting the null does not provide logical or strong support for the alternative
* Failing to rejct the null does not provide logical or strong support for the null.
* NHST is backwards because it evaluates the probability of the data given the hypothesis, rather than the probability of the hypothesis given the data.
* All point-estimate null hypotheses can be rejected if the sample size is large enough.
* Statistical significance does not necessitate practical significance.

Consequently, we have ongoing discussion/debates about power, effect sizes, sample size, Type I and II errors, confidence intervals, fit statistics, and the relations between them.

### Introducing:  *The Model*

Understanding modeling in our *scientist-practitioner* context probably needs to start with understanding the *mathematical model*.  Niemark and Este [-@niemark_stimulus_1967] defined a mathematical model as a set of assumptions together with implications drawn from them by mathematical reasoning. Luce [@luce_four_1995] suggested that mathematical equations capture model-specific features by highlighting some aspects while ignoring others. The use of mathematics helps us uncover the "structure." For example, the *mean* is a mathematical model. *I always like to stop and think about that notion...about what the mean represents and what it doesn't.*  Pearl [-@pearl_causality_2000] defined the model as an idealized representation of reality that highlights some aspects and ignores others by suggesting that a model:

* matches the reality it describes in some important ways.
* is simpler than that reality.

As we transition from the NHST approach to statistical modeling there is [@rodgers_epistemology_2010]:

* decreased emphasis on
  - null hypothesis
  - *p* values
* increased emphasis on
  - model residuals
  - degrees of freedom
  - additional indices of *fit*
  
Further, statistical models [@rodgers_epistemology_2010]:

* are more readily falsifiable
* require greater theoretical precision
* include assumptions that are more readily evaluated
* offer more practical application

Circling back around to Fisher and Neyman-Pearson, Rogers [-@rodgers_epistemology_2010] contended that Fisher's work provided a framework for modeling because of the model process of specification, estimation, and goodness of fit. As we move into more complex modeling, we will spend a great deal of time understanding parameters and their relationship to degrees of freedom.  Fisher viewed degrees of freedom as *statistical currency* that could be used in exchange for the estimation of parameters.

If this topic is exciting to you, let me refer you to Cumming's [@cumming_new_2014] article, "The New Statistics:  Why and How," in the Journal, *Psychological Science"


## OLS to ML for Estimation

### Ordinary least squares (OLS)

Known by a variety of names, the estimation algorithm typically used in regression models (linear, hierarchical, multiple, sequential) is *ordinary least squares* (OLS; also termed least squares criterion, general least squares, etc.). As we move into multivariate (and then psychometrics) we are going to transition our estimation method from OLS to MLE.  Consequently, it is essential to understand some underlying differences [@cohen_applied_2003; @myung_tutorial_2003]

In OLS regression:

* The estimated values of regression coefficients are chosen so that the sum of squared errors is minimized (aka, the *least squares criteria*). Consequently, 
  - the mean of errors is zero, and
  - the errors correlate *zero* with each predictor
* The solution to OLS regression is *analytic*
  - the equations from which the coefficients are created are *known normal equations*. Among other places, you can look them up in CCW&A [@cohen_introduction_1934] Appendix 1)






<img src="07-SimpleMod_files/figure-html/drawing a line of best fit-1.png" width="672" />

### Maximum likelihood estimation (MLE):  A brief orientation

Although I started this chapter with a critique of NHST, Fisher is credited [@myung_tutorial_2003] with the original development of the central principal of *maximum likelihood estimation* which is that the desired probability distribution is the one that makes the observed data *most likely*. As such, the *MLE estimate* is a resulting parameter vector that maximizes the likelihood function. Myung's [-@myung_tutorial_2003] tutorial provides an excellent review. My summary is derived from Dr. Myung article.  A *likelihood* is a measure of how *typical* a person (or sample) is of that population. 

  * When there is one IV the MLE distribution behaves like a chi-square distribution (which also tests observed versus expected data).
  * There is a point in the MLE curve that represents where the maximum likelihood exists that the data is likely given the model.
  * When there are multiple IVs, this simple curve takes the shape of a *k* dimensional geometrical surface.

Extended to regression, we are interested in the *likelihoods* of individuals having particular scores on Y, given values on predictors $x_{1}$ to $x_{k}$ (and the specific values of regression coefficients chosen as the parameter estimates)

* MLE provides *maximum likelihood estimates* of the regression coefficients (and SEs) that is, estimates that make a sample as likely or typical as possible
* *L* is a symbol for *maximum likelihood of a sample*
* The solutions are *iterative* (i.e., identified by trial-and-error; with each trial informed by the prior)
  - a statistical criteria is specified for the coefficients to be chosen
  - different values of coefficients are tried
  - these *iterations* continue until the regression coefficients cease to change by more than a small amount (i.e., the *convergence criteria*)
  - hopefully, a set of coefficients is found that makes the solution as close to the statistical criteria (i.e., maximum likelihood) as possible
* The *optimization algorithm* does not guarantee that a set of parameters will be found; convergence failures may be caused by
  - multicollinearity among predictors
  - a large number of predictors
  - the *local maxima problem*; the optimization algorithm returns sub-optimal parameter values [@myung_tutorial_2003]
* MLE is a *full information model*
  - calculates the estimates of model parameters all at once
* MLE is for large samples
* MLE assumptions include
  - independence of observations
  - multivariate normality of endogenous variables
  - independence of exogeneous variables and disturbances
  - correct specification of the model (MLE is only appropriate for testing theoretically informed models)
  
### OLS and MLE Comparison

In this table we can compare OLS and MLE in a side-by-side manner.
**Table 2**

|Comparing OLS and MLE [@cohen_applied_2003; @myung_tutorial_2003]
|:------------------------------------------------------------------------------------------------------|

|                         
|:---------------:|:----------------------------------------:|:----------------------------------------:|
|**Criterion**    |**Ordinary Least Squares (OSL)**          |**Maximum Likelihood Estimation (MLE)**   |

|                         
|:---------------:|:----------------------------------------:|:----------------------------------------:|
|Parameter values chosen to...|minimize the distance between the predictions from regression line and the observations; considered to be those that are *most accurate*|be those that are *most likely* to have produced the data|
|Parameter values are obtained by|equations that are known and linear (you can find them in the "back of the book")|a non-linear optimization algorithm|
|Preferred when...|sample size is small|sample size is large, for complex models, non-linear models, and when OLS and MLE results differ|
|In R...|the *lm()* function in base R|*lavaan* and other packages*; specifying the FIML option allows for missing data (without imputation)|

### Hayes and PROCESS (aka conditional process analysis)

In the early 2000s, the bias-corrected, bootstrapped, confidence interval (CI) was identifed as a more powerful approach to assessing indirect effects than the classic Sobel test. Because programs did not produce them,  no one was using them. Preacher, Edwards, Lambert, Hayes, and colleagues created Excel worksheets that would calculate these (they were so painful).  Hayes turned this process into a *series* of macros to do a variety of things for SPSS and other programs.  Because of his clear, instructional, text, PROCESS is popular. In 2021, Hayes released the PROCESS macro for R. It can be downloaded at the [ProcessMacro website](https://www.processmacro.org/download.html). Documention for it is newly emerging. Although PROCESS produces bias-corrected, bootstrapped confidence intervals, for models with indirect effects, PROCESS utilizes OLS as the estimator.

Although most regression models can be completed with the *lm()* function in base R, it can be instructive to run a handful of these familiar models with *lavaan* (or even PROCESS) as a precurser to more complicated models.

## Introducing the *lavaan* package

In the regression classes (as well as in research designs that are cross-sectional, non-linear, and can be parsimoniously and adequately measured with OLS regression) we typically use the base R function, *lm()* ("linear model") which relies on an OLS algorithm. You can learn about it with this simple code:


```r
#?lm
```

Rosseel's [-@rosseel_lavaan_2020] *lavaan* package was developed for SEM, but is readily adaptable to most multiple regression models. Which do we use and when?

* For relatively simple models that involve only predictors, covariates, and moderators, *lm()* is adequate.
* Models that involve mediation need to use *lavaan*
* SEM/CFA needs *lavaan*
* If your sample size is small, *but* you are planning a mediation, it gets tricky (try to increase your sample size) because MLE estimators rely on large sample sizes (how big?  hard to say).


### The FIML magic for which we have been waiting

There are different types of maximum likelihood.  In this chapter we'll utilize *full information maximum likelihood* (FIML).  FIML is one of the most practical missing data estimation approaches around and is especially used in SEM and CFA.  When data are thought to be MAR (missing at random) or MCAR (missing completely at random), it has been shown to produce unbiased parameter estimates and standard errors.

The FIML approach works by estimating a likelihood function for each individual based on the variables that are present so that all available data are used.  Model fit is calculated from (or informed by) the fit functions for all individual cases. Hence, "FIML" is *full information* maximum likelihood. 

When I am able to use *lavaan*, my approach is to use Parent's AIA (available information analysis, -@parent_handling_2013) approach to scoring data, then specify a FIML approach (i.e., adding *missing = 'fiml'*) in my lavaan code. Even though the text-book examples we work have complete data, I will try to include this code so that it will be readily available for you, should you use the as templates for your own data.

In this portion of the ReCentering Psych Stats series we are headed toward more complex models that include both mediation and moderation. Hayes [@hayes_introduction_2018] would call this "conditional process analysis."  Others would simply refer to it as "path analysis."  Although all these terms are sometimes overlapping, *path analysis* is a distinction from *structural equation modeling* (SEM) where latent variables are composed of the observed variables. Let's take a look at some of the nuances of the whole SEM world and how it relates to PROCESS.

**SEM** is broad term (that could include CFA and path analysis) but is mostly reserved for models with some type of latent variable (i.e., some might exclude path analysis from its definitions).  SEM typically uses some form of MLE (not ordinary least squares).

*Latent variables* (circles in the model, below) are those that are "created" in the analytic process but will never appear as a column in your dataset. It may be easiest to think of a latent variable as a scale score -- where you sum (or average) the indicator item values to get the score (except we don't do that).  Rather, the LV is "indicated" by variance the indicator/observed/manifest variables share with each other.

The image below is of a simple mediation model but the variables in the model are latent, and indicated by each of the 3 observed/manifest variables.  PROCESS (in SPSS) could not assess this model because PROCESS uses ordinary least squares regression and SEM will use a maximum likelihood estimator.

![Image of a simple mediation model with latent variables](images/SimpleMod/SimpleMedLV.jpg)

**Confirmatory factor analysis** (CFA) is what we'll do in psychometrics.  Purely SEM, CFA is used to evaluate the structural validity of a scale or measure.  In pure CFA, first-order factors represent subscales and a second-order factor (not required) might provide support for a total scale score.  For example, in the above figure, the three squares represent the observed (or manifest) items to which a person respond.  In CFA, we evaluate their adequacy to represent the latent variable (circle) construct. It's a little more complicated than this, but this will get you started.  Mediation/indirect effects are not assessed in a pure CFA.

**Path analysis** is a form of SEM, but without latent variables.  That is, all the variables in the model are directly observed.  They are represented by squares/rectangles and each has a corresponding column in a dataset.  PROCESS in SPSS is entirely path analysis.

![Image of a simple mediation in path analysis](images/SimpleMod/SimpleMed.jpg)

**Hybrid models** are a form of SEM that include observed/manifest variables as predictors along with other latent variables.  In the diagram below, you see tiny little measurement models (3 indicators that "create" or "inform" an LV, think baby CFA) and one predictor that is manifest.  An example might be a categorical predictor (e.g., treatment, control).

![Image of a simple mediation in path analysis](images/SimpleMod/HybridMed.jpg)

## Picking up with Moderation

**Moderation**:  The effect of X (IV) on some variable Y (DV) is moderated if its size, sign, or strength depends on or can be predicted by W (moderator). In that case, W is said to be a *moderator* of X's effect on Y.  Or, that W and X *interact* in their influence on Y.

Identifying a moderator of an effect helps establish the *boundary conditions* of that effect or the circumstances, stimuli, or type of people for which the effect is large versus small, present versus absent, positive versus negative, and so forth.

**Conditional vs Unconditional Effects**:  Consider the following two equations:

$$\hat{Y} = i_{y}+b_{1}X + b_{2}W + e_{y}$$

and 

$$\hat{Y} = i_{y}+b_{1}X + b_{2}W + b_{3}XW+ e_{y}$$

The first equation constrains X's effect to be unconditional on W, meaning that it is invariant across all values of W.  By introducting the interaction term ($b_{3}XW$), we can evaluate a model where X's effect can be dependent on W.  That is, for different values of W, X's effect on Y is different.  The resulting equation (#2) is the *simple linear moderation model.*  In it, X's effect on Y is *conditional*.


## Workflow for a Simple Moderation

Below is a workflow comparing the approaches to analyzing a regression model (moderators only) with OLS and MLE.

![Image of a simple mediation in path analysis](images/SimpleMod/OLS_MLEwrkflow.jpg)

The Bonus Track at the end of the chapter includes script templates with just X and Y variables.

## Research Vignette

The research vignette comes from the Kim, Kendall, and Cheon's [-@kim_racial_2017], "Racial Microaggressions, Cultural Mistrust, and Mental Health Outcomes Among Asian American College Students."  Participants were 156 Asian American undergraduate students in the Pacific Northwest. The researchers posited the a priori hypothesis that cultural mistrust would mediate the relationship between racial microaggressions and two sets of outcomes:  mental health (e.g., depression, anxiety, well-being) and help-seeking.

Variables used in the study included:

* **REMS**:  Racial and Ethnic Microaggressions Scale (Nadal, 2011). The scale includes 45 items on a 2-point scale where 0 indicates no experience of a microaggressive event and 1 indicates it was experienced at least once within the past six months.  Higher scores indicate more experience of microaggressions.
* **CMI**:  Cultural Mistrust Inventory (Terrell & Terrell, 1981). This scale was adapted to assess cultural mistrust harbored among Asian Americans toward individuals from the mainstream U.S. culture (e.g., Whites). The CMI includes 47 items on a 7-point scale where higher scores indicate a higher degree of cultural mistrust.
* **ANX**, **DEP**, **PWB**:  Subscales of the Mental Health Inventory (Veit & Ware, 1983) that assess the mental health outcomes of anxiety (9 items), depression (4 items), and psychological well-being (14 items).  Higher scores (on a 6 point scale) indicate stronger endorsement of the mental health outcome being assessed.
* **HlpSkg**:  The Attiudes Toward Seeking Professional Psychological Help -- Short Form (Fischer & Farina, 1995) includes 10 items on a 4-point scale (0 = disagree, 3 = agree) where higher scores indicate more favorable attitudes toward help seeking.

### Simulate Data from the Journal Article

First, we simulate the data from the means, standard deviations, and correlation matrix from the journal article.


```r
#Entering the intercorrelations, means, and standard deviations from the journal article
mu <- c(.34, 3.00, 2.98, 2.36, 3.50, 1.64)
sd <- c(.16, .83, .99, .90, .90, .53)
r_mat <- matrix (c(1,   .59, .26,   .34,  -.25, -.02,
                  .59, 1.00, .12,   .19,  -.28, .00, 
                  .26,  .12, 1.00, .66,  -.55, .07,
                  .34,  .19, .66,  1.00, -.66, .05,
                 -.25, -.28, -.55,-.66,  1.00, .08, 
                 -.02,  .00,  .07, .05, .08,  1), ncol = 6)
#Creating a covariance matrix
cov_mat <- sd %*% t(sd) * r_mat

#Set random seed so that the following matrix always gets the same results.
set.seed(210409)
library(MASS)
Kim_df <- mvrnorm(n = 156, mu=mu, Sigma = cov_mat, empirical = TRUE)
#renaming the variables
as.data.frame(Kim_df, row.names = NULL, optional = FALSE, make.names = TRUE)
library(tidyverse)
Kim_df <- Kim_df%>%
  as.data.frame %>%
  rename(REMS = V1, CMI = V2, ANX = V3, DEP = V4, PWB = V5, HlpSk = V6)
#Checking our work against the original correlation matrix
#round(cor(Kim_df),3)
```
We can perform a quick check of our data to check its alignment with the journal article, and also get a sense of the bivariate relations with a couple of useful tools. The package *apaTables* produces a journal-ready table with means, standard deviations, and the correlation matrix.


```r
library(psych)
psych::pairs.panels(Kim_df)
```

<img src="07-SimpleMod_files/figure-html/pairs panels for Kim et al-1.png" width="672" />

Kim et al. [-@kim_racial_2017] did not conduct any moderation analyses in their article. Core to their analysis was predicting mental health outcomes (e.g., anxiety, depression, psychological well-being).  Their predictors were racial/ethnic microaggressions, cultural mistrust, and help-seeking behaviors. In the majority of their models, REMS was the independent variable, predicting one of the mental health outcomes, mediated by cultural mistrust. Given the strong correlation with REMS (*r* = 0.59) the choice of CMI as a mediator is sound.

In looking at the data, I will ask the question, "Does help-seeking (HlpSk) moderate the relationship between REMS and ANX?"

![Conceptual diagram of a proposed simple moderation model using Kim et al. data](images/SimpleMod/KimMod.jpg)

![Statistical diagram of a proposed simple moderation model using Kim et al. data](images/SimpleMod/KimModStatistical.jpg)

Here is the formulaic rendering:
$$Y = i_{Y}+ b_{1}X+ b_{2}W + b_{3}XW +e_{Y}$$

## Working the Simple Moderation with OLS and MLE

### OLS with *lm()*

In this demonstration we will use the *lm()* function in base R to evaluate help seeking behaviors (HlpSK) as a moderator to the relationship between racial/ethnic microaggressions (REMS) on anxiety (ANX). Ordinary least squares is the estimator used in *lm()*. We will probe the moderating effect with both pick-a-point and Johnson-Neyman approaches.

Let's specify this simple moderation model with base R's *lm()* function. We'll use the *jtools* package so we get that great summ function and *interactions* for an awesome plot.


```r
library(jtools) #the summ function creates a terrific regression table
library(interactions)
library(ggplot2)

KimSimpMod <- lm(ANX~REMS*HlpSk, data=Kim_df)
#summary(KimSimpMod)
```

**Table 3**

```r
KimSimpMod_summ <- summ(KimSimpMod, digits = 3)
KimSimpMod_summ
```

<table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Observations </td>
   <td style="text-align:right;"> 156 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Dependent variable </td>
   <td style="text-align:right;"> ANX </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Type </td>
   <td style="text-align:right;"> OLS linear regression </td>
  </tr>
</tbody>
</table> <table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;">
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> F(3,152) </td>
   <td style="text-align:right;"> 5.047 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> R² </td>
   <td style="text-align:right;"> 0.091 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Adj. R² </td>
   <td style="text-align:right;"> 0.073 </td>
  </tr>
</tbody>
</table> <table class="table table-striped table-hover table-condensed table-responsive" style="width: auto !important; margin-left: auto; margin-right: auto;border-bottom: 0;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Est. </th>
   <th style="text-align:right;"> S.E. </th>
   <th style="text-align:right;"> t val. </th>
   <th style="text-align:right;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> (Intercept) </td>
   <td style="text-align:right;"> 1.280 </td>
   <td style="text-align:right;"> 0.618 </td>
   <td style="text-align:right;"> 2.073 </td>
   <td style="text-align:right;"> 0.040 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> REMS </td>
   <td style="text-align:right;"> 4.315 </td>
   <td style="text-align:right;"> 1.655 </td>
   <td style="text-align:right;"> 2.607 </td>
   <td style="text-align:right;"> 0.010 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> HlpSk </td>
   <td style="text-align:right;"> 0.683 </td>
   <td style="text-align:right;"> 0.350 </td>
   <td style="text-align:right;"> 1.952 </td>
   <td style="text-align:right;"> 0.053 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> REMS:HlpSk </td>
   <td style="text-align:right;"> -1.596 </td>
   <td style="text-align:right;"> 0.938 </td>
   <td style="text-align:right;"> -1.702 </td>
   <td style="text-align:right;"> 0.091 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; " colspan="100%">
<sup></sup> Standard errors: OLS</td></tr></tfoot>
</table>



Looking at these results we can see that the predictors account for about 10% of variance in anxiety. It appears that there is a statistically significant interaction of REMS and HlpSk on Anxiety.  The *interaction_plot()* function from the package, *interactions* can make helpful illustrations.  In the case of interactions/moderations, I like to run them "both ways" to see which makes more sense.


```r
interact_plot(KimSimpMod, pred = HlpSk, modx = REMS)
```

<img src="07-SimpleMod_files/figure-html/Plot Kim SimpMod-1.png" width="672" />

```r
interact_plot(KimSimpMod, pred = REMS, modx = HlpSk)
```

<img src="07-SimpleMod_files/figure-html/Plot Kim SimpMod-2.png" width="672" />

The first figure (where REMS is the moderator) illustrates that for those with the highest experience of racial/ethnic microaggression, the relationship between help-seeking and anxiety is strong ad positive. Anxiety is the highest for those who are +1SD above the mean on REMS and who have sought help. This slope is far less strong for those at the mean on REMS and the slope trends negative for those at the lowest REMS.
 
The second figure places HlpSg as the moderator. The results are the same, merely presented differently. Here we see that at all levels of help seeking, there is a positive relationship between REMS and anxiety. The relationship is the sharpest for those who are at +1SD above the mean on help-seeking. That is, the highest levels of anxiety are among those who experience the most racial and ethnic microaggressions and have the most favorable attitudes toward help-seeking.

Next, let's probe the interaction with simple slopes.  With these additional inferential tests we can see where in the distribution of the moderator, X has an effect on Y that is different from zero (and where it does not). There are two common approaches.

The Johnson-Neyman is a *floodlight* approach and provides an indication of the places in the distribution of W (moderator) that X has an effect on Y that is different than zero. The pick-a-point is sometimes called the *analysis of simple slopes* or a *spotlight* approach, probes the distribution at specific values (often the *M* +/- 1*SD*).  


```r
sim_slopes(KimSimpMod, pred = REMS, modx = HlpSk)
```

```
JOHNSON-NEYMAN INTERVAL 

When HlpSk is INSIDE the interval [-4.47, 2.01], the slope of REMS is p <
.05.

Note: The range of observed values of HlpSk is [0.28, 2.90]

SIMPLE SLOPES ANALYSIS 

Slope of REMS when HlpSk = 1.11 (- 1 SD): 

  Est.   S.E.   t val.      p
------ ------ -------- ------
  2.54   0.72     3.51   0.00

Slope of REMS when HlpSk = 1.64 (Mean): 

  Est.   S.E.   t val.      p
------ ------ -------- ------
  1.70   0.48     3.53   0.00

Slope of REMS when HlpSk = 2.17 (+ 1 SD): 

  Est.   S.E.   t val.      p
------ ------ -------- ------
  0.85   0.66     1.30   0.20
```

```r
#sim_slopes(KimSimpMod, pred=GRIcntlty, modx = GRMS) #sometimes I like to look at it in reverse -- like in the plots
```
The Johnson-Neyman suggests that the relationship between REMS and ANX is statistically significant when HlpSk is above 1.34 (the mean of help-seeking is 1.64). We see the same result in the pick-a-point approach where there is a non-significant relationship between REMS and anxiety when help-seeking is 1SD below the mean. However, there is a statistically significant relationship between help-seeking and REMS when help-seeking is at and above the mean.

#### An APA Style Write-up of OLS results

**Method/Analytic Strategy**

Data were analyzed with an ordinary least squares approach with the base R function (v. 4.0.4), *lm()*. We specified a model predicting anxiety (ANX) from the interacting effects of racial and ethnic microaggressions (REMS) and attitudes toward help-seeking (HlpSk).

**Results**

**Preliminary Analyses**

*  Missing data analyses and managing missing data
*  Bivariate correlations, means, SDs
*  Distributional characteristics, assumptions, etc.
*  Address limitations and concerns

**Primary Analyses**
A multiple regression analysis was conducted to predict anxiety from racial and ethnic microaggressions and attitudes toward help-seeking. Results supported a statistically significant interaction effect that accounted for 8% of the variance. Probing the interaction effect with Johnson-Neyman and pick-a-point approaches indicated that the relationship between REMS and anxiety is non-significant when help-seeking is 1SD below the mean, but is significant when help-seeking is at and above the mean. Results are listed in Table 3.  As illustrated in Figure 1, the relationship between REMS and anxiety is the sharpest for those who are at +1SD above the mean on help-seeking. That is, the highest levels of anxiety are among those who experience the most racial and ethnic microaggressions and have the most favorable attitudes toward help-seeking.


### MLE with *lavaan::sem()*

Specifying the path analysis in lavaan

Things to note:

* MLE has an element of random, by setting the seed we tell it where to "start"...so we all get the same answer
* The code below "draws our model."  It opens and close with ' marks
* "Labels" (e.g., b1, b2) are useful for identifying the paths. But later in path analysis (mediation) we will use them to do some calculations (i.e., multiplying paths to create an indirect effect).  In SEM/CFA (latent variable modeling) we can use them to "fix and free" constraints; the asterisk makes them look like interactions, but they are not
* Interactions are created with the colon
* We can use hashtags internal to the code to remind ourselves (or teach others)
* Following specification of the model, we use the lavaan function *sem()* to conduct the estimation 
  - adding *missing = 'fiml'* is the magic we have been waiting for with regard to missing data
  - bootstraping is an MLE tool that gives us greater power (more later in mediation)
  - the *summary()* and *parameterEstimates()* functions get us the desired output















