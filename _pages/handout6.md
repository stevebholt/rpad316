---
title: "Class Lab 6"
permalink: /labs/class-lab-6/
toc: true
mathjax: true
---

# Two Sample Hypothesis Tests
## Estimating Summary Statistics
In order to illustrate the process and usefulness of two sample hypothesis tests, we will use the STAR project data. The STAR project was a study conducted in Tennessee schools that aimed to measure the efficacy of small classrooms and teaching aides in improving educational outcomes for students. The researchers measured the effects by randomly assigning students to small classrooms (a "treatment"), regular classrooms with a teacher's aide (another "treatment"), and regular classrooms (a "control" group). The setup of the STAR project, interested in isolating the effects of a policy or program, draws on the gold standard of research designs: randomized control trials (RCT).

Whenever analysts would like to know the causal relationship between an action or intervention and a policy-relevant outcome, they want be able to simultaneously observe outcomes in a world where the action was taken and a world without the action. Of course, such observation is impossible, so analysts compare groups exposed to an action (i.e., a "treatment" group) to groups not exposed to the action (i.e., a "control" group) to make inferences about the effects of the action. When assignment to treatment and control group is not random, a potential worry is that the two groups differ in ways besides exposure to the treatment. For instance, perhaps a larger share of the treatment group has more highly motivated students; thus, the treatment and control group would differ in both motivation _and_ exposure to the treatment, leaving the analyst to wonder which actually caused the difference in outcomes between treatment and control groups. However, in the STAR project (and RCTs more generally), since the sample of students was both large and random, and assignment to each group was also random, the treatment and control groups, _on average_, are unlikely to differ substantially in ways besides their exposure to the treatment.

For our purposes in this exercise, the STAR data has been restricted only to students with non-missing data and who received either the small classroom treatment for all four years of the study (kindergarten through 3rd grade) or the regular classroom control status for all four years of the study.[^bignote1] Let's begin with a quick comparison of the characteristics of the treatment and control groups, defined by the indicator variable `treatment_four`. The variable `treatment_four` takes on a value of 1 a student is in the treatment group and 0 if a student is in the control group. Things like teacher's characteristics in kindergarten and students demographic and socioeconomic characteristics can give us some indication that the treatment and control groups are similar in their observable characteristics. By comparing the average characteristics of the two groups, we are performing a simplified version of what is called a "balance test." Such a test can tell us if our treatment and control group differs, on average, in some important way besides their treatment status. We'll do this by summarizing the observable variables for each group:

```
sum twhite tblack tadvanced_degree frpl speced smale swhite sblack  sasian shisp snative sother if treatment_four == 1
sum twhite tblack tadvanced_degree frpl speced smale swhite sblack  sasian shisp snative sother if treatment_four == 0
```
In the above code, I am telling Stata to calculate summary statistics for the treatment group and control group on the list of variables I have included. The variables are teachers' race, gender, and whether they have an advanced degree and students' race, gender, free or reduced-price lunch eligibility, and special education status. Generally, the proportions of these characteristics should be about equal between treatment and control groups. Let's see how things look.

```
. sum twhite tblack tadvanced_degree frpl speced smale swhite sblack  sasian shisp snative sother if treatment_four == 1

    Variable |        Obs        Mean    Std. dev.       Min        Max
-------------+---------------------------------------------------------
      twhite |        357    .9439776    .2302878          0          1
      tblack |        357    .0560224    .2302878          0          1
tadvanced_~e |        357    .3753501    .4848928          0          1
        frpl |        357     .280112     .449684          0          1
      speced |        357    .0168067    .1287271          0          1
-------------+---------------------------------------------------------
       smale |        357    .4565826    .4988105          0          1
      swhite |        357    .8235294    .3817551          0          1
      sblack |        357    .1680672    .3744508          0          1
      sasian |        357    .0028011    .0529256          0          1
       shisp |        357    .0028011    .0529256          0          1
-------------+---------------------------------------------------------
     snative |        357           0           0          0          0
      sother |        357    .0028011    .0529256          0          1

. 
. sum twhite tblack tadvanced_degree frpl speced smale swhite sblack  sasian shisp snative sother if treatment_four == 0

    Variable |        Obs        Mean    Std. dev.       Min        Max
-------------+---------------------------------------------------------
      twhite |        143     .951049    .2165241          0          1
      tblack |        143     .048951    .2165241          0          1
tadvanced_~e |        143    .3706294     .484671          0          1
        frpl |        143    .2307692     .422806          0          1
      speced |        143     .048951    .2165241          0          1
-------------+---------------------------------------------------------
       smale |        143    .4055944    .4927326          0          1
      swhite |        143    .8741259    .3328734          0          1
      sblack |        143    .1188811    .3247862          0          1
      sasian |        143     .006993    .0836242          0          1
       shisp |        143           0           0          0          0
-------------+---------------------------------------------------------
     snative |        143           0           0          0          0
      sother |        143           0           0          0          0

```

