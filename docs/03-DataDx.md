
# Data Dx {#DataDx}

 [Screencasted Lecture Link](https://spu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?pid=43dbe818-8186-498d-8e84-acf7000acb5b) 

The focus of this chapter is *data diagnostics*. We are asking the question, "Does the data have the appropriate characteristics for the analysis we want to perform?" Some statistics are more robust than others to violations of the assumptions about the characteristics of the data. None-the-less, we must report these characteristics when we disseminate the results.

## Navigating this Lesson

There is about 45 minutes of lecture.  If you work through the materials with me it would be plan for an additional hour.

While the majority of R objects and data you will need are created within the R script that sources the chapter, there are a few that cannot be created from within the R framework. Additionally, sometimes links fail.  All original materials are provided at the [Github site](https://github.com/lhbikos/ReC_MultivModel) that hosts the book. More detailed guidelines for ways to access all these materials are provided in the OER's [introduction](#ReCintro)

### Learning Objectives

Learning objectives from this lecture include the following:

* Conduct and interpret critical data diagnostics, including
  * alpha coefficients
  * skew
  * kurtosis
* Assess univariate and multivariate normality
* Identify options for managing outliers and skewed data
* Articulate a workflow for data preparation, including scrubbing, scoring, and data diagnostics


### Planning for Practice

The suggestions from practice are a continuation from the two prior chapters. If you have completed one or more of those assignments, you should have started with a raw dataset and then scrubbed and scored it. This chapter will involve running basic data diagnostics. Options of graded complexity could incude:

* Repeating the steps in the chapter with the most recent data from the Rate-A-Recent-Course survey; differences will be in the number of people who have completed the survey since the chapter was written.
* Use the dataset that is the source of the chapter, but score a different set of items that you choose.
* Begin with raw data to which you have access. 

### Readings & Resources

In preparing this chapter, I drew heavily from the following resource(s). Other resources are cited (when possible, linked) in the text with complete citations in the reference list.

* Parent, M. C. (2013). Handling item-level missing data: Simpler is just as good. The Counseling Psychologist, 41(4), 568–600. https://doi.org/10.1177/0011000012445176
  - The purpose of Parent's article was to argue that complex and resource-intensive procedurs like multiple imputation are unnecessary. Following a simulation that supports his claims, Parent provides some guidelines to follow for the AIA approach. 

* Kline, R. B. (2015). Data preparation and psychometrics review. In Principles and Practice of Structural Equation Modeling, Fourth Edition. Guilford Publications. http://ebookcentral.proquest.com/lib/spu/detail.action?docID=4000663
  - Kline's chapter is my "go-to" for making decisions about preparing data for analysis.

### Packages

The packages used in this lesson are embedded in this code. When the hashtags are removed, the script below will (a) check to see if the following packages are installed on your computer and, if not (b) install them.


```r
if(!require(tidyverse)){install.packages("tidyverse")} #this includes dplyr
if(!require(psych)){install.packages("psych")}
if(!require(apaTables)){install.packages("apaTables")}
```


## Workflow for Preliminary Data Diagnostics

The same workflow guides us through the Scrubbing, Scoring, and Data Dx chapters.  At this stage we have 

* imported our raw data from Qualtrics, 
* scrubbed the data by applying our inclusion and exclusion criteria, and
* used Parent's available information approach [AIA; -@parent_handling_2013] for determining the acceptable amount of missingness for each scale, and
* prepared variables and scored them.

We are now ready to engage in data diagnostics for the statistical model we will test.

![An image of our stage in the workflow for scrubbing and scoring data.](images/Ch04/wrkflow_dx.jpg)

## Research Vignette

The research vignette comes from the survey titled, [Rate-a-Recent-Course:  A ReCentering Psych Stats Exercise](https://spupsych.az1.qualtrics.com/jfe/form/SV_b2cClqAlLGQ6nLU) and is explained in the [scrubbing chapter](#scrub). In the [scoring chapter](#score) we prepared four variables for analysis. Details for these are in our [codebook](./Rate-a-Course_Codebook.pdf).

Variable recap:

* Perceived Campus Climate for Black Students includes 6 items, one of which was reverse scored. This scale was adapted from Szymanski et al.'s [-@szymanski_perceptions_2020] Campus Climate for LGBTQ students. It has not been evaluated for use with other groups.  The Szymanski et al. analysis suggested that it could be used as a total scale score, or divided into three items each that assess
  * College response to LGBTQ students (items 6, 4, 1)
  * LGBTQ stigma (items 3, 2, 5)
* Sense of Belonging includes 3 items. This is a subscale from Bollen and Hoyle's [-@bollen_perceived_1990] Perceived Cohesion Scale. There are no items on this scale that require reversing.
* Percent of Black classmates is a single item that asked respondents to estimate the proportion of students in various racial categories
* Percent of BIPOC instructional staff, similarly, asked respondents to identify the racial category of each member of their instructional staff

As we noted in the [scrubbing chapter](#scrub), our design has notable limitations. Briefly, (a) owing to the open source aspect of the data we do not ask about the demographic characteristics of the respondent; (b) the items that ask respondents to *guess* the identities of the instructional staff and to place them in broad categories, (c) we do not provide a "write-in" a response. We made these decisions after extensive conversation with stakeholders. The primary reason for these decisions was to prevent potential harm (a) to respondents who could be identified if/when the revealed private information in this open-source survey, and (b) trolls who would write inappropriate or harmful comments. 
  
As I think about "how these variables go together" (which is often where I start in planning a study), imagine a parallel mediation.  That is the perception of campus climate for Black students would be predicted by the respondent's sense of belonging, mediated in separate paths through the proportion of classmates who are Black and the proportion of BIPOC instructional staff.  

*I would like to assess the model by having the instructional staff variable to be the %Black instructional staff.  At the time that this lecture is being prepared, there is not sufficient Black representation in the staff to model this.* 

![An image of the statistical model for which we are preparing data.](images/Ch04/BlStuMed.jpg)

I will finish up this chapter by conducting a regression. Because parallel mediation can be complicated (I teach it in a later chapter), I will demonstrate use of our prepared variables with a simple multiple regression.

![An image of the statistical model for which we are preparing data.](images/Ch04/BlStuRegression.jpg)


First, though, let's take a more conceptual look at issues regarding missing data.  We'll come back to details of the survey as we work with it.

## Internal Consistency of Scales/Subscales

Alpha coefficients are *reliability coefficients* that assess the *internal consistency* of an instrument. It asks, "For each person, are responses *consistently* high, or medium, or low?"  To the degree that they are (meaning there are high inter-item correlations), the internal consistency coefficient will be high.  We want values >.80.  There are numerous problems with alpha coefficients.  The biggest one is that they are influenced by sample size -- longer scales have higher alpha coefficients [@cortina_what_1993].  Fourteen seems to be a magic number where we begin to not trust the high alpha coefficient. I address this more thoroughly -- offering an alternative -- in psychometrics. While there is much criticism about the usefulness of the alpha coefficient [@sijtsma_use_2009], researchers continue to use the alpha coefficient as an indicator of the internal consistency of scales that consist of multiple items and contain several variables.  

We need item level data to compute an alpha coefficient. The easiest way to get an alpha coefficient is to feed the *alpha()* function (*psych* package) a contatonated list of items (with any items already reverse-scored). There should be no extra items. In the [scoring chapter](#score) we already reverse-coded the single item in the campus climate scale, so we are ready to calculate alphas. 

The df from which I am pulling data was created and written as an outfile in the [scoring chapter](#score). You may also download the file from the [Github site](https://github.com/lhbikos/ReC_MultivModel) that hosts the chapter. Be sure to place the file in the same folder as the .rmd file. This particular df has item-level data. I am working with the .rds file. In case this is problematic for you, I have also provided code to import a .csv version of the file. 


```r
item_scores_df <- readRDS("BlStItmsScrs230902.rds")
#item_scores_df <- read.csv("BlStItmsScrs230902.csv", header = TRUE)
```

From this, we create tiny dfs, one for each of the alpha coefficients we need to create. A priori, we are planning to use all six items of the campus climate scale. We'll go ahead and also calculate the subscales because (a) it's good practice and (b) if the alpha is low, a *reason* might show up in one of the subscales.



```r
#alpha for the belonging scale
psych::alpha(item_scores_df[c("Belong_1", "Belong_2", "Belong_3")])
```

```
## 
## Reliability analysis   
## Call: psych::alpha(x = item_scores_df[c("Belong_1", "Belong_2", "Belong_3")])
## 
##   raw_alpha std.alpha G6(smc) average_r S/N    ase mean  sd median_r
##       0.95      0.95    0.93      0.87  21 0.0099    4 1.5     0.88
## 
##     95% confidence boundaries 
##          lower alpha upper
## Feldt     0.93  0.95  0.97
## Duhachek  0.93  0.95  0.97
## 
##  Reliability if an item is dropped:
##          raw_alpha std.alpha G6(smc) average_r S/N alpha se var.r med.r
## Belong_1      0.94      0.94    0.88      0.88  15    0.016    NA  0.88
## Belong_2      0.92      0.92    0.85      0.85  11    0.020    NA  0.85
## Belong_3      0.94      0.94    0.89      0.89  16    0.015    NA  0.89
## 
##  Item statistics 
##           n raw.r std.r r.cor r.drop mean  sd
## Belong_1 64  0.95  0.95  0.92   0.90  4.1 1.5
## Belong_2 65  0.96  0.96  0.94   0.92  4.1 1.6
## Belong_3 64  0.95  0.95  0.91   0.89  3.8 1.5
## 
## Non missing response frequency for each item
##             1    2    3    4    5    6    7 miss
## Belong_1 0.02 0.14 0.23 0.17 0.22 0.17 0.05 0.03
## Belong_2 0.03 0.14 0.22 0.22 0.15 0.20 0.05 0.02
## Belong_3 0.05 0.19 0.19 0.23 0.20 0.09 0.05 0.03
```
For each scale I will capture a statement for the APA style write-up. Because these values are typically reported with each measure (and not in the prliminary results), I won't create a cumulative write-up.

>>Cronbach's alpha for the belonging scale was 0.95.


```r
#alpha for the campus climate for Black students scale
psych::alpha(item_scores_df[c("rBlst_1", "Blst_2", "Blst_3", "Blst_4", "Blst_5", "Blst_6")])
```

```
## 
## Reliability analysis   
## Call: psych::alpha(x = item_scores_df[c("rBlst_1", "Blst_2", "Blst_3", 
##     "Blst_4", "Blst_5", "Blst_6")])
## 
##   raw_alpha std.alpha G6(smc) average_r S/N  ase mean  sd median_r
##       0.85      0.87    0.87      0.52 6.5 0.03  2.5 1.1     0.52
## 
##     95% confidence boundaries 
##          lower alpha upper
## Feldt     0.78  0.85  0.90
## Duhachek  0.79  0.85  0.91
## 
##  Reliability if an item is dropped:
##         raw_alpha std.alpha G6(smc) average_r S/N alpha se var.r med.r
## rBlst_1      0.85      0.87    0.87      0.57 6.5    0.031 0.029  0.57
## Blst_2       0.87      0.88    0.87      0.59 7.1    0.026 0.019  0.56
## Blst_3       0.83      0.85    0.85      0.54 5.8    0.034 0.029  0.50
## Blst_4       0.80      0.82    0.82      0.48 4.6    0.041 0.027  0.48
## Blst_5       0.79      0.81    0.81      0.46 4.3    0.042 0.024  0.47
## Blst_6       0.80      0.82    0.81      0.48 4.6    0.040 0.021  0.50
## 
##  Item statistics 
##          n raw.r std.r r.cor r.drop mean  sd
## rBlst_1 60  0.69  0.67  0.56   0.52  3.4 1.6
## Blst_2  64  0.68  0.62  0.51   0.46  3.0 1.8
## Blst_3  63  0.71  0.74  0.66   0.59  2.0 1.2
## Blst_4  62  0.85  0.86  0.84   0.77  2.5 1.3
## Blst_5  63  0.89  0.89  0.89   0.82  2.0 1.2
## Blst_6  63  0.83  0.86  0.86   0.77  2.1 1.3
## 
## Non missing response frequency for each item
##            1    2    3    4    5    6    7 miss
## rBlst_1 0.10 0.23 0.20 0.25 0.08 0.10 0.03 0.09
## Blst_2  0.33 0.16 0.09 0.17 0.16 0.06 0.03 0.03
## Blst_3  0.44 0.33 0.06 0.11 0.03 0.02 0.00 0.05
## Blst_4  0.27 0.34 0.15 0.18 0.05 0.00 0.02 0.06
## Blst_5  0.46 0.30 0.05 0.14 0.05 0.00 0.00 0.05
## Blst_6  0.38 0.35 0.11 0.08 0.06 0.02 0.00 0.05
```
>>Cronbach's alpha for the campus climate scale was 0.87. 

Since this value is $\geq$ .80, it is within the realm of acceptability. Let's go ahead, though, and examine its subscales.


```r
#alpha for the stigma scale of the campus climate for Black students scale
psych::alpha(item_scores_df[c("Blst_3", "Blst_2", "Blst_5")])
```

```
## 
## Reliability analysis   
## Call: psych::alpha(x = item_scores_df[c("Blst_3", "Blst_2", "Blst_5")])
## 
##   raw_alpha std.alpha G6(smc) average_r S/N   ase mean  sd median_r
##       0.69      0.73    0.69      0.47 2.7 0.065  2.3 1.2     0.54
## 
##     95% confidence boundaries 
##          lower alpha upper
## Feldt     0.54  0.69  0.80
## Duhachek  0.57  0.69  0.82
## 
##  Reliability if an item is dropped:
##        raw_alpha std.alpha G6(smc) average_r  S/N alpha se var.r med.r
## Blst_3      0.67      0.70    0.54      0.54 2.35    0.074    NA  0.54
## Blst_2      0.75      0.75    0.60      0.60 3.03    0.061    NA  0.60
## Blst_5      0.41      0.43    0.28      0.28 0.76    0.135    NA  0.28
## 
##  Item statistics 
##         n raw.r std.r r.cor r.drop mean  sd
## Blst_3 63  0.72  0.78  0.62   0.46    2 1.2
## Blst_2 64  0.82  0.75  0.55   0.46    3 1.8
## Blst_5 63  0.87  0.89  0.83   0.70    2 1.2
## 
## Non missing response frequency for each item
##           1    2    3    4    5    6    7 miss
## Blst_3 0.44 0.33 0.06 0.11 0.03 0.02 0.00 0.05
## Blst_2 0.33 0.16 0.09 0.17 0.16 0.06 0.03 0.03
## Blst_5 0.46 0.30 0.05 0.14 0.05 0.00 0.00 0.05
```
>>Cronbach's alpha for the campus climate stigma subscale was 0.73. 


```r
#alpha for the campus responsiveness scale of the campus climate for Black students scale
psych::alpha(item_scores_df[c("rBlst_1", "Blst_4", "Blst_6")])
```

```
## 
## Reliability analysis   
## Call: psych::alpha(x = item_scores_df[c("rBlst_1", "Blst_4", "Blst_6")])
## 
##   raw_alpha std.alpha G6(smc) average_r S/N   ase mean  sd median_r
##       0.79      0.81    0.76      0.58 4.2 0.045  2.7 1.2     0.52
## 
##     95% confidence boundaries 
##          lower alpha upper
## Feldt     0.69  0.79  0.87
## Duhachek  0.71  0.79  0.88
## 
##  Reliability if an item is dropped:
##         raw_alpha std.alpha G6(smc) average_r S/N alpha se var.r med.r
## rBlst_1      0.86      0.86    0.75      0.75 6.0    0.035    NA  0.75
## Blst_4       0.64      0.65    0.48      0.48 1.8    0.087    NA  0.48
## Blst_6       0.68      0.68    0.52      0.52 2.1    0.078    NA  0.52
## 
##  Item statistics 
##          n raw.r std.r r.cor r.drop mean  sd
## rBlst_1 60  0.81  0.78  0.58   0.53  3.4 1.6
## Blst_4  62  0.88  0.89  0.84   0.72  2.5 1.3
## Blst_6  63  0.85  0.87  0.81   0.69  2.1 1.3
## 
## Non missing response frequency for each item
##            1    2    3    4    5    6    7 miss
## rBlst_1 0.10 0.23 0.20 0.25 0.08 0.10 0.03 0.09
## Blst_4  0.27 0.34 0.15 0.18 0.05 0.00 0.02 0.06
## Blst_6  0.38 0.35 0.11 0.08 0.06 0.02 0.00 0.05
```

>>Cronbach's alpha for the campus climate responsiveness subscale was 0.80.  Between the two subscales, it looks as if the responsivenes subscale is more internally consistent. 

## Distributional Characteristics of the Variables

### Evaluating Univariate Normality

Statistics like ANOVA and regression each have a set of assumptions about the distributional characteristics of the data. In most of the chapters in this OER we review those assumptions and how to evaluate them.  Common across many statistics is the requirement of univariate and multivariate normality.  Let's take a look at the variables we will use in our analysis and assess those.

We can continue to work from the df we uploaded at the beginning of the chapter to do this work. Let's take a quick peek. This df has the item-level data (we used it for the alpha coefficients); the scale and subscale scores; and the two items that assess proportion of instructional staff that are BIPOC and proportion of classmates that are BIPOC.

The *str()* function let's us look at the variable format/measurement level of each variable. 

```r
str(item_scores_df)
```

```
## 'data.frame':	66 obs. of  17 variables:
##  $ ID        : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ iBIPOC_pr : num  0.333 0 0.5 0.333 1 ...
##  $ cmBlack   : int  0 5 10 6 5 20 0 0 0 4 ...
##  $ Belong_1  : int  6 4 NA 5 4 5 6 7 6 3 ...
##  $ Belong_2  : int  6 4 3 3 4 6 6 7 6 3 ...
##  $ Belong_3  : int  7 6 NA 2 4 5 5 7 6 3 ...
##  $ Blst_1    : int  5 6 NA 2 6 5 5 5 5 3 ...
##  $ Blst_2    : int  3 6 5 2 1 1 4 4 3 5 ...
##  $ Blst_3    : int  5 2 2 2 1 1 4 3 1 2 ...
##  $ Blst_4    : int  2 2 2 2 1 2 4 3 2 3 ...
##  $ Blst_5    : int  2 4 NA 2 1 1 4 4 1 3 ...
##  $ Blst_6    : int  2 1 2 2 1 2 4 3 2 3 ...
##  $ rBlst_1   : num  3 2 NA 6 2 3 3 3 3 5 ...
##  $ Belonging : num  6.33 4.67 NA 3.33 4 5.33 5.67 7 6 3 ...
##  $ ResponseBL: num  2.33 1.67 2 3.33 1.33 2.33 3.67 3 2.33 3.67 ...
##  $ StigmaBL  : num  3.33 4 3.5 2 1 1 4 3.67 1.67 3.33 ...
##  $ ClimateBL : num  2.83 2.83 NA 2.67 1.17 1.67 3.83 3.33 2 3.5 ...
```
The difference between "int" (integer) and "num" (numerical) is that integers are limited to whole numbers. For the statistics used in this lesson, both are acceptable formats for the variables.


```r
#the script may look a little complicated; I could have simply written:
#describe(item_scores_df) 
#because I only wanted only a few variables, I provided them in a concatenated: list [c("iBIPOC_pr", "cmBlack", "Belonging", "ClimateBL")]
#I used type =1 so that we can interpret skew and kurtosis along Kline's recommendations
#I created an object from the descriptive results, this can be used to export the results for easier table making or manipulation outside of R

descriptives <- psych::describe(item_scores_df[c("iBIPOC_pr", "cmBlack", "Belonging", "ClimateBL")], type = 1)
#When we capture results in an object, we need to write it below so the results will display
descriptives
```

```
##           vars  n mean   sd median trimmed  mad min   max range skew kurtosis
## iBIPOC_pr    1 64 0.35 0.39   0.25    0.32 0.37   0  1.00  1.00 0.64    -1.05
## cmBlack      2 66 8.20 8.02   5.50    7.24 8.15   0 30.00 30.00 0.95     0.05
## Belonging    3 64 4.03 1.47   4.00    4.03 1.48   1  7.00  6.00 0.03    -0.76
## ClimateBL    4 61 2.48 1.09   2.33    2.41 0.99   1  5.67  4.67 0.56     0.04
##             se
## iBIPOC_pr 0.05
## cmBlack   0.99
## Belonging 0.18
## ClimateBL 0.14
```

```r
#this can be useful if you wish to manually format the data for an APA style table
write.csv (descriptives, file="DataDx_descripts.csv") 
```

Skew and kurtosis are one way to evaluate whether or not data are normally distributed. When we use the "type=1" argument, the skew and kurtosis indices in the *psych* package can be interpreted according to Kline's [-@kline_data_2016] guidelines. Regarding skew, values greater than the absolute value of 3.0 are generally considered "severely skewed." Regarding kurtosis, "severely kurtotic" is argued to be anywhere greater 8 to 20. Kline recommended using a conservative threshold of the absolute value of 10. The skew and kurtosis values for our variables fall well below these thessholds.

We can also apply the Shapiro-Wilk test of normality to each of our variables. When the $p$ value is < .05, the variable's distribution is deviates from a normal distribution to a degree that is statistically significant. Below, the plotting of the histogram with a normal curve superimposed shows how the distribution approximates one that is normal.


```r
#The shapiro-test is in base R; it's specification is simple:  shapiro.test(df$variable)
#I added the object (and had to list it below) so I can use the inline text function
shapiro.test(item_scores_df$cmBlack)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  item_scores_df$cmBlack
## W = 0.87796, p-value = 9.899e-06
```

```r
shapiro.test(item_scores_df$iBIPOC_pr)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  item_scores_df$iBIPOC_pr
## W = 0.78725, p-value = 3.181e-08
```

```r
shapiro.test(item_scores_df$Belonging)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  item_scores_df$Belonging
## W = 0.97262, p-value = 0.1654
```

```r
shapiro.test(item_scores_df$ClimateBL)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  item_scores_df$ClimateBL
## W = 0.95102, p-value = 0.01613
```
CUMULATIVE CAPTURE FOR THE APA STYLE WRITE-UP:  

>>Regarding the distributional characteristics of the data, skew and kurtosis values of the variables fell below the values of 3 (skew) and 10 (kurtosis) that Kline suggests are concerning [-@kline_principles_2016]. Results of the Shapiro-Wilk test of normality indicate that our variables assessing the proportion of classmates who are Black ($W = 0.878, p < 0.001$) and the proportion of BIPOC instructional staff($W = 0.787, p < 0.001$) are statistically significantly different than a normal distribution. The scales assessing the respondent's belonging ($0.973, p = 0.165$) and the respondent's perception of campus climate for Black students ($W = 0.951, p = 0.016$) did not differ differently from a normal distribution.

What would we do in the case of a univariate outlier? I find Kline's [-@kline_principles_2016] chapter on data preparation and management to be extremely useful. He provides ideas for more complex analysis of both univariate and multivariate normality and provides suggestions that range from recoding an extreme value to the next most extreme that is within three standard deviations of the mean to more complicated transformations. First, though we need to further examine the relationships between variables.  We do that, next.

### Pairs Panels

As we work our way from univariate to multivariate inspection of our data, let's take a look at the bivariate relations.

The *pairs.panels()* function from the *psych* package is useful for showing the relationship between variables (probably no more than 10) in a model.

* The lower half is a scatterplot between the two variables with a regression line (red) and mean (dot).  
* The diagonal is a histogram of each variable.  
* The upper half of is the correlation coefficient between the two variables.


```r
psych::pairs.panels(item_scores_df[c("iBIPOC_pr", "cmBlack", "Belonging", "ClimateBL")], stars = TRUE, lm = TRUE)
```

![](03-DataDx_files/figure-docx/unnamed-chunk-10-1.png)<!-- -->

The histograms displayed in the diagonal graph for us what we learned from the Shapiro Wilk's test of normality.  We can clearly see the non-normal distribution in the iBIPOC_pr and cmBlack variables.


## Evaluating Multivariate Normality

**Multivariate outliers** have extreme scores on two or more variables, or a pattern of scores that is atypical.  For example, a case may have scores between two and three standard deviations above the mean on all variables, even though no case would be extreme.  A common method of multivariate outlier detection is the  **Mahalanobis distance** ($D_{M}^{2}$).  This indicates the  distance in variance units between the profile of scores for that case and the vector of sample means, or **centroid**, correcting for intercorrelations.

The *outlier()* function from the *psych* package tells us how far each datapoint is from the multivariate centroid of the data.  That is, find the squared Mahalanobis distance for each data point and compare it to the expected values of $\chi^2$.  The *outlier()* protocol also produces a Q-Q (quantile-quantile) plot with the *n* most extreme data points labeled. 

The code below appends the Mahalanobis values to the dataframe.  It is easy, then, to identify, sort, and examine the most extreme values (relative to the rest of the data in their case/row) to make decisions about their retention or adjustment.

Numeric variables are required in the of the calculation of the Mahalanobis.


```r
item_scores_df$Mahal <- psych::outlier(item_scores_df[c("iBIPOC_pr", "cmBlack", "Belonging", "ClimateBL")]) 
```

![](03-DataDx_files/figure-docx/unnamed-chunk-11-1.png)<!-- -->

Q-Q plots take your sample data, sort it in ascending order, and then plot them versus quantiles (the number varies; you can see it on the X axis) calculated from a theoretical distribution. The number of quantiles is selected to match the size of your sample data. While Normal Q-Q Plots are the ones most often used in practice due to so many statistical methods assuming normality, Q-Q Plots can actually be created for any distribution. To the degree that the plotted line stays on the straight line (representing the theoretical normal distribution), the data is multivariate normally distributed.

It is possible, then to analyze the Mahalanobis distance values.


```r
psych::describe(item_scores_df$Mahal)
```

```
##    vars  n mean   sd median trimmed  mad min   max range skew kurtosis   se
## X1    1 66 3.81 2.24   3.68    3.62 2.36 0.2 11.25 11.05 0.86     0.82 0.28
```

Using this information we can determine cases that have a Mahalanobis distance values that exceeds three standard deviations around the median.  In fact, we can have these noted in a column in the dataframe.


```r
#creates a variable indicating TRUE or FALSE if an item is an outlier
item_scores_df$MOutlier <- dplyr::if_else(item_scores_df$Mahal > (median(item_scores_df$Mahal) + (3*sd(item_scores_df$Mahal))), TRUE, FALSE)

#shows us the first 6 rows of the data so we can see the new variables (Mahal, MOutlier)
head(item_scores_df)
```

```
##   ID iBIPOC_pr cmBlack Belong_1 Belong_2 Belong_3 Blst_1 Blst_2 Blst_3 Blst_4
## 1  1 0.3333333       0        6        6        7      5      3      5      2
## 2  2 0.0000000       5        4        4        6      6      6      2      2
## 3  3 0.5000000      10       NA        3       NA     NA      5      2      2
## 4  4 0.3333333       6        5        3        2      2      2      2      2
## 5  5 1.0000000       5        4        4        4      6      1      1      1
## 6  6 0.0000000      20        5        6        5      5      1      1      2
##   Blst_5 Blst_6 rBlst_1 Belonging ResponseBL StigmaBL ClimateBL     Mahal
## 1      2      2       3      6.33       2.33     3.33      2.83 3.1826820
## 2      4      1       2      4.67       1.67     4.00      2.83 1.0923648
## 3     NA      2      NA        NA       2.00     3.50        NA 0.1979919
## 4      2      2       6      3.33       3.33     2.00      2.67 0.3505949
## 5      1      1       2      4.00       1.33     1.00      1.17 3.8711215
## 6      1      2       3      5.33       2.33     1.00      1.67 4.8818622
##   MOutlier
## 1    FALSE
## 2    FALSE
## 3    FALSE
## 4    FALSE
## 5    FALSE
## 6    FALSE
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
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
#counts frequency TRUE and FALSE indicating outlier or not
OutlierCount<- item_scores_df%>%
  dplyr::count(MOutlier)

#calculating how many outliers a slightly different way
nrow(item_scores_df) - OutlierCount 
```

```
##   MOutlier  n
## 1       66  1
## 2       65 65
```
When we identify outliers we often ask if we should delete them or transform the data. A general rule of thumb is to look for "jumps" in the Mahalanobis distance values. If they are progressing steadily and there is no "jump," researchers will often retain the outliers.


CUMULATIVE CAPTURE FOR THE APA STYLE WRITE-UP:  

>>We evaluated multivariate normality with the Mahalanobis distance test. Specifically, we used the *psych::outlier()* function  and included all continuous variables in the calculation. Our visual inspection of the Q-Q plot suggested that the plotted line strayed from the straight line as the quantiles increased.  Additionally, we appended the Mahalanobis distance scores as a variable to the data. Analyzing this variable, we found that 1 case exceed three standard deviations beyond the median. Given that the Mahalanobis distance values increased in a consistent manner (i.e., no extreme "jumps") we retained all cases.

## A Few Words on Transformations

To quote from Kline [-@kline_principles_2016], "Before applying a normalizing transformation, you should think about the variables of interest and whether the expectation of normality is reasonable." (p. 77)  

At this point in history, the non-normal distribution of the proportions of classmates who are Black and instructional staff who are BIPOC are accurate representations in higher education. Kline [-@kline_principles_2016] has noted that transforming an inherently non-normal variable to force a normal distribution may fundamentally alter it such that the variable of interest is not actually studied. Kline's chapter reviews some options for applying corrections to outliers.  Additionally, the chapter describes a variety of normalizing transformations.

On a personal note, while I will use standardized scores (a linear transformation) if it improves interpretation and center variables around a meaningful intercept, I tend to resist the transformation of data without a really compelling reason. Why?  It's complicated and can make interpretation difficult.


## The APA Style Write-Up

This results section will draw from the three lessons on scrubbing, scoring, and data diagnostics.:

### Data Diagnostics

>>Data screening suggested that 107 individuals opened the survey link. Of those, 83 granted consent and proceeded into the survey items. A further inclusion criteria was that the course was taught in the U.S; 69 met this criteria.

>>Available item analysis (AIA; [@parent_handling_2013]) is a strategy for managing missing data that uses available data for analysis and excludes cases with missing data points only for analyses in which the data points would be directly involved. Parent (2013) suggested that AIA is equivalent to more complex methods (e.g., multiple imputation) across a number of variations of sample size, magnitude of associations among items, and degree of missingness. Thus, we utilized Parent’s recommendations to guide our approach to managing missing data. Missing data analyses were conducted with tools in base R as well as the R packages, *psych* (v. 2.3.6) and *mice* (v. 3.16.0). 

>>Across cases that were deemed eligible on the basis of the inclusion/exclusion criteria, missingness ranged from 0 to 67%. Across the dataset, 3.86% of cells had missing data and 87.88% of cases had nonmissing data. At this stage in the analysis, we allowed all cases with less than 90% missing to continue to the scoring stage. Guided by Parent's [-@parent_handling_2013] AIA approach, scales with three items were scored if at least two items were non-missing; the scale with four items was scored if it at least three non-missing items; and the scale with six items was scored if it had at least five non-missing items. 

>>Across the 66 cases for which the scoring protocol was applied, missingness ranged from 0 to 67%. After eliminating cases with greater than 20% missing, the dataset analyzed included 61 cases. In this dataset we had less than 1% (0.55%) missing across the df; 97% of the rows had nonmissing data.

>>Regarding the distributional characteristics of the data, skew and kurtosis values of the variables fell below the values of 3 (skew) and 10 (kurtosis) that Kline suggests are concerning [-@kline_principles_2016]. Results of the Shapiro-Wilk test of normality indicate that our variables assessing the proportion of classmates who are Black ($W = 0.878, p < 0.001$) and the proportion of BIPOC instructional staff($W = 0.787, p < 0.001$) are statistically significantly different than a normal distribution. The scales assessing the respondent's belonging ($0.973, p = 0.165$) and the respondent's perception of campus climate for Black students ($W = 0.951, p = 0.016$) did not differ differently from a normal distribution.

>>We evaluated multivariate normality with the Mahalanobis distance test. Specifically, we used the *psych::outlier()* function  and included all continuous variables in the calculation. Our visual inspection of the Q-Q plot suggested that the plotted line strayed from the straight line as the quantiles increased.  Additionally, we appended the Mahalanobis distance scores as a variable to the data. Analyzing this variable, we found that 1 case exceed three standard deviations beyond the median. Given that the Mahalanobis distance values increased in a consistent manner (i.e., no extreme "jumps") we retained all cases. 

>>Given that our sample sizes were reasonable for the planned analyses and the degree of missingness was low, we used pairwise deletion in our multiple regression analysis.

## A Quick Regression of our Research Vignette

With some confidence that our scrubbed-and-scored variables are appropriate for analysis, let me conduct the super quick regression that is our research vignette.

![An image of the statistical model for which we are preparing data.](images/Ch04/BlStuRegression.jpg)


```r
Climate_fit <- lm(ClimateBL ~ Belonging + cmBlack + iBIPOC_pr, data = item_scores_df)
summary(Climate_fit)
```

```
## 
## Call:
## lm(formula = ClimateBL ~ Belonging + cmBlack + iBIPOC_pr, data = item_scores_df)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -1.86732 -0.80535  0.02355  0.70459  3.02003 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  2.90791    0.46653   6.233 6.74e-08 ***
## Belonging   -0.01742    0.09643  -0.181    0.857    
## cmBlack     -0.01918    0.01717  -1.117    0.269    
## iBIPOC_pr   -0.64125    0.35701  -1.796    0.078 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.066 on 55 degrees of freedom
##   (7 observations deleted due to missingness)
## Multiple R-squared:  0.08212,	Adjusted R-squared:  0.03206 
## F-statistic:  1.64 on 3 and 55 DF,  p-value: 0.1906
```
### Results

>>Results of a multiple regression predicting the respondents' perceptions of campus climate for Black students indicated that neither contributions of the respondents' personal belonging ($B = -0.017, p = -.857$), the proportion of BIPOC instructional staff ($B-0.641, p = 0.078), nor the proportion of Black classmates ($B$ = -0.019, p = 0.269 ) led to statistically significant changes in perceptions of campus climate for Black students. The model accounted for only 8% of the variance and was not statistically significant ($p = 0.191$). Means, standard deviations, and correlations among variables are presented in Table 1; results of the regression model are presented in Table 2.


```r
apaTables::apa.cor.table(item_scores_df[c("iBIPOC_pr", "cmBlack", "Belonging", "ClimateBL")], table.number = 1, show.sig.stars = TRUE, filename = "Table1_M_SDs_r_DataDx.doc")
```

```
## 
## 
## Table 1 
## 
## Means, standard deviations, and correlations with confidence intervals
##  
## 
##   Variable     M    SD   1           2           3          
##   1. iBIPOC_pr 0.35 0.39                                    
##                                                             
##   2. cmBlack   8.20 8.02 .07                                
##                          [-.18, .31]                        
##                                                             
##   3. Belonging 4.03 1.47 .01         -.13                   
##                          [-.24, .26] [-.36, .12]            
##                                                             
##   4. ClimateBL 2.48 1.09 -.25        -.17        -.04       
##                          [-.47, .01] [-.41, .08] [-.29, .22]
##                                                             
## 
## Note. M and SD are used to represent mean and standard deviation, respectively.
## Values in square brackets indicate the 95% confidence interval.
## The confidence interval is a plausible range of population correlations 
## that could have caused the sample correlation (Cumming, 2014).
##  * indicates p < .05. ** indicates p < .01.
## 
```


```r
library(apaTables)
apaTables::apa.reg.table(Climate_fit, table.number = 2, filename = "Climate_table.doc")
```

```
## 
## 
## Table 2 
## 
## Regression results using ClimateBL as the criterion
##  
## 
##    Predictor      b      b_95%_CI  beta   beta_95%_CI sr2  sr2_95%_CI    r
##  (Intercept) 2.91**  [1.97, 3.84]                                         
##    Belonging  -0.02 [-0.21, 0.18] -0.02 [-0.28, 0.24] .00 [-.01, .01] -.00
##      cmBlack  -0.02 [-0.05, 0.02] -0.15 [-0.41, 0.12] .02 [-.05, .09] -.17
##    iBIPOC_pr  -0.64 [-1.36, 0.07] -0.23 [-0.49, 0.03] .05 [-.06, .16] -.25
##                                                                           
##                                                                           
##                                                                           
##              Fit
##                 
##                 
##                 
##                 
##        R2 = .082
##  95% CI[.00,.20]
##                 
## 
## Note. A significant b-weight indicates the beta-weight and semi-partial correlation are also significant.
## b represents unstandardized regression weights. beta indicates the standardized regression weights. 
## sr2 represents the semi-partial correlation squared. r represents the zero-order correlation.
## Square brackets are used to enclose the lower and upper limits of a confidence interval.
## * indicates p < .05. ** indicates p < .01.
## 
```

## Practice Problems

The three problems described below are designed to be continuations from the Scrubbing and Scoring lessons. You will likely encounter challenges that were not covered in this chapter. Search for and try out solutions, knowing that there are multiple paths through the analysis. The overall notion of the suggestions for practice are to (a) calculate alpha coefficients for the scales, (b) evaluate univariate and multivariate normality, (c) create an APA-style write-up appropriate for a data diagnostics subsection of the results, and (d) run a "quickie" regression, ANOVA, or similar analysis. 
 
### Problem #1: Reworking the Chapter Problem

If you chose this option in the prior chapters, you imported the data from Qualtrics, applied inclusion/exclusion criteria, renamed variables, downsized the df to the variables of interest, properly formatted the variables, interpreted item-level missingness, scored the scales/subscales, interpreted scale-level missingness, and wrote up the results. Please continue with the remaining tasks.


### Problem #2:  Use the *Rate-a-Recent-Course* Survey, Choosing Different Variables

If you chose this option in the prior chapter, you chose a minimum of three variables (different from those in the cahpter) from the *Rate-a-Recent-Course* survey to include in a simple statistical model. You imported the data from Qualtrics, applied inclusion/exclusion criteria, renamed variables, downsized the df to the variables of interest, properly formatted the variables, interpreted item-level missingness, scored the scales/subscales, interpreted scale-level missingness, and wrote up the results.  Please continue with the remaining tasks.
                                                                 

### Problem #3:  Other data

If you chose this option in the prior chapter, you used raw data that was available to you. You imported it into R, applied inclusion/exclusion criteria, renamed variables, downsized the df to the variables of interest, properly formatted the variables, interpreted item-level missingness, scored the scales/subscales, interpreted scale-level missingness, and wrote up the results.  Please continue with the remaining tasks.

### Grading Rubric

|Assignment Component  
|:---------------------------------------------------------------------------------------------|:-------------: |:------------:|
|1. Calculate alpha coefficients for scales/subscales.                                         |      5         |    _____     |    
|2. Evaluate univariate normality (skew, kurtosis, Shapiro-Wilks).                             |      5         |    _____     |
|3. Evaluate multivarite normality (Mahalanobis test)                                          |      5         |    _____     |
|4. Represent your work in an APA-style write-up (added to the writeup in the previous chapter)|      5         |    _____     |  
|5. Conduct a quick analysis (e.g., regression, ANOVA) including at least three variables      |      5         |    _____     |    |6. Explanation to grader                                                                      |      5         |    _____     |   
|**Totals**                                                                                    |      30        |    _____     |               
                                                                                                                                         


