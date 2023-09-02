# MEDIATION  {-#MED}

# Simple Mediation {#SimpleMed}

 [Screencasted Lecture Link](https://spu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?pid=7ffb03e6-b34b-4e0b-8f10-ad080180b069) 

The focus of this lecture is to estimate indirect effects (aka "mediation").  We examine the logic/design required to support the argument that *mediation* is the *mechanism* that explains the X --> Y relationship.  We also work three examples (one with covariates).

At the outset, please note that although I rely heavily on Hayes [-@hayes_introduction_2018] text and materials, I am using the R package *lavaan* in these chapters.  Very recently, Hayes has introduced a [PROCESS macro for R](https://www.processmacro.org/index.html). Because I am not yet up-to-speed on using this macro (it is not a typical R package) and because we will use *lavaan* for confirmatory factor analysis and structural equation modeling, I have chosen to utilize the *lavaan* package.  A substantial difference is that the PROCESS macros use ordinary least squares and *lavaan* uses maximum likelihood estimators.

## Navigating this Lesson

There is about 1 hour and 10 minutes of lecture.  If you work through the materials with me it would be plan for an additional 1.5 hours.

While the majority of R objects and data you will need are created within the R script that sources the chapter, ocasionally there are some that cannot be created from within the R framework. Additionally, sometimes links fail.  All original materials are provided at the [Github site](https://github.com/lhbikos/ReC_MultivModel) that hosts the book. More detailed guidelines for ways to access all these materials are provided in the OER's [introduction](#ReCintro)

### Learning Objectives

Learning objectives from this lecture include the following:

* Define mediation and indirect effect. 
* Distinguish the role of a mediating variable from independent variables, covariates, and moderators.
* Identify the conditions upon which there can be justification to support the presence of a mediated effect.
* Articulate the arguments for and against using the term, "mediation."
* Using the R package *lavaan*, 
  - specify a model with indirect effects,
  - identify and interpret B weights, *p* values, and *CIs* for total, direct, and indirect effects, 
  - calculate the total effects of X and M on Y, 
  - identify the proportion of variance accounted for in predicting M and Y.
* Hand calculate the values of an indirect, direct, and total effects from statistical output or a figure (just the $B$ or $\beta$, not the significance level)

### Planning for Practice

The following suggestions for practice will involve specifying, testing, and interpreting a model with a single indirect effect (mediator).

* Rework the problem in the chapter by changing the random seed in the code that simulates the data.  This should provide minor changes to the data, but the results will likely be very similar.
* There are a number of variables in the dataset and there were a handful of simple mediations conducted in the journal article that sources the research vignette.  Swap out one or more variables in the model of simple mediation and compare your solution to the one in the chapter and/or the research article.
* Conduct a simple mediation with data to which you have access. This could include data you simulate on your own or from a published article.

### Readings & Resources

In preparing this chapter, I drew heavily from the following resource(s). Other resources are cited (when possible, linked) in the text with complete citations in the reference list.

* Hayes, A. F. (2018). *Introduction to mediation, moderation, and conditional process anlaysis:  A regression-based approach*. New York, NY: Guilford Press. Available as an ebook from the SPU library:  https://ebookcentral-proquest-com.ezproxy.spu.edu/lib/spu/detail.action?docID=5109647 
  - **Chapter 3, Simple mediation**:  Hayes' text is another great example of a teaching tool that is accessible at both procedural and conceptual levels.   I especially appreciate  his attention to the controversies (even those directed toward his work).  We deviate from his text in that we are not using the PROCESS macro...and I'll address those concerns in the lecture.
  - **Chapter 4, Causality and confounds**:  A great chapter that addresses "What happened to Baron & Kenny"; partial v complete mediation; and conditions required for claims of causality.  Procedurally, our focus in this chapter is on the role of covariates. 
  - **Appendix A:  Using Process**:  An essential tool for PROCESS users because, even when we are in the R environment, this is the "idea book." That is, the place where all the path models are presented in figures.
* Kim, P. Y., Kendall, D. L., & Cheon, H.-S. (2017). Racial microaggressions, cultural mistrust, and mental health outcomes among Asian American college students. *American Journal of Orthopsychiatry, 87*(6), 663–670. https://doi-org.ezproxy.spu.edu/10.1037/ort0000203

### Packages

The script below will (a) check to see if the following packages are installed on your computer and, if not (b) install them.

<!-- TODO: Build out this section. -->

```r
#will install the package if not already installed
if(!require(lavaan)){install.packages("lavaan")}
if(!require(semPlot)){install.packages("semPlot")}
if(!require(tidyverse)){install.packages("tidyverse")}
if(!require(psych)){install.packages("psych")}
if(!require(formattable)){install.packages("formattable")}
if(!require(semTable)){install.packages("semTable")}
```

## Estimating Indirect Effects (the analytic approach often termed *mediation*)

### The definitional and conceptual

As in Hayes text [-@hayes_introduction_2018], we will differentiate between *moderation* and *mediation*.  *Conditional process analysis* involves both! With each of these, we are seeking to understand the *mechanism* at work that leads to the relationship (be it correlational, predictive, or causal)

Even though this process has sometimes been termed *causal modeling*, Hayes argues that his *statistical approach* is not claiming to determine *cause*; that is really left to the argument of the research design. 

**Moderation** (a review):  

* Answers questions of *when* or *for whom* and is often the source of the answer, *it depends*.
* Think of our *interaction* effects in ANOVA and regression
* The effect of X on some variable Y is moderated by W if its size, sign, or strength depends on, or can be predicted, by W.  Then we can say, "W is a *moderator* of X's effect on Y" or "W and X *interact* in their influence on Y."
* The image below illustrates moderation with *conceptual* and *statistical* diagrams. Note that three predictors (IV, DV, their interaction) point to the DV.

![Image of Hayes'style conceptual and statistical diagrams of a simple moderation](images/SimpleMed/ModConcStat.jpg)


The classic plot of moderation results is often the best way to detect that an interaction was included in the analysis and helps understand the *conditional* (e.g., for whom, under what conditions) nature of the analysis.

![Image of classic interaction graph that illustrates a moderated effect.  The IV is on the X axis, DV on the Y axis, and two intersecting lines represent the differential/moderated effect of the IV on the DV by the moderator](images/SimpleMed/SimpleInteraction.jpg)

**Mediation**:  

* Answers questions of *how* (I also think *through* and *via* to describe the proposed mediating mechanism)
* Paths in a mediation model are *direct* (X does not pass through M on its way to Y) and *indirect* (X passes through M on its way to Y). Once we get into the statistics, we will also be focused on *total* effects.
* Hayes thinks in terms of *antecedent* and *consequent* variables. In a 3-variable, simple mediation, X and M are the antecedent variables; X and M are the consequent variables.  
* There is substantial debate and controversy about whether we can say "the effect of X on Y is *mediated* through M" or whether we should say, "There is a statistically significant indirect effect of X on Y thru M."  Hayes comes down on the "use mediation language" side of the debate.  
* In sum, a simple mediation model is any causal system in which at least one causal antecedent X variable is proposed as influencing an outcome Y through a single intervening variable, M.  In such a model there are two pathways by which X can influence Y.
* The figure below doubles as both the conceptual and statistical diagram of evaluating a simple mediation -- a simple indirect effect.

![Image of Hayes'style conceptual diagram of a simple moderation](images/SimpleMed/SimpleMed.jpg)

**Conditional process analysis**:  

* Used when the research goal is to understand the boundary conditions of the mechanism(s) by which a variable transmits its effect on another.  
* Typically, simultaneously, assesses the influence of mediating (indirect effects) and moderating (interactional effects) in a model-building fashion.
* In a conditional process model, the moderator(s) may be hypothesized to influence one or more of the paths.

We will work toward building a conditional process model, a moderated mediation, over the next several chapters.


![Image of conditional process analysis model where the moderator is hypothesized to change the a path; the path between the IV and mediator](images/SimpleMed/CPAmodel.jpg){#id .class width=250 height=180}

## Workflow for Simple Mediation

The workflow for a simple mediation is straightforward, however the figure below (i.e., the very traditional figure used to represent mediation) is very helpful in understanding the logic beneath mediation as the explanatory mechanism.

![Image of conditional process analysis model where the mediator is hypothesized to change the a path; the path between the IV and mediator](images/SimpleMed/MedRationale.jpg)

The top figure represents the bivariate relationship between the independent and dependent variable.  The result of a simple linear regression (one predictor) represent the *total* effect of the IV on the DV. We can calculate this by simply regressing the DV onto the IV.  The resulting $B$ weight is known as the *c* path.  A bivariate correlation coefficient results in the same value -- only it is standardized (so would be the same as the $\beta$ weight).

The lower figure represents that the relationship between the IV and DV is *mediated* by a third variable.  We assign three labels to the paths:  *a*, between the IV and mediator; *b*, between the mediator and DV; and *c'* (c prime) between the IV and DV.

Statistically speaking, a mediated relationship is supported when the value of *c'* is statistically significantly lower than *c*.  If this occurs, then we can say that the mediator is sharing some of the variance in the prediction of the DV.

You might already be imagining potential challenges to this model.  For example, which variable should be the IV and which one should be the mediator?  Can we switch them?  You can -- and you will likely have very similar (if not identical) results.  Good research design is what provides support for suggesting that mediation is the proper, casual, mechanism regarding the relationship between the IV and DV.  An excellent review of the challenges of establishing a robust mediation model is provided by Kline [-@kline_mediation_2015], where he suggests the following as the minimally required elements of a mediation design:

* the IV is an experimental variable with random assignment to conditions;
* the mediator is an individual difference variable that is not manipulated and is measured at a later time;and
* the DV is measured at a third occasion

These criteria are in addition to the rather standard criteria for establishing causality [see @stone-romero_research_2010 for a review]:

* temporal precedence,
* statistical covariation, and
* ruling out plausible rival hypotheses.

Some journals take this very seriously.  In fact [FAQs](https://www.journals.elsevier.com/journal-of-vocational-behavior/news/frequently-asked-questions-about-submitting-a-manuscript) in the  Journal of Vocational Behavior make it clear that they will very rarely publish a "mediation manuscript" unless it has a minimum of three waves.

Working through a mediation will help operationalize these concepts.


## Simple Mediation in *lavaan*:  A focus on the mechanics

The lavaan tutorial [@rosseel_lavaan_2020] provides a helpful model of how writing code to estimate an indirect effect. Using the lavaan tutorial as our guide, let's start with just a set of fake data with variable names that represent X (predictor, IV, antecedent), M (mediator, atencedent, consequent), and Y (outcome, DV, consequent). 

### Simulate Fake Data

The code below is asking to create a dataset with a sample size of 100.  The dataset has 3 variables, conveniently named X (predictor, antecedent, IV), M (mediator), and Y (outome, consequent, DV).  The R code asks for random selection of numbers with a normal distribution.  You can see that the M variable will be related to the X variable by + .5; and the Y variable will be related to the M variable by + .7.  This rather ensures a statistically significant indirect effect.

<!-- TODO: Return and replace with data from our mediation) -->


```r
set.seed(210410)
X <- rnorm(100)
M <- 0.5*X + rnorm(100)
Y <- 0.7*M + rnorm(100)
Data <- data.frame(X = X, Y = Y, M = M)
```


### Specify Mediation Model

The package we are using is *lavaan*.  Hayes' model is *path analysis*, which can be a form of structural equation modeling.  As a quick reminder, in SPSS, PROCESS is limited to ordinary least squares regression.  We will use maximum likliehood estimators for the Hayes/PROCESS examples, but *lavaan* can take us further than PROCESS because

* We can (and, in later chapters,  will) do latent variable modeling.
* We can have more specificity and flexibility than the prescribed PROCESS models allow.  I say this with all due respect to Hayes -- there is also a good deal of flexibility to be able to add multiple mediators and covariates within most of the Hayes' prescribed models.

Hayes text is still a great place to start because the conceptual and procedural information is clear and transferable to the R environment.


```r
library(lavaan)
```

```
## This is lavaan 0.6-16
## lavaan is FREE software! Please report any bugs.
```

Our atheoretical dataset makes it easy to identify which variable belongs in each role (X,Y,M).  When specifying the paths in lavaan, here's what to keep in mind: 

* Name your model/object (below is X, "<-" means "is defined by")
* The model exists between 2 single quotation marks (the odd looking ' and ' at the beginning and end).
* The # of regression equations you need depends on the # of variables that have arrows pointing to them.  In a simple mediation, there are 3 variables with 2 variables having arrows pointing to them -- need 2 regression equations:
  +  one for the Mediator
  +  one for the DV (Y)
* Operator for a regression analysis is the (tilde, ~)
* DV goes on left
  +  In first equation we regress both the X and M onto Y
  +  In second equation we regress M onto X
* The asterisk (*) is a handy tool to label variables (don't confuse it as defining an interaction); this labeling as a, b, and c_p (in traditional mediation, the total effect is labeled with a and the direct effect is c'[c prime], but the script won't allow and extra single quotation mark, hence c_p) is super helpful in interpreting the ouput
* The indirect effect is created by multiplying the a and b paths.  
* The ":=" sign is used when creating a new variable that is a function of variables in the model, but not in the dataset (i.e., the a and b path).

After specifying the model, we create an object that holds our results from the SEM.  To obtain all the results from our of indirect effects, we also need to print a summary of the fit statistics, standardized estimates, r-squared, and confidence intervals.

*Other authors will write the model code more sensibly, predicting the mediator first, and then the Y variable.  However, I found that by doing it this way, the semPlot produces a more sensible figure.*

Also, because we set a random seed, you should get the same results, but if it differs a little, don't panic.
Also, in Hayes text the direct path from X to Y is c' ("c prime"; where as c is reserved for the total effect of X on Y).  

Let's run the whole model.


```r
set.seed(210410) #reset in case you choose to separate these sections
model <- '
          Y ~ b*M + c_p*X 
          M ~ a*X
          
          indirect :=  a*b
          direct  := c_p
          total_c  := c_p + (a*b)
          '
fit <- sem(model, data = Data, se="bootstrap", missing= 'fiml')
FDsummary <- summary(fit, standardized=T, rsq=T, fit=TRUE, ci=TRUE)
FD_ParamEsts <- parameterEstimates(fit, boot.ci.type = "bca.simple", standardized=TRUE)
FDsummary
```

```
## lavaan 0.6.16 ended normally after 1 iteration
## 
##   Estimator                                         ML
##   Optimization method                           NLMINB
##   Number of model parameters                         7
## 
##   Number of observations                           100
##   Number of missing patterns                         1
## 
## Model Test User Model:
##                                                       
##   Test statistic                                 0.000
##   Degrees of freedom                                 0
## 
## Model Test Baseline Model:
## 
##   Test statistic                                92.701
##   Degrees of freedom                                 3
##   P-value                                        0.000
## 
## User Model versus Baseline Model:
## 
##   Comparative Fit Index (CFI)                    1.000
##   Tucker-Lewis Index (TLI)                       1.000
##                                                       
##   Robust Comparative Fit Index (CFI)             1.000
##   Robust Tucker-Lewis Index (TLI)                1.000
## 
## Loglikelihood and Information Criteria:
## 
##   Loglikelihood user model (H0)               -287.127
##   Loglikelihood unrestricted model (H1)       -287.127
##                                                       
##   Akaike (AIC)                                 588.253
##   Bayesian (BIC)                               606.489
##   Sample-size adjusted Bayesian (SABIC)        584.382
## 
## Root Mean Square Error of Approximation:
## 
##   RMSEA                                          0.000
##   90 Percent confidence interval - lower         0.000
##   90 Percent confidence interval - upper         0.000
##   P-value H_0: RMSEA <= 0.050                       NA
##   P-value H_0: RMSEA >= 0.080                       NA
##                                                       
##   Robust RMSEA                                   0.000
##   90 Percent confidence interval - lower         0.000
##   90 Percent confidence interval - upper         0.000
##   P-value H_0: Robust RMSEA <= 0.050                NA
##   P-value H_0: Robust RMSEA >= 0.080                NA
## 
## Standardized Root Mean Square Residual:
## 
##   SRMR                                           0.000
## 
## Parameter Estimates:
## 
##   Standard errors                            Bootstrap
##   Number of requested bootstrap draws             1000
##   Number of successful bootstrap draws            1000
## 
## Regressions:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##   Y ~                                                                   
##     M          (b)    0.764    0.094    8.162    0.000    0.587    0.949
##     X        (c_p)   -0.209    0.110   -1.896    0.058   -0.420    0.014
##   M ~                                                                   
##     X          (a)    0.693    0.100    6.922    0.000    0.490    0.879
##    Std.lv  Std.all
##                   
##     0.764    0.719
##    -0.209   -0.163
##                   
##     0.693    0.574
## 
## Intercepts:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##    .Y                -0.044    0.109   -0.406    0.685   -0.265    0.158
##    .M                 0.106    0.107    0.995    0.320   -0.110    0.326
##    Std.lv  Std.all
##    -0.044   -0.034
##     0.106    0.086
## 
## Variances:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##    .Y                 1.031    0.151    6.841    0.000    0.732    1.320
##    .M                 1.037    0.141    7.374    0.000    0.747    1.320
##    Std.lv  Std.all
##     1.031    0.590
##     1.037    0.670
## 
## R-Square:
##                    Estimate
##     Y                 0.410
##     M                 0.330
## 
## Defined Parameters:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##     indirect          0.529    0.087    6.102    0.000    0.353    0.710
##     direct           -0.209    0.110   -1.895    0.058   -0.420    0.014
##     total_c           0.321    0.120    2.669    0.008    0.079    0.545
##    Std.lv  Std.all
##     0.529    0.413
##    -0.209   -0.163
##     0.321    0.250
```

```r
FD_ParamEsts
```

```
##         lhs op       rhs    label    est    se      z pvalue ci.lower ci.upper
## 1         Y  ~         M        b  0.764 0.094  8.162  0.000    0.596    0.954
## 2         Y  ~         X      c_p -0.209 0.110 -1.896  0.058   -0.425    0.013
## 3         M  ~         X        a  0.693 0.100  6.922  0.000    0.496    0.885
## 4         Y ~~         Y           1.031 0.151  6.841  0.000    0.772    1.353
## 5         M ~~         M           1.037 0.141  7.374  0.000    0.813    1.381
## 6         X ~~         X           1.063 0.000     NA     NA    1.063    1.063
## 7         Y ~1                    -0.044 0.109 -0.406  0.685   -0.256    0.159
## 8         M ~1                     0.106 0.107  0.995  0.320   -0.103    0.332
## 9         X ~1                    -0.195 0.000     NA     NA   -0.195   -0.195
## 10 indirect :=       a*b indirect  0.529 0.087  6.102  0.000    0.373    0.716
## 11   direct :=       c_p   direct -0.209 0.110 -1.895  0.058   -0.425    0.013
## 12  total_c := c_p+(a*b)  total_c  0.321 0.120  2.669  0.008    0.090    0.560
##    std.lv std.all std.nox
## 1   0.764   0.719   0.719
## 2  -0.209  -0.163  -0.158
## 3   0.693   0.574   0.557
## 4   1.031   0.590   0.590
## 5   1.037   0.670   0.670
## 6   1.063   1.000   1.063
## 7  -0.044  -0.034  -0.034
## 8   0.106   0.086   0.086
## 9  -0.195  -0.189  -0.195
## 10  0.529   0.413   0.401
## 11 -0.209  -0.163  -0.158
## 12  0.321   0.250   0.243
```



### Interpret the Output

Note that in the script we ask (and get) two sets of parameter estimates.  The second set (in the really nice dataframe) includes bootstrapped, bias-corrected confidence intervals. Bias-corrected confidence interals have the advantage of being more powerful and bias-free.  Note, though, that when the CI crosses 0, the effect is NS.  

So let's look at this step-by-step.

*  Overall, our model accounted for  of the variance in the IV and  of the variance in the mediator.
*  a path = 0.693, $p$ = 0.000
*  b path = 0.764, $p$ = 0.000
*  the indirect effect is a product of the a and b paths (0.693 * 0.764 = 0.529); while we don't hand calculate it's significance, we see that it is $p$ = 0.000
*  the direct effect (c', c prime, or c_p) is the isolated effect of X on Y when including M.  We hope this value is LOWER than the total effect because this means that including M shared some of the variance in predicting Y:  c' = -0.209, $p$ = 0.058, and it is no longer signifcant.
*  we also see the total effect; this value is 
   *  identical to the value of simply predicting Y on X (with no M it the model)
   * the value of a(b) + c_p:  0.693(0.764) + -0.209 =  0.321 ($p$ = 0.008)
  

Here's a demonstration that the total effect is, simply, predicting Y from X (also, the correlation between X and Y:


```r
fitXY <- lm(Y ~ X, data = Data)
summary(fitXY)
```

```
## 
## Call:
## lm(formula = Y ~ X, data = Data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.2631 -0.8288  0.0902  0.9637  3.5891 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)  
## (Intercept)   0.0370     0.1315   0.281    0.779  
## X             0.3208     0.1253   2.560    0.012 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.292 on 98 degrees of freedom
## Multiple R-squared:  0.06267,	Adjusted R-squared:  0.05311 
## F-statistic: 6.552 on 1 and 98 DF,  p-value: 0.012
```
Which is the same as the bivariate correlation.  The only trick is that the bivariate correlation produces a standardized result; so it would be the $\beta$.


```r
library(psych)
```

```
## 
## Attaching package: 'psych'
```

```
## The following object is masked from 'package:lavaan':
## 
##     cor2cov
```

```r
XY_r <- corr.test(Data[c("Y", "X")])
XY_r
```

```
## Call:corr.test(x = Data[c("Y", "X")])
## Correlation matrix 
##      Y    X
## Y 1.00 0.25
## X 0.25 1.00
## Sample Size 
## [1] 100
## Probability values (Entries above the diagonal are adjusted for multiple tests.) 
##      Y    X
## Y 0.00 0.01
## X 0.01 0.00
## 
##  To see confidence intervals of the correlations, print with the short=FALSE option
```
### A Table and a Figure

We can use the package [semPlot](https://rdrr.io/cran/semPlot/man/semPaths.html) to create a figure that includes the values on the path.  

Here's what the base package gets us


```r
library(semPlot)
semPaths(fit, #must identify the model you want to map
         what = "est", #"est" plots the estimates, but keeps it greyscale with no fading
         #whatLabels = "stand", #"stand" changes to standardized values
         layout = 'tree', rotation = 2, #together, puts predictors on left, IVs on right 
         edge.label.cex = 1.00, #font size of parameter values
         #edge.color = "black", #overwrites the green/black coloring
         sizeMan=10, #size of squares/observed/"manifest" variables
         fade=FALSE, #if TRUE, there lines are faded such that weaker lines correspond with lower values -- a cool effect, but tough for journals
         esize=2, 
         asize=3,
         #label.prop = .5,
         label.font = 2.5, #controls size (I think) of font for labels
         label.scale = TRUE, #if false, the labels will not scale to fit inside the nodes
         nDigits = 3, #decimal places (default is 2)
         residuals = FALSE,#excludes residuals (and variances) from the path diagram
         nCharNodes = 0, #specifies how many characters to abbreviate variable lables; default is 3.  If 0, uses your entire variable label and adjusts fontsize (which could be a downside)
         intercepts = FALSE, #gets rid of those annoying triangles (intercepts) in the path diagram)
)
title("Fake Data:  Simple Mediation")
```

![](05-SimpleMed_files/figure-docx/FakyDaty Simple Plot-1.png)<!-- -->

Hayes has great examples of APA style tables.  I haven't yet found a package that will turn this output into a journal-ready table, however the *semTable* package can at least write the output to a .csv file and you can further manipulate it into a table.


```r
library(semTable)
fitTab1 <- semTable(fit, columns = c("est", "se", "p", "rsquare"),  columnLabels = c(eststars = "Estimate"), paramSets = c("composites", "loadings", "slopes", "intercepts", "residualvariances"), file = "fitTABLE", type = "csv", print.results = TRUE)
```

### Results

A simple mediation model examined the degree to which M mediated the relation of X on Y.  Using the *lavaan* package (v 0.6-7) in R, coefficients for  each path, the indirect effect, and total effects were calculated. These values are presented in Table 1 and illustrated in Figure 1.  Results suggested that  of the variance in M and  of the variance in Y were accounted for in the model.  The indirect effect ($B$ = 0.529, $p$  = 0.000) was statistically significant; the direct effect ($B$ = -0.209, $p$ = 0.058) was not. Comparing the nonsignificant direct effect to the statistically significant total effect ($B$ = 0.321, $p$ = 0.008) is consistent with the notion that the effect of X on Y is explained through M. 


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
```

```
## 
## Attaching package: 'MASS'
```

```
## The following object is masked from 'package:formattable':
## 
##     area
```

```r
Kim_df <- mvrnorm(n = 156, mu=mu, Sigma = cov_mat, empirical = TRUE)
colMeans(Kim_df)
```

```
## [1] 0.34 3.00 2.98 2.36 3.50 1.64
```

```r
#Checking our work against the original correlation matrix
round(cor(Kim_df),3)
```

```
##       [,1]  [,2]  [,3]  [,4]  [,5]  [,6]
## [1,]  1.00  0.59  0.26  0.34 -0.25 -0.02
## [2,]  0.59  1.00  0.12  0.19 -0.28  0.00
## [3,]  0.26  0.12  1.00  0.66 -0.55  0.07
## [4,]  0.34  0.19  0.66  1.00 -0.66  0.05
## [5,] -0.25 -0.28 -0.55 -0.66  1.00  0.08
## [6,] -0.02  0.00  0.07  0.05  0.08  1.00
```

```r
#renaming the variables
as.data.frame(Kim_df, row.names = NULL, optional = FALSE, make.names = TRUE)
```

```
##              V1        V2        V3         V4       V5        V6
## 1    0.31480075 3.7742194 3.9885237  2.6561280 3.902766 1.2282205
## 2    0.12504376 2.4630397 3.1591179  1.7873208 4.434146 1.4990282
## 3    0.17784660 1.8376841 3.8176890  2.5481372 3.299962 1.3126023
## 4    0.75415493 4.8268167 3.2961255  2.1700517 3.274046 1.4857932
## 5    0.28473851 2.1622754 2.9837665  2.6213814 3.851441 1.4330810
## 6    0.37498180 4.1070853 3.4324724  3.0951215 2.880348 1.9417795
## 7    0.17693663 1.8937966 3.6027771  2.7026733 4.159506 1.5721610
## 8    0.29125231 2.6160275 2.9306768  2.0874299 4.148450 2.4368026
## 9    0.20511465 2.7325373 2.7857356  1.9796662 3.253885 1.8116435
## 10   0.53359317 3.2392905 3.0132959  2.0895764 4.367763 2.4243275
## 11   0.63114162 3.9771384 3.4587058  2.9438475 1.807563 1.2569630
## 12   0.40896721 3.6662876 4.0138230  3.3097403 2.742602 1.7651180
## 13   0.26224312 2.8483570 4.2526175  2.8099626 3.295733 1.9924000
## 14   0.32704221 3.1617904 3.1070588  2.4118363 3.810240 2.3476934
## 15   0.60748029 3.6350970 3.9352681  3.2005377 3.393395 1.7029545
## 16   0.55843862 3.7676612 2.6728071  1.8013062 2.456926 1.8749401
## 17   0.16189032 2.7778703 2.1929777  3.1968281 4.053990 1.4759460
## 18   0.09036787 2.5826597 1.7540157  1.4921435 3.472623 1.5609561
## 19   0.60989051 4.6660979 3.4085289  2.2633219 2.865315 1.3588810
## 20   0.45105455 2.5689387 3.5198695  3.5398776 2.862075 2.1906123
## 21   0.08222908 2.8395169 1.7860559  1.0991829 4.203118 1.5276039
## 22   0.24533439 1.5002846 4.7377924  4.0310332 1.560094 1.7210027
## 23   0.38076827 3.0562669 1.6113009  1.0246868 4.530600 0.9667363
## 24   0.20535295 2.9163293 4.1163801  3.3911153 2.241094 2.5286007
## 25   0.41877232 2.7303151 1.5616356  2.4506453 3.626496 1.3331132
## 26   0.26617565 3.2712843 3.1396021  2.5046344 3.107293 2.6373851
## 27   0.28769261 3.9226886 2.0225615  2.5273085 2.897739 1.4673821
## 28   0.39977557 2.9378013 4.5977776  2.2221782 2.777697 1.5796865
## 29   0.56682075 3.2977619 4.1540235  1.4287847 3.676300 2.6380399
## 30   0.52963158 3.4581461 3.8075875  2.7607609 3.345936 1.3725315
## 31   0.22833829 2.9526622 2.8162920  1.8302324 4.461217 1.6180715
## 32   0.20715467 2.6428462 0.6872940  0.6175158 5.945886 1.2083258
## 33   0.41239292 2.9599932 2.6683891  2.5953392 4.100208 1.7439009
## 34   0.17706607 1.9297199 1.6580187  1.4474162 3.547635 0.9385664
## 35   0.44076050 3.7674252 0.6451316  0.9195607 4.872259 2.9042943
## 36   0.31629873 2.1146493 3.7536880  3.0360594 2.838068 1.0597817
## 37   0.37035488 3.8020496 2.7486592  0.2643106 4.290151 1.7539316
## 38   0.18525000 2.7299318 1.3546404 -0.2523240 4.721493 1.2751736
## 39   0.50115577 3.1349456 3.2421449  3.6172850 2.243026 1.7142033
## 40   0.46397513 3.0130363 1.6878921  2.1082437 3.980620 1.8878141
## 41   0.35589931 3.8035799 2.6925134  2.7952193 2.433082 1.7502752
## 42   0.44021737 2.9537302 2.5509321  2.1958486 4.282946 1.6619111
## 43   0.32847707 2.7533998 2.1885928  2.1955374 4.179124 1.2966225
## 44   0.56599738 3.8086684 2.6614906  1.7883525 3.534684 0.9109111
## 45   0.63298561 5.4722538 2.6246473  2.0323609 4.245740 1.6019083
## 46   0.36671192 3.8373379 1.2029473  1.0808787 4.120857 1.4083544
## 47   0.65026343 4.4611344 3.3155709  3.2096415 3.275165 1.8197487
## 48   0.21525053 1.4784055 0.6087000  1.5537867 3.773753 1.6499578
## 49   0.27368491 1.1111501 1.2805981  1.9063158 4.896829 0.9122461
## 50   0.10696105 1.8148086 3.8605081  2.9060923 3.723161 2.1977074
## 51   0.33966450 3.4810637 3.1089231  3.2156230 2.771850 1.6027278
## 52   0.57280640 3.1872778 3.0340553  2.9384369 3.690575 1.7279573
## 53   0.15508574 2.1099147 2.3821604  2.0299025 3.666993 1.8155390
## 54   0.40500505 2.5808052 3.8734464  2.9063157 3.539234 1.6241724
## 55   0.12820991 2.1598440 3.3058396  2.2974267 3.559505 2.0272936
## 56   0.33136509 3.5787209 0.5054556  1.3450229 3.588936 2.6649975
## 57   0.37281298 1.9809393 2.4679268  2.0532096 2.693133 1.8264299
## 58   0.54951382 2.5396186 1.9478418  1.9405555 3.707789 1.2143895
## 59   0.42571472 3.8397686 3.4512906  2.5756583 2.748384 1.0366664
## 60   0.45118332 2.6936624 4.3733104  2.6169130 2.954446 0.4413846
## 61   0.53225789 2.6442805 4.3567514  2.7931458 3.160311 2.5012683
## 62   0.32424735 3.6281840 3.6375053  1.7813392 3.975108 1.1801099
## 63   0.56918841 3.2878176 2.3571347  1.9000358 3.707492 1.5158290
## 64   0.39689825 2.8722661 3.3891630  3.6161698 4.068442 1.9059061
## 65   0.50916945 3.0716802 3.0138930  3.3409739 3.174682 1.9282731
## 66   0.37381045 2.8613972 2.5346525  2.1169429 4.467001 1.1984569
## 67   0.44510070 3.4257946 3.1816739  2.7318242 3.732238 1.6831477
## 68   0.39299460 3.2010693 3.0060414  2.7628540 3.740873 2.4609643
## 69   0.44653298 3.1644675 3.6843226  3.1614193 3.279748 0.8922136
## 70   0.21899834 3.1967184 3.2764076  2.6424565 2.771732 2.2746857
## 71   0.25699825 4.2762230 3.7504485  2.6255869 3.432270 1.9718086
## 72   0.40767690 3.9634931 1.7851558  2.0298828 4.034073 0.8764307
## 73   0.47161399 3.3279451 5.5579604  3.6499364 2.663229 1.9991762
## 74   0.16250469 3.2773109 2.3015365  2.7213271 2.207900 0.6357077
## 75   0.21824252 1.9657893 3.6326756  2.3072120 3.714804 1.7842104
## 76   0.35899955 4.7632581 2.7294744  1.6372696 3.315482 1.7298316
## 77   0.27873590 3.7828931 3.4194274  2.6702150 3.061570 2.5602023
## 78   0.32614924 2.4435997 3.0606050  1.7580998 4.515989 1.0150848
## 79   0.44731840 2.6447258 2.4664493  3.3599532 3.161522 1.8540898
## 80   0.38029332 2.8780152 2.5960868  2.4639264 4.166040 1.5279508
## 81   0.29347514 2.0532644 2.5949659  2.3422173 4.566912 1.7580519
## 82   0.33449008 1.6763340 4.0745945  2.4576674 1.670886 0.3301944
## 83  -0.13333427 1.8646838 1.8605537  0.8743076 3.859640 1.2873846
## 84   0.35487008 3.1329063 5.1607506  4.4201740 3.263059 1.5195622
## 85   0.58713616 3.5871929 3.5483750  4.3854551 2.030597 0.9455332
## 86   0.45786163 3.1040782 5.0810000  2.4448840 2.710524 1.1693378
## 87   0.11330940 2.7322124 2.9335807  2.6275255 4.450236 2.6072942
## 88   0.44777502 3.9562751 2.8594018  1.2333628 5.032948 2.1101670
## 89   0.20234249 3.5441251 1.4882324  0.3852576 4.367248 0.4775579
## 90   0.34962746 3.0855190 2.6132050  3.0958749 3.614912 2.4456362
## 91   0.35472400 1.3014022 2.6016511  1.1155728 4.948843 1.1118416
## 92   0.23522461 2.2139784 2.9370279  2.1549473 4.138606 1.7861832
## 93   0.42014755 4.1320485 2.7372095  1.4261881 2.536445 0.7664355
## 94   0.15876267 3.2073345 1.5006009  0.2652437 4.404531 1.3220635
## 95  -0.09879649 0.8656131 2.7234710  0.6494040 5.941681 2.7164183
## 96   0.38591913 4.9543775 3.8013379  2.7004684 2.425454 1.7873045
## 97   0.44230619 3.2033360 4.2147040  3.6971513 2.430067 1.4020194
## 98   0.29716688 2.3867630 2.9367032  2.4687254 4.053484 1.4651382
## 99   0.25872796 2.2176660 1.2114582  1.1411757 4.777005 1.5079464
## 100  0.06517719 2.7807617 3.4760948  2.0910964 4.221674 2.1545484
## 101  0.16162849 3.8400098 1.7971752  2.4634779 2.940266 1.0140635
## 102  0.64606063 4.3452532 4.0757378  4.4425493 2.503920 1.4400385
## 103  0.43953190 2.6128498 2.7762497  2.4062207 3.726635 0.2829370
## 104  0.32010790 3.0664919 2.4460013  2.0391555 3.790884 0.8317398
## 105  0.23294638 2.8678142 3.3969138  3.1130449 2.096275 1.6124933
## 106  0.50169163 3.1905757 3.6799971  3.4794417 2.458951 1.5971075
## 107  0.29363188 2.6876792 2.9182203  2.2699264 1.957908 2.5447356
## 108  0.26214714 3.4488353 3.8895770  2.8992699 2.788475 1.6974445
## 109  0.37009802 3.7469694 2.9289129  2.9753900 2.931447 1.8848354
## 110  0.29730729 3.1534935 3.3337636  3.0937892 2.581927 2.3980973
## 111  0.24470529 3.2782295 2.8703885  2.2915569 3.751316 1.5109690
## 112  0.54659869 3.9875404 4.8961430  4.0922097 1.973596 1.2552789
## 113  0.10697366 1.7824763 1.9450107  2.5996601 4.482268 2.2628556
## 114  0.26811871 2.5759431 2.8436596  0.8909314 3.820002 1.0753586
## 115  0.43803765 2.4273121 3.3702456  2.6467107 4.705230 2.7045322
## 116 -0.06773831 1.0890615 2.1039908  1.6805607 3.898468 1.4121238
## 117  0.67609125 3.4169175 3.0278088  2.4529501 3.759270 2.6841150
## 118  0.22489645 3.3639686 4.2235025  3.4781409 2.397229 1.2734251
## 119  0.48300880 2.7120258 3.2456654  2.1793229 3.866140 2.4408081
## 120  0.21667980 3.0603200 0.7108609  1.4644661 5.260412 1.9287701
## 121  0.06512801 1.5886292 2.3749889  0.9180135 4.825579 1.5245304
## 122  0.61003798 3.6223559 2.4047411  2.1696687 2.872875 1.8673206
## 123  0.22977532 3.0189730 2.4342990  2.1243346 3.603260 1.0869989
## 124  0.36422147 2.7425189 3.1556307  1.5394070 4.365395 2.0480712
## 125  0.22933372 2.2046979 2.6570211  2.8655072 2.491654 1.0139864
## 126  0.43238708 4.3102068 1.4277537  1.8567860 3.762495 1.5152676
## 127  0.38990877 2.7218866 2.2477225  1.0646075 4.641716 1.0071908
## 128  0.21753221 1.5882455 1.7407732  1.8213958 3.979175 2.0259607
## 129  0.24771023 1.8672735 2.1881830  1.7507315 4.963161 1.0898960
## 130  0.75408295 4.1289706 3.5474464  3.7791487 2.890496 1.5877356
## 131  0.41644783 2.3299547 3.2208705  2.4181467 4.168489 2.0548755
## 132  0.51769871 4.8743013 3.0157732  3.4737052 3.199802 1.1111757
## 133  0.35717215 2.5981651 2.8779430  2.8980202 3.187424 1.7153314
## 134  0.26920553 3.6847735 3.5523502  2.1903271 2.951610 1.5712546
## 135  0.16434131 1.2258814 3.0293592  1.9656563 3.951977 1.6376233
## 136  0.31628364 2.9034206 1.6436139  1.1249672 4.345822 2.1125579
## 137  0.43040767 3.5183625 3.1331817  1.5718843 3.896956 0.9777469
## 138  0.09448973 1.9683952 2.4498808  1.8165286 3.656073 1.3069823
## 139  0.35002893 3.5852879 2.5912530  1.6491977 5.409283 1.5340238
## 140  0.42296660 3.0640201 3.3574675  2.0421916 3.569581 0.8512156
## 141  0.24932068 3.4213164 2.7485416  3.6280473 2.312726 1.2516653
## 142  0.53062494 3.6640311 5.0907192  4.5694108 1.350203 1.8851816
## 143  0.27962118 3.3932218 2.7264126  1.6711386 3.765364 2.2954985
## 144  0.23322586 3.3279561 3.1592797  1.8154681 1.526867 2.0054852
## 145  0.37962928 2.9616314 3.0021556  2.2003911 3.365035 1.6114163
## 146  0.58980078 3.4742094 4.9949216  3.5702922 1.845780 1.9212095
## 147  0.19270590 2.2602334 2.8233585  0.9669035 4.679490 2.3836889
## 148  0.31012390 2.1745958 4.0843806  3.6022376 3.523946 1.6006189
## 149  0.32504460 3.3473480 2.5347074  2.3469577 3.709119 1.5668096
## 150  0.15226205 2.0648724 5.0295232  2.4532674 2.093509 2.0246498
## 151  0.28115818 1.9376116 3.6657196  2.5134560 2.871831 1.2246409
## 152  0.31936688 3.2432213 4.9907982  2.7616598 4.100608 1.8425462
## 153  0.26239276 3.3908566 2.7704343  2.4932497 2.810725 2.4545378
## 154  0.18193724 3.1099861 2.9947179  3.4509396 2.180071 1.4960772
## 155  0.24134651 2.5263768 2.2951753  1.7940080 3.185113 1.7917425
## 156  0.22502241 2.8972357 4.7873242  4.3623474 1.775025 1.0875423
```

```r
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.2     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.0
## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
## ✔ purrr     1.0.1     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ ggplot2::%+%()   masks psych::%+%()
## ✖ ggplot2::alpha() masks psych::alpha()
## ✖ dplyr::filter()  masks stats::filter()
## ✖ dplyr::lag()     masks stats::lag()
## ✖ dplyr::select()  masks MASS::select()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
Kim_df <- Kim_df%>%
  as.data.frame %>%
  rename(REMS = V1, CMI = V2, ANX = V3, DEP = V4, PWB = V5, HlpSk = V6)
```


```r
#look at the first 6 rows of the new df
head(Kim_df)
```

```
##        REMS      CMI      ANX      DEP      PWB    HlpSk
## 1 0.3148008 3.774219 3.988524 2.656128 3.902766 1.228220
## 2 0.1250438 2.463040 3.159118 1.787321 4.434146 1.499028
## 3 0.1778466 1.837684 3.817689 2.548137 3.299962 1.312602
## 4 0.7541549 4.826817 3.296125 2.170052 3.274046 1.485793
## 5 0.2847385 2.162275 2.983767 2.621381 3.851441 1.433081
## 6 0.3749818 4.107085 3.432472 3.095121 2.880348 1.941779
```


Let's check the descriptives to see if they align with those in the article.

```r
library(psych)
psych::describe(Kim_df)
```

```
##       vars   n mean   sd median trimmed  mad   min  max range  skew kurtosis
## REMS     1 156 0.34 0.16   0.33    0.34 0.16 -0.13 0.75  0.89  0.03     0.16
## CMI      2 156 3.00 0.83   3.06    3.00 0.72  0.87 5.47  4.61  0.01     0.13
## ANX      3 156 2.98 0.99   2.99    2.98 0.83  0.51 5.56  5.05 -0.02     0.12
## DEP      4 156 2.36 0.90   2.41    2.36 0.83 -0.25 4.57  4.82 -0.05     0.10
## PWB      5 156 3.50 0.90   3.61    3.51 0.97  1.35 5.95  4.60 -0.04    -0.23
## HlpSk    6 156 1.64 0.53   1.61    1.63 0.49  0.28 2.90  2.62  0.07    -0.24
##         se
## REMS  0.01
## CMI   0.07
## ANX   0.08
## DEP   0.07
## PWB   0.07
## HlpSk 0.04
```
There are a number of reasons I love the Kim et al. [-@kim_racial_2017] manuscript.  One is that their approach was openly one that tested *alternate models*.  Byrne [-@byrne_structural_2016] credits Joreskog [@bollen_testing_1993] with classifying the researcher's model testing approach in three ways.  If someone is *strictly confirmatory*, they only test the model they proposed and then accept or reject it without further alteration. While this is the tradition of null hypothesis significance testing, it contributes to the "file drawer problem" of unpublished, non-significant, findings.  Additionally, the data are them discarded -- potentially losing valuable resource.  The *alternative models* approach is to propose a handful of competing models before beginning the analysis and then evaluating to see if one model is superior to the other.  The third option is *model generating*.  In this case the researcher begins with a theoretically proposed model.  In the presence of poor fit, the researcher seeks to identify the source of misfit -- respecifying it to best represent the sample data.  The researcher must use caution to produce a model that fits well and is meaningful.

Several of the Kim et al. [-@kim_racial_2017] models were non-significant.  To demonstrate a model that is statistically significant, I will test the hypothesis that racial microaggressions (REMS, the X variable) influence depression (DEP, the Y variable) through cultural mistrust (CMI, the M variable).

![Image of the simple mediation model from Kim et al.](images/SimpleMed/Kim_SimpMed.jpg)

### Specify the Model in *lavaan*

I am a big fan of "copying the model."  In specifying my model I used our simple mediation template above 

* replaced the Y, X, and M with variables names
* replacing the name of the df
* updated the object names (so I could use them in the same .rmd file)


```r
library(lavaan)
set.seed(210410) #reset in case you choose to separate these sections
Kim_model <- '
          PWB ~ b*CMI + c_p*REMS 
          CMI ~a*REMS
          
          indirect :=  a*b
          direct  := c_p
          total_c  := c_p + (a*b)
          '
```


```r
Kim_fit <- sem(Kim_model, data = Kim_df, se="bootstrap", missing= 'fiml')
```


```r
Kim_summary <- summary(Kim_fit, standardized=T, rsq=T, fit=TRUE, ci=TRUE)
Kim_ParamEsts <- parameterEstimates(Kim_fit, boot.ci.type = "bca.simple", standardized=TRUE)
Kim_summary
```

```
## lavaan 0.6.16 ended normally after 1 iteration
## 
##   Estimator                                         ML
##   Optimization method                           NLMINB
##   Number of model parameters                         7
## 
##   Number of observations                           156
##   Number of missing patterns                         1
## 
## Model Test User Model:
##                                                       
##   Test statistic                                 0.000
##   Degrees of freedom                                 0
## 
## Model Test Baseline Model:
## 
##   Test statistic                                81.362
##   Degrees of freedom                                 3
##   P-value                                        0.000
## 
## User Model versus Baseline Model:
## 
##   Comparative Fit Index (CFI)                    1.000
##   Tucker-Lewis Index (TLI)                       1.000
##                                                       
##   Robust Comparative Fit Index (CFI)             1.000
##   Robust Tucker-Lewis Index (TLI)                1.000
## 
## Loglikelihood and Information Criteria:
## 
##   Loglikelihood user model (H0)               -355.521
##   Loglikelihood unrestricted model (H1)       -355.521
##                                                       
##   Akaike (AIC)                                 725.042
##   Bayesian (BIC)                               746.391
##   Sample-size adjusted Bayesian (SABIC)        724.234
## 
## Root Mean Square Error of Approximation:
## 
##   RMSEA                                          0.000
##   90 Percent confidence interval - lower         0.000
##   90 Percent confidence interval - upper         0.000
##   P-value H_0: RMSEA <= 0.050                       NA
##   P-value H_0: RMSEA >= 0.080                       NA
##                                                       
##   Robust RMSEA                                   0.000
##   90 Percent confidence interval - lower         0.000
##   90 Percent confidence interval - upper         0.000
##   P-value H_0: Robust RMSEA <= 0.050                NA
##   P-value H_0: Robust RMSEA >= 0.080                NA
## 
## Standardized Root Mean Square Residual:
## 
##   SRMR                                           0.000
## 
## Parameter Estimates:
## 
##   Standard errors                            Bootstrap
##   Number of requested bootstrap draws             1000
##   Number of successful bootstrap draws            1000
## 
## Regressions:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##   PWB ~                                                                 
##     CMI        (b)   -0.220    0.108   -2.046    0.041   -0.423   -0.006
##     REMS     (c_p)   -0.732    0.513   -1.427    0.154   -1.786    0.270
##   CMI ~                                                                 
##     REMS       (a)    3.061    0.300   10.188    0.000    2.475    3.655
##    Std.lv  Std.all
##                   
##    -0.220   -0.203
##    -0.732   -0.130
##                   
##     3.061    0.590
## 
## Intercepts:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##    .PWB               4.410    0.272   16.220    0.000    3.821    4.910
##    .CMI               1.959    0.118   16.598    0.000    1.717    2.187
##    Std.lv  Std.all
##     4.410    4.916
##     1.959    2.368
## 
## Variances:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##    .PWB               0.733    0.082    8.912    0.000    0.564    0.889
##    .CMI               0.446    0.050    8.979    0.000    0.349    0.549
##    Std.lv  Std.all
##     0.733    0.911
##     0.446    0.652
## 
## R-Square:
##                    Estimate
##     PWB               0.089
##     CMI               0.348
## 
## Defined Parameters:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##     indirect         -0.675    0.330   -2.043    0.041   -1.314   -0.018
##     direct           -0.732    0.513   -1.426    0.154   -1.786    0.270
##     total_c          -1.406    0.421   -3.339    0.001   -2.192   -0.545
##    Std.lv  Std.all
##    -0.675   -0.120
##    -0.732   -0.130
##    -1.406   -0.250
```

```r
Kim_ParamEsts
```

```
##         lhs op       rhs    label    est    se      z pvalue ci.lower ci.upper
## 1       PWB  ~       CMI        b -0.220 0.108 -2.046  0.041   -0.435   -0.013
## 2       PWB  ~      REMS      c_p -0.732 0.513 -1.427  0.154   -1.852    0.202
## 3       CMI  ~      REMS        a  3.061 0.300 10.188  0.000    2.467    3.643
## 4       PWB ~~       PWB           0.733 0.082  8.912  0.000    0.595    0.935
## 5       CMI ~~       CMI           0.446 0.050  8.979  0.000    0.362    0.564
## 6      REMS ~~      REMS           0.025 0.000     NA     NA    0.025    0.025
## 7       PWB ~1                     4.410 0.272 16.220  0.000    3.880    4.934
## 8       CMI ~1                     1.959 0.118 16.598  0.000    1.703    2.185
## 9      REMS ~1                     0.340 0.000     NA     NA    0.340    0.340
## 10 indirect :=       a*b indirect -0.675 0.330 -2.043  0.041   -1.364   -0.077
## 11   direct :=       c_p   direct -0.732 0.513 -1.426  0.154   -1.852    0.202
## 12  total_c := c_p+(a*b)  total_c -1.406 0.421 -3.339  0.001   -2.263   -0.605
##    std.lv std.all std.nox
## 1  -0.220  -0.203  -0.203
## 2  -0.732  -0.130  -0.816
## 3   3.061   0.590   3.699
## 4   0.733   0.911   0.911
## 5   0.446   0.652   0.652
## 6   0.025   1.000   0.025
## 7   4.410   4.916   4.916
## 8   1.959   2.368   2.368
## 9   0.340   2.132   0.340
## 10 -0.675  -0.120  -0.752
## 11 -0.732  -0.130  -0.816
## 12 -1.406  -0.250  -1.568
```




### Interpret the Output

*  Overall, our model accounted for  of the variance in the IV and  of the variance in the mediator.
*  a path = 3.061, $p$ = 0.000
*  b path = -0.220, $p$ = 0.041
*  the indirect effect is a product of the a and b paths (-0.675); while we don't hand calculate it's significance, we see that it is $p$ = 0.041; the bias-corrected bootstrapped confidence intervals can sometimes be more lenient than $p$ values; it is important they don't cross zero.  They don't:  CI95 -1.364 to -0.077  
*  the direct effect (c', c prime, or c_p) is the isolated effect of X on Y when including M.  We hope this value is LOWER than the total effect because this means that including M shared some of the variance in predicting Y:  c' = -0.732, $p$ = 0.154, and it is no longer signifcant.
*  we also see the total effect; this value is 
  + identical to the value of simply predicting Y on X (with no M it the model)
  + the value of a(b) + c_p:  3.061(-0.220) + -0.732 =  -1.406 ($p$ = 0.001)
  
### A Figure and a Table

I make it a practice to immediately plot what I did. Because the plotting packages use our models, this can be a helpful self-check of our work.


```r
library(semPlot)
semPaths(Kim_fit, #must identiy the model you want to map
         what = "est", #"est" plots the estimates, but keeps it greyscale with no fading
         #whatLabels = "stand", #"stand" changes to standardized values
         layout = 'tree', rotation = 2, #together, puts predictors on left, IVs on right 
         edge.label.cex = 1.00, #font size of parameter values
         #edge.color = "black", #overwrites the green/black coloring
         sizeMan=10, #size of squares/observed/"manifest" variables
         fade=FALSE, #if TRUE, there lines are faded such that weaker lines correspond with lower values -- a cool effect, but tough for journals
         esize=2, 
         asize=3,
         #label.prop = .5,
         label.font = 2.5, #controls size (I think) of font for labels
         label.scale = TRUE, #if false, the labels will not scale to fit inside the nodes
         nDigits = 3, #decimal places (default is 2)
         residuals = FALSE,#excludes residuals (and variances) from the path diagram
         nCharNodes = 0, #specifies how many characters to abbreviate variable lables; default is 3.  If 0, uses your entire variable label and adjusts fontsize (which could be a downside)
         intercepts = FALSE, #gets rid of those annoying triangles (intercepts) in the path diagram)
)
title("Depression by Racial Microaggressions via Cultural Mistrust")
```

![](05-SimpleMed_files/figure-docx/semPLOT of PMI-1.png)<!-- -->

The semTable package can be used to write results to an outfile.

```r
library(semTable)
fitTab1 <- semTable(Kim_fit, columns = c("est", "se", "p", "rsquare"),  columnLabels = c(eststars = "Estimate"), paramSets = c("composites", "loadings", "slopes", "intercepts", "residualvariances"), file = "pmi_fitTABLE", type = "csv", print.results = TRUE)
```

For the purpose of the OER, and because it's good trainng, I also think it can be useful to make our own table.  For me, it facilitates my conceptual understanding of (a) what the statistic is doing and (b) the results of our specific data.

Table 1  

|Model Coefficients Assessing Cultural Mistrust as a Mediator Between Racial Microaggressions and Well-Being
|:-------------------------------------------------------------------------------------------------------------------|

|                         
|:------------------------|:---------------------------------------:|:-----:|:--------------------------------------:|
|                         |Cultural Mistrust (M)                    |       |Well-Being (Y)                          |

|
|:----------------|:-----:|:----------:|:------------:|:-----------:|:-----:|:---------:|:------------:|:-----------:|
|Antecedent       |path   |$B$         |$SE$          |$p$          |path   |$B$        |$SE$          |$p$          |
|constant         |$i_{M}$|1.959 |0.118|0.000|$i_{Y}$|4.410|0.272|0.000|
|REMS (X)         |$a$    |3.061      |0.300     |0.000     |$c'$   |-0.732   |0.513   |0.154   |
|CMI (M)          |       |            |              |             |$b$    |-0.220     |0.108     |0.041     |

|
|:------------------------|:---------------------------------------:|:-----:|:--------------------------------------:|
|                         |$R^2$ =                       |       |$R^2$ =                      |

### Results

A simple mediation model examined the degree to which cultural mistrust mediated the relation of racial microaggressions on depressive symptoms  Using the *lavaan* package (v 0.6-7) in R, coefficients for  each path, the indirect effect, and total effects were calculated. These values are presented in Table 1 and illustrated in Figure 1.  Results suggested that  of the variance in cultural mistrust and of the variance in depression were accounted for by the model.  When the mediator was included in the model, bias-corrected confidence intervals surrounding the indirect effect  ($B$ = -0.675, $p$ = 0.041, CI95 -1.364 to -0.077) were not quite statistically significant. Consistent with mediation, the value of the total effect was larger in magnitude and statistically significant ($B$ = -1.406, $p$ = 0.001, CI95 -2.263 to -0.605) than the smaller and non-significant direct effect ($B$ = -0.732, $p$ = 0.154, CI95 -1.852 to 0.202).    

## Considering Covariates

Hayes Chapter 4 [-@hayes_introduction_2018] considers the role of covariates (e.g., other variables that could account for some of the variance in the model).  When previous research (or commonsense, or detractors) suggest you should include them...its worth a try.  If they are non-significant and/or your variables continue to explain variance over-and-above their contribution, then you have gained ground in ruling out plausible rival hypotheses and are adding to causal evidence.

They are relatively easy to specify in *lavaan*.  Just look at to where the arrows point and then write the path!

Let's say we are concerned that anxiety covaries with cultural mistrust and PWB  We'll add it as a covariate to both.

![Image of the simple mediation model from Kim et al.](images/SimpleMed/Kim_wCovs.jpg)


```r
set.seed(210410)
Kim_fit_covs <- '
          PWB ~ b*CMI + c_p*REMS 
          CMI ~a*REMS
          CMI ~ covM*ANX
          PWB ~ covY*ANX

          indirect :=  a*b
          direct  := c_p
          total_c  := c_p + (a*b)
          '
Kim_fit_covs <- sem(Kim_fit_covs, data = Kim_df, se="bootstrap", missing = 'fiml')
Kcov_sum <- summary(Kim_fit_covs, standardized=T, rsq=T, fit=TRUE, ci=TRUE)
Kcov_ParEsts<- parameterEstimates(Kim_fit_covs, boot.ci.type = "bca.simple", standardized=TRUE)
Kcov_sum
```

```
## lavaan 0.6.16 ended normally after 1 iteration
## 
##   Estimator                                         ML
##   Optimization method                           NLMINB
##   Number of model parameters                         9
## 
##   Number of observations                           156
##   Number of missing patterns                         1
## 
## Model Test User Model:
##                                                       
##   Test statistic                                 0.000
##   Degrees of freedom                                 0
## 
## Model Test Baseline Model:
## 
##   Test statistic                               134.067
##   Degrees of freedom                                 5
##   P-value                                        0.000
## 
## User Model versus Baseline Model:
## 
##   Comparative Fit Index (CFI)                    1.000
##   Tucker-Lewis Index (TLI)                       1.000
##                                                       
##   Robust Comparative Fit Index (CFI)             1.000
##   Robust Tucker-Lewis Index (TLI)                1.000
## 
## Loglikelihood and Information Criteria:
## 
##   Loglikelihood user model (H0)               -329.168
##   Loglikelihood unrestricted model (H1)       -329.168
##                                                       
##   Akaike (AIC)                                 676.337
##   Bayesian (BIC)                               703.785
##   Sample-size adjusted Bayesian (SABIC)        675.297
## 
## Root Mean Square Error of Approximation:
## 
##   RMSEA                                          0.000
##   90 Percent confidence interval - lower         0.000
##   90 Percent confidence interval - upper         0.000
##   P-value H_0: RMSEA <= 0.050                       NA
##   P-value H_0: RMSEA >= 0.080                       NA
##                                                       
##   Robust RMSEA                                   0.000
##   90 Percent confidence interval - lower         0.000
##   90 Percent confidence interval - upper         0.000
##   P-value H_0: Robust RMSEA <= 0.050                NA
##   P-value H_0: Robust RMSEA >= 0.080                NA
## 
## Standardized Root Mean Square Residual:
## 
##   SRMR                                           0.000
## 
## Parameter Estimates:
## 
##   Standard errors                            Bootstrap
##   Number of requested bootstrap draws             1000
##   Number of successful bootstrap draws            1000
## 
## Regressions:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##   PWB ~                                                                 
##     CMI        (b)   -0.250    0.092   -2.702    0.007   -0.445   -0.078
##     REMS     (c_p)    0.131    0.479    0.273    0.785   -0.830    1.103
##   CMI ~                                                                 
##     REMS       (a)    3.109    0.318    9.762    0.000    2.483    3.732
##     ANX     (covM)   -0.030    0.058   -0.515    0.607   -0.144    0.083
##   PWB ~                                                                 
##     ANX     (covY)   -0.480    0.064   -7.502    0.000   -0.606   -0.355
##    Std.lv  Std.all
##                   
##    -0.250   -0.230
##     0.131    0.023
##                   
##     3.109    0.599
##    -0.030   -0.036
##                   
##    -0.480   -0.528
## 
## Intercepts:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##    .PWB               5.636    0.277   20.365    0.000    5.091    6.157
##    .CMI               2.032    0.185   10.992    0.000    1.647    2.376
##    Std.lv  Std.all
##     5.636    6.283
##     2.032    2.457
## 
## Variances:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##    .PWB               0.524    0.058    9.086    0.000    0.404    0.631
##    .CMI               0.445    0.050    8.926    0.000    0.345    0.548
##    Std.lv  Std.all
##     0.524    0.651
##     0.445    0.651
## 
## R-Square:
##                    Estimate
##     PWB               0.349
##     CMI               0.349
## 
## Defined Parameters:
##                    Estimate  Std.Err  z-value  P(>|z|) ci.lower ci.upper
##     indirect         -0.776    0.290   -2.680    0.007   -1.376   -0.223
##     direct            0.131    0.479    0.273    0.785   -0.830    1.103
##     total_c          -0.646    0.404   -1.600    0.110   -1.436    0.099
##    Std.lv  Std.all
##    -0.776   -0.138
##     0.131    0.023
##    -0.646   -0.115
```

```r
Kcov_ParEsts
```

```
##         lhs op       rhs    label    est    se      z pvalue ci.lower ci.upper
## 1       PWB  ~       CMI        b -0.250 0.092 -2.702  0.007   -0.456   -0.085
## 2       PWB  ~      REMS      c_p  0.131 0.479  0.273  0.785   -0.863    1.070
## 3       CMI  ~      REMS        a  3.109 0.318  9.762  0.000    2.433    3.718
## 4       CMI  ~       ANX     covM -0.030 0.058 -0.515  0.607   -0.143    0.083
## 5       PWB  ~       ANX     covY -0.480 0.064 -7.502  0.000   -0.601   -0.353
## 6       PWB ~~       PWB           0.524 0.058  9.086  0.000    0.438    0.669
## 7       CMI ~~       CMI           0.445 0.050  8.926  0.000    0.365    0.568
## 8      REMS ~~      REMS           0.025 0.000     NA     NA    0.025    0.025
## 9      REMS ~~       ANX           0.041 0.000     NA     NA    0.041    0.041
## 10      ANX ~~       ANX           0.974 0.000     NA     NA    0.974    0.974
## 11      PWB ~1                     5.636 0.277 20.365  0.000    5.083    6.152
## 12      CMI ~1                     2.032 0.185 10.992  0.000    1.620    2.357
## 13     REMS ~1                     0.340 0.000     NA     NA    0.340    0.340
## 14      ANX ~1                     2.980 0.000     NA     NA    2.980    2.980
## 15 indirect :=       a*b indirect -0.776 0.290 -2.680  0.007   -1.380   -0.226
## 16   direct :=       c_p   direct  0.131 0.479  0.273  0.785   -0.863    1.070
## 17  total_c := c_p+(a*b)  total_c -0.646 0.404 -1.600  0.110   -1.512    0.035
##    std.lv std.all std.nox
## 1  -0.250  -0.230  -0.230
## 2   0.131   0.023   0.146
## 3   3.109   0.599   3.758
## 4  -0.030  -0.036  -0.036
## 5  -0.480  -0.528  -0.535
## 6   0.524   0.651   0.651
## 7   0.445   0.651   0.651
## 8   0.025   1.000   0.025
## 9   0.041   0.260   0.041
## 10  0.974   1.000   0.974
## 11  5.636   6.283   6.283
## 12  2.032   2.457   2.457
## 13  0.340   2.132   0.340
## 14  2.980   3.020   2.980
## 15 -0.776  -0.138  -0.866
## 16  0.131   0.023   0.146
## 17 -0.646  -0.115  -0.720
```



### A Figure and a Table

Let's look at a figure to see see if we did what we think we did. And to also get a graphic representation of our results.  The semplot package does this easily, but the figure is more statistical than conceptual and would require more tinkering for a journal article.



```r
semPaths(Kim_fit_covs, #must identiy the model you want to map
         what = "est", #"est" plots the estimates, but keeps it greyscale with no fading
         #whatLabels = "stand", #"stand" changes to standardized values
         layout = 'tree', rotation = 2, #together, puts predictors on left, IVs on right 
         edge.label.cex = 1.00, #font size of parameter values
         #edge.color = "black", #overwrites the green/black coloring
         sizeMan=10, #size of squares/observed/"manifest" variables
         fade=FALSE, #if TRUE, there lines are faded such that weaker lines correspond with lower values -- a cool effect, but tough for journals
         esize=2, 
         asize=3,
         #label.prop = .5,
         label.font = 2.5, #controls size (I think) of font for labels
         label.scale = TRUE, #if false, the labels will not scale to fit inside the nodes
         nDigits = 3, #decimal places (default is 2)
         residuals = FALSE,#excludes residuals (and variances) from the path diagram
         nCharNodes = 0, #specifies how many characters to abbreviate variable lables; default is 3.  If 0, uses your entire variable label and adjusts fontsize (which could be a downside)
         intercepts = FALSE, #gets rid of those annoying triangles (intercepts) in the path diagram)
)
title("Entrepreneurial Withdrawal by eDistress via Negative Affect (& some covariates(")
```

![](05-SimpleMed_files/figure-docx/semPLOT for model w covs-1.png)<!-- -->

The path coefficients appear to be correct, but this is really a statistical map and doesn't relay the concept of mediation well.

Below is code to create an outfile that could help with creating a table in a word document or spreadsheet. There will be output that is produced with SEM models that won't be relevant for this project.

```r
KimCOVTab <- semTable(Kim_fit_covs, columns = c("est", "se", "p", "rsquare"),  columnLabels = c(eststars = "Estimate"), paramSets = c("composites", "loadings", "slopes", "intercepts", "residualvariances"), file = "ESTRESScov_fitTABLE", type = "csv", print.results = TRUE)
```


Table 2  

|Model Coefficients Assessing Cultural Mistrust as a Mediator Between Racial Microaggressions and Well-Being
|:---------------------------------------------------------------------------------------------------------------------------------|

|                         
|:------------------------|:---------------------------------------:|:-----:|:----------------------------------------------------:|
|                         |Cultural Mistrust (M)                    |       |Well-Being (Y)                                        |

|
|:----------------|:-----:|:------------:|:--------------:|:---------------:|:-----:|:-----------:|:--------------:|:-------------:|
|Antecedent       |path   |$B$           |$SE$            |$p$              |path   |$B$          |$SE$            |$p$            |
|constant         |$i_{M}$|2.032 |0.185|0.000  |$i_{Y}$|5.636|0.277|0.000|
|REMS (X)         |$a$    |3.109      |0.318      |0.000       |$c'$   |0.131   |0.479      |0.785   |
|CMI (M)          |       |              |                |                 |$b$    |-0.250     |0.092     |0.007     |
|ANX (Cov)        |       |-0.030     |0.058      |0.607       |       |-0.480    |0.064      |0.000     |

|
|:------------------------|:-----------------------------------------------:|:-----:|:--------------------------------------------:|
|                         |$R^2$ =                             |       |$R^2$ =                          |                    


### APA Style Write-up

There are varying models for reporting the results of mediation.  The Kim et al. [@kim_racial_2017] writeup is a great example.  Rather than copying it directly, I have modeled my table after the ones in Hayes [-@hayes_introduction_2018] text. You'll notice that information in the table and text are minimally overlapping.  APA style cautions us against redundancy in text and table.

**Results**

A simple mediation model examined the degree to which cultural mistrust mediated the effect of racial microaggressions on psychological well-being.  Using the *lavaan* package (v 0.6-7) in R, coefficients for the each path, the indirect effect, and total effects were calculated. The effect of covariate, anxiety, was mapped onto both the mediator and dependent variable.  These values are presented in Table 3 and illustrated in Figure 3.  Results suggested that  of the variance in cultural mistrust and  of the variance in well-being were accounted for by the model.  Supporting the notion of a mediated model, there was a statistically significant indirect effect ($B$ = -0.776, $p$ = 0.007, CI95 -1.380 to -0.226) in combination with a  non-significant direct effect ($B$ = 0.131, $p$ = 0.785, CI95 -0.863 to	1.070). Curiously, though, the total effect ($B$ = -0.646, $p$ = 0.110, CI95 -1.512	to 0.035) was also non-significant.


## Residual and Related Questions...

..that you might have; or at least I had, but if had answered them earlier it would have disrupt the flow.

1. Are you sure you can claim a significant indirect effect in the presence of a non-significant total effect?  Hayes [-@hayes_introduction_2018] is.  
   * In the section subtitled, "What about Baron & Kenny" (chapter 4), Hayes argues from both logical/philosophical and statistical perspectives that the size of the total effect does not constrain or determine the size of the indirect effect.  That is, an indirect effect can be different from zero even when the total effect is not (pp. 117-119).    
2. The output we get is different from the output in the journal article being used as the research vignette.  Why?  And should we worry about it? 
   * We are simulating data.  This gives us some advantages in that (unless we specify it), we never have missingness and our variables should be normally distributed. Because we are working from means, standard deviations, and correlations, our data will never be the same as the original researcher.  That said, we can compare our results to the journal to *check out work.*  In fact, in this very chapter, I got turned around (e.g., first accidentally swapping the mediator and IV; then using the wrong DV) and was able to compare my work against the journal article to correct my errors. 
3. Some of the statistics you are reporting are different than the ones in Hayes and the ones that use the PROCESS macro (e.g., what happened to the *F* test)?
   *  The default estimator for *lavaan* is maximum likelihood (ML) and Hayes uses ordinary least squares (OLS).  This affects both the values of coefficients, standard errors, AND the type of statistics that are reported. 
   *  You can ask for OLS regression by adding the statement "estimator = "GLS". Even with this option, I have not discovered a way to obtain the *F* tests for the overall model.  Researchers seem to be comfortable with this, even asking for less than we did (e.g., many do not request R square).
   * Best I can tell, researchers who do want this might use a combination of packages, using GLS estimators in *lavaan* (this easily gets them the bootstrapped CIs) and the move to a different regression package to get the intercepts and *F* tests.  If I did this I would triple check to make sure that all the output really lined up.
4. Why did we ignore the traditional fit statistics associated with structural equation modeling (e.g., CFI, RMSEA). 
   * I hesitate to do this with models that do not include latent variables.  Therefore, we asked for an "in-between" amount of info that should be sufficient for publication submission (any editor may have their own preferences and ask for more).
5. What if I have missing data?
   *  When we enter the *lavaan* world we do get options other than multiple imputation.  In today's example we used the "sem" fitting function. Unless otherwise specified, listwise deletion (deleting the entire case when one of its variables is used to estimate the model) is the default in *lavaan*.  If data are MCAR or MAR, you can add the argument *missing = "ml"* (or its alias *missing = "fiml"*).  More here https://users.ugent.be/~yrosseel/lavaan/lavaan2.pdf on the 1.7/Missing data in lavaan slide.
   * That said, the type of estimator matters.  If you estimate your data with GLS (generalized least squares) or WLS (weighted least squares), you are required to have complete data (however you got it).  We used maximum likelihood and, even though we had non-missing data, I used the *missing = "fiml"* code.

   
## Practice Problems

The three problems described below are designed to grow with the subsequent chapters on complex mediation and conditional process analysis (i.e,. moderated mediation). Therefore, I recommend that you select a dataset that includes at least four variables. If you are new to this topic, you may wish to select variables that are all continuously scaled.  The IV and moderator (subsequent chapters) could be categorical (if they are dichotomous, please use 0/1 coding; if they have more than one category it is best if they are ordered).  You will likely encounter challenges that were not covered in this chapter. Search for and try out solutions, knowing that there are multiple paths through the analysis.

The suggested practice problem for this chapter is to conduct a simple mediation.

### Problem #1: Rework the research vignette as demonstrated, but change the random seed

If this topic feels a bit overwhelming, simply change the random seed in the data simulation, then rework the problem. This should provide minor changes to the data (maybe in the second or third decimal point), but the results will likely be very similar.

|Assignment Component  
|:---------------------------------------------------------------------------------------------|:-------------: |:------------:|
|1. Assign each variable to the X, Y, or M roles (ok but not required  to include a cov)       |      5         |    _____     |      
|2. Specify and run the lavaan model                                                           |      5         |    _____     |
|3. Use semPlot to create a figure                                                             |      5         |    _____     |
|4. Create a table that includes regression output for the M and Y variables                   |      5         |    _____     |  
|5. Represent your work in an APA-style write-up                                               |      5         |    _____     |          
|6. Explanation to grader                                                                      |      5         |    _____     |   
|7. Be able to hand-calculate the indirect, direct, and total effects from the a, b, & c' paths|      5         |    _____     |
|**Totals**                                                                                    |      35        |    _____     |

### Problem #2:  Rework the research vignette, but swap one or more variables

Use the simulated data, but select one of the other models that was evaluated in the Kim et al. [-@kim_racial_2017] study.  Compare your results to those reported in the mansucript.


|Assignment Component  
|:---------------------------------------------------------------------------------------------|:-------------: |:------------:|
|1. Assign each variable to the X, Y, or M roles (ok but not required  to include a cov)       |      5         |    _____     |      
|2. Specify and run the lavaan model                                                           |      5         |    _____     |
|3. Use semPlot to create a figure                                                             |      5         |    _____     |
|4. Create a table that includes regression output for the M and Y variables                   |      5         |    _____     |  
|5. Represent your work in an APA-style write-up                                               |      5         |    _____     |          
|6. Explanation to grader                                                                      |      5         |    _____     |   
|7. Be able to hand-calculate the indirect, direct, and total effects from the a, b, & c' paths|      5         |    _____     |
|**Totals**                                                                                    |      35        |    _____     |
             
                                                                 

### Problem #3:  Use other data that is available to you

Using data for which you have permission and access (e.g.,  IRB approved data you have collected or from your lab; data you simulate from a published article; data from an open science repository; data from other chapters in this OER), complete a simple mediation.

|Assignment Component  
|:---------------------------------------------------------------------------------------------|:-------------: |:------------:|
|1. Assign each variable to the X, Y, or M roles (ok but not required  to include a cov)       |      5         |    _____     |      
|2. Specify and run the lavaan model                                                           |      5         |    _____     |
|3. Use semPlot to create a figure                                                             |      5         |    _____     |
|4. Create a table that includes regression output for the M and Y variables                   |      5         |    _____     |  
|5. Represent your work in an APA-style write-up                                               |      5         |    _____     |          
|6. Explanation to grader                                                                      |      5         |    _____     |   
|7. Be able to hand-calculate the indirect, direct, and total effects from the a, b, & c' paths|      5         |    _____     |
|**Totals**                                                                                    |      35        |    _____     |
   