Comparing the two, it looks like the two groups enter kindergarten facing strikingly similar teachers: about the same proportion of the treatment and control group have white teachers, black teachers, and teachers with advanced degrees in kindergarten. However, the treatment group appears to have a slightly higher proportion of boys, lower proportion of white students, and higher proportion of black students. This could be due to random sampling noise or due to the way I constructed the sub-sample, which diverges from the way the study was designed and carried out.

The STAR project collected data on a wide range of educational outcomes at the end of each academic year, including standardized scores on the major subjects. We will focus on reading test scores for this exercise. Let's start by simply summarizing kindergarten reading test scores, the first year of the study, for each group. Here, we will introduce sorting data and the `by variable:` preface:

```
sort treatment_four
by treatment_four: sum read_k
```
## Setting Up a T-test By Hand
The sort command tells Stata to reorganize the observations according to the values of the variable specified, in this case `treatment_four`. Since `treatment_four` has two values, 0 and 1, Stata reorganizes the data to put all the zeroes (i.e., the control group) on top and all the ones (i.e., the treatment group) on the bottom. The second command tells Stata to summarize the reading scores for each group separately. The output will look like:

```
. by treatment_four: sum read_k

--------------------------------------------------------------------------------------------------------------------------------------------------------------
-> treatment_four = 0

    Variable |        Obs        Mean    Std. dev.       Min        Max
-------------+---------------------------------------------------------
      read_k |        143    439.3986     25.9976        384        518

--------------------------------------------------------------------------------------------------------------------------------------------------------------
-> treatment_four = 1

    Variable |        Obs        Mean    Std. dev.       Min        Max
-------------+---------------------------------------------------------
      read_k |        357    453.8515    34.48812        386        605
```

Comparing the average reading scores of the treatment and control groups, we can see that the treatment group has, on average, higher reading scores at the end of the first year of the study. At least superficially, it looks like students in small classrooms who can theoretically receive more attention from less stressed teachers score about 14 points higher on reading tests than their counterparts in regular classrooms one year into the study. It is possible, however, that the gap we see is attributable to sampling noise or error and different samples would find no difference between the two groups. Since policy changes and public resources might depend on getting this right, we want to rule out this possibility using a two sample hypothesis test against the null hypothesis that the difference between the treatment and control group is really zero. Of course, with the summary statistics for both samples, we already have everything we need to conduct a two sample t-test by hand using our handy formula for a two sample t-statistic:


$$ t = \dfrac{(\overline{X_1} - \overline{X_2})}{\sqrt{\frac{s_1^{2}}{n_1}+\frac{s_2^{2}}{n_2}}} $$


Plugging in the mean and standard deviation we have from our summary statistics for each sample, we can calculate a t-score:


$$ \dfrac{(439.40 - 453.85)}{\sqrt{\frac{26^{2}}{143}+\frac{34.49^{2}}{357}}} \rightarrow \dfrac{-14.45}{\sqrt{\frac{676}{143}+\frac{1189.56}{357}}} \rightarrow \dfrac{-14.45}{\sqrt{4.73+3.33}} \rightarrow \dfrac{-14.45}{2.84} \rightarrow t = -5.09 $$


Note that there was a lot of rounding to two decimal points in my calculation, but the t-score of -5.09 will be a reasonably close approximation of what we would find using more precise calculations. Now, we know that our conventional threshold for alpha to determine statistical significance is 0.05.Our last step will be to use our t-score and degrees of freedom to find our p-value. For our purposes, we will use the smaller degrees of freedom from the two samples, in this case `df = 142`. To calculate the p-value, we will again turn to Stata and use the `display` command to estimate the p-value. Here, the command will take the form of `display 2*ttail(df, t)` where we enter our degrees of freedom and the absolute value of our t-score in place of `df` and `t` respectively. Thus, the code is:

```
. display 2*ttail(142, 5.09)
1.117e-06
```

As we can see, our p-value of 0.0000001117 is much lower than the alpha threshold of 0.05. We can therefore confidently reject the null hypothesis that the difference between the treatment and control group is actually 0. More specifically, if the underlying population actually had 0 difference between small and regular classroom students' reading scores, 99.99 percent of samples would have a difference smaller than the one observed in our sample. In short, it would be extremely unlikely for two samples of this size to have a gap in scores this large if there was no gap in the broader population.

## Two-sample T-test in Stata
That was quite a bit of work, however, and we know Stata can be useful for simplifying our work. We can use the `ttest` command with the `, by()` option to have Stata run a two sample t-test for us. The full command is:

```
. ttest read_k, by(treatment_four)

Two-sample t test with equal variances
------------------------------------------------------------------------------
   Group |     Obs        Mean    Std. err.   Std. dev.   [95% conf. interval]
---------+--------------------------------------------------------------------
       0 |     143    439.3986    2.174029     25.9976     435.101    443.6962
       1 |     357    453.8515    1.825305    34.48812    450.2618    457.4413
---------+--------------------------------------------------------------------
Combined |     500     449.718    1.472172    32.91878    446.8256    452.6104
---------+--------------------------------------------------------------------
    diff |           -14.45294    3.196126               -20.73249   -8.173385
------------------------------------------------------------------------------
    diff = mean(0) - mean(1)                                      t =  -4.5220
H0: diff = 0                                     Degrees of freedom =      498

    Ha: diff < 0                 Ha: diff != 0                 Ha: diff > 0
 Pr(T < t) = 0.0000         Pr(|T| > |t|) = 0.0000          Pr(T > t) = 1.0000

```

Here, we can see 1) a summary of both samples separately, 2) a summary of the samples combined, 3) a summary of the difference between the samples, and 4) the null hypothesis tested and test statistics used to test it. Note that the t-statistics looks a little different from ours. This is because Stata uses a more sophisticated way to account for degrees of freedom in two samples, one that will rarely lead to a large enough difference in t-scores to affect hypothesis testing and statistical inference. We can see that the two tailed t-test, and associated p-value, is 0.0000. In other words, it is just as small as the p-value we calculated when we calculated the t-score by hand. Also notice that the 95% Confidence Interval of the difference between the two samples (in the fourth row of the table) does not include zero. This tells us that in 95% of samples of this size, we would not see a difference between treatment and control group test scores of 0. Again, this simply provides us with another way of thinking about how confident we are in the results of our study.

If we were advising policy-makers, we could confidently suggest that small class sizes will increase average reading test scores, particularly since students were randomly assigned to the treatment status. We will not always have samples so cleanly assigned, however, and, in many cases, we will need to account for other differences between the two groups to get more reliable estimates of differences statistical inference.

[^bignote1]: For those interested, the code for cleaning the data to create the subsample used in this lab can be found here.
    
    ```
    use "C:\\star_student.dta"
    keep hstest hsgrdcol gender race gktrace gkthighdegree gktyears gkfreelunch 
    gkrepeat gkspeced gktreadss gktmathss gkabsent g5treadss g5tmathss g4treadss 
    g4tmathss g3treadss g3tmathss hstest hsgrdcol gkclasstype g1classtype 
    g2classtype g3classtype
    /*
    This drops cases missing data using a "loop," a powerful tool to automate
    monotonous and repetitive tasks. It essentially tells Stata to run the
    "drop" command over and over for each of the variables I listed.
    */
    foreach x of varlist gender race gktrace gkthighdegree gktyears gkfreelunch 
    gkrepeat gkspeced gktreadss gktmathss gkabsent g5treadss g5tmathss g4treadss 
    g4tmathss g3treadss g3tmathss gkclasstype g1classtype 
    g2classtype g3classtype{
    drop if `x' == .
    }
    /*
    This creates binary indicators for each category of teacher 
    and student characteristics.
    */
    gen twhite = 0
    replace twhite = 1 if gktrace == 1
    gen tblack = 0
    replace tblack = 1 if gktrace == 2
    label var twhite "White teacher"
    label var tblack "Black teacher"
    gen tadvanced_degree = 0
    replace tadvanced_degree = 1 if gkthighdegree > 2
    label var tadvanced_degree "Teacher has advanced degree"
    gen frpl = 0
    replace frpl = 1 if gkfreelunch == 1
    label var frpl "Free or reduced-price lunch eligible"
    gen repeated = 0
    replace repeated = 1 if gkrepeat == 2
    label var repeated "Student repeated kindergarten"
    gen speced = 0
    replace speced = 1 if gkspeced == 1
    label var speced "Student is special education"
    gen smale = 0
    replace smale = 1 if gender == 1
    label var smale "Male"
    gen swhite = 0
    replace swhite = 1 if race == 1
    gen sblack = 0
    replace sblack = 1 if race == 2
    gen sasian = 0
    replace sasian = 1 if race == 3
    gen shisp = 0
    replace shisp = 1 if race == 4
    gen snative = 0
    replace snative = 1 if race == 5
    gen sother = 0
    replace sother = 1 if race == 6
    label var swhite "White"
    label var sblack "Black"
    label var sasian "Asian"
    label var shisp "Latinx"
    label var snative "Native American"
    label var sother "Other race"
    /*
    This identifies treatment and control groups based on 
    treatment and control status each grade.
    */
    gen treatment_four = 0
    replace treatment_four = 1 if (gkclasstype == 1 & g1classtype == 1 & 
    g2classtype == 1 & g3classtype == 1)
    label var treatment_four "Small class all 4 years"
    gen control_four = 0
    replace control_four = 1 if (gkclasstype == 2 & g1classtype == 2 & 
    g2classtype == 2 & g3classtype == 2)
    label var control_four "Regular class all 4 years"
    keep if (control_four == 1 | treatment_four == 1)
    /*
    This applies a more intuitive name for the tests
    for each grade of the tests.
    */
    rename gktreadss read_k
    rename gktmathss math_k
    rename g3treadss read_3
    rename g3tmathss math_3
    rename g4treadss read_4
    rename g4tmathss math_4
    rename g5treadss read_5
    rename g5tmathss math_5
    save "C:\\STAR_longrun.dta", replace
    ```