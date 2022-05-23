---
title: "Class Lab 5"
permalink: /labs/class-lab-5/
toc: true
mathjax: true
---

# Means, Deviations, and Standard Errors
For this activity, we are going to be working with data from the Census of Adult Correctional Facilities collected and maintained by the Bureau of Prisons. For this activity, we are going to be looking at the use of disciplinary citations in different prison contexts. Disciplinary citations are used by prison staff to maintain order and control, but are sometimes overused, abused, or used extensively when inmates react to persistent mistreatment. In short, they can sometimes be considered a proxy for the environment in a prison.

Let's start by summarizing the variable `discipline` in the dataset:

```
sum discipline
```

Which should look like: 
```
    Variable |        Obs        Mean    Std. dev.       Min        Max
-------------+---------------------------------------------------------
  discipline |      3,311    725.2921    1633.571          0      53988

```
So correction facilities have an average of 725 disciplinary citations given to inmates per year. Note that the standard deviation is 1633.57 and there are 3,311 observations in our sample. If we apply our formula for measuring the spread of the sampling distribution for samples of 3311 prisons (i.e., calculate the standard error), we would get something like:

$$ s.e. = \dfrac{\sigma}{\sqrt{n}} \rightarrow s.e. = \dfrac{1633.57}{\sqrt{3311}} \rightarrow s.e. = \dfrac{1633.57}{\pm 57.54} \rightarrow s.e. = \pm28.39 $$


## Confidence Intervals
Extending this further, if we wanted to know the 95% confidence interval of disciplinary citations used in our sample of prisons, we would use the z-score associated with 95% of observations falling between it in a normal distribution (see a z-table to confirm). Recall that the z-score for 95% will have 0.025 observations below it in the z-table because there are two tales in the distribution and 2 * 0.025 is 0.05 or 5%. We would calculate out confidence interval as:

$$ C.I. = \overline{x} \pm (Z * s.e.) \rightarrow C.I. = 725.2921 \pm (1.96 * 28.39) \rightarrow C.I. = 725.2921 \pm 55.6444 $$

Resolving equation (2) gives us a 95% confidence interval that ranges from 669.6477 to 780.9365. Of course, the reason we want to use tools like Stata when doing data analysis is because we do not want to have to calculate these statistics by hand for every analysis we run. Stata can apply the formulas for us with a simple command: `mean`. Using `mean` instead of `sum` like this:

```
mean discipline

Mean estimation                          Number of obs = 3,311

--------------------------------------------------------------
             |       Mean   Std. err.     [95% conf. interval]
-------------+------------------------------------------------
  discipline |   725.2921   28.38954      669.6292    780.9549
--------------------------------------------------------------
```
Note that the standard error and 95% confidence interval are provided for us! Of course, because Stata does not round and we did, our hand calculation is slightly different at the second decimal level. Still, Stata is making the same calculation we did, only without rounding.

Of course, sometimes we might be interested in a more relaxed or more restrictive confidence interval. Stata defaults to the 95% confidence interval because in most scientific analyses, 95% confidence has become a conventional threshold for acceptable confidence in an estimate. Using the `, level(#)` option in Stata can allow us to estimate a different confidence interval if needed. For example, the more relaxed 90% confidence interval can be estimated with this code:

```
. mean discipline, level(90)

Mean estimation                          Number of obs = 3,311

--------------------------------------------------------------
             |       Mean   Std. err.     [90% conf. interval]
-------------+------------------------------------------------
  discipline |   725.2921   28.38954      678.5823    772.0018
--------------------------------------------------------------
```

...and here we can see the more restrictive 99% confidence interval:

```
. mean discipline, level(99)

Mean estimation                          Number of obs = 3,311

--------------------------------------------------------------
             |       Mean   Std. err.     [99% conf. interval]
-------------+------------------------------------------------
  discipline |   725.2921   28.38954      652.1233    798.4609
--------------------------------------------------------------
```

Note that the range for the 90% confidence interval contains fewer values and the range for the 99% confidence interval contains more values. That's because when we include a wider range of possible estimates of X-bar, we can be more confident that the true value of mu falls within that range of possible estimates.

## Means and Confidence Intervals by Categories
We care about the average number of disciplinary citations (`discipline`) because the more citations used by guards might suggest a more turbulent environment in the prison. It could be that the guards are being excessively punitive with inmates or that inmates are simply more likely to violate rules, but either way, extensive use of citations might suggest something is amiss in the prison. As policy analysts, we might be interested in looking at how different programs change the number of citations a prison hands out to inmates. First, let's see if the average number of citations is higher at higher security prisons. If citations capture something about the prison environment, we might expect maximum security prisons to have more citations handed to inmates, on average, than minimum security prisons. Let's use the `over()` option of the `mean` command to see how the average number of disciplinary citations varies by prison security level:

```
. mean discipline, over(security_level)

Mean estimation                                         Number of obs = 3,311

-----------------------------------------------------------------------------
                            |       Mean   Std. err.     [95% conf. interval]
----------------------------+------------------------------------------------
c.discipline@security_level |
                   Sup Max  |   1473.957   609.1273      279.6531    2668.262
                       Max  |   1614.596    109.623       1399.66    1829.532
                       Med  |   953.3715   38.76677      877.3622    1029.381
                       Min  |    217.296   15.33997      187.2192    247.3728
-----------------------------------------------------------------------------
```

This looks about how we might expect. Higher security level prisons, on average, give inmates a lot more disciplinary citations than lower security level prisons.

# Hypothesis Testing
## Single Sample T-Test
Now, let's say after a lawsuit against some prisons that excessively cite inmates, a judge has capped their use of citations at 300. We have been tasked with examining prisons with a court order and seeing if they are adhering to the cap. In the dataset, `order_discipline` is an indicator variable that is equal to 1 if the judge has ordered them to change their citation practices. Let's look at the mean and standard deviation of these prisons:

```
. tabstat discipline if order_discipline == 1, statistics(mean sd)

    Variable |      Mean        SD
-------------+--------------------
  discipline |  925.5283  1191.809
----------------------------------
```

Our sample of prisons with a court order gave out an average of 925 citations, which certainly seems like prisons are ignoring court orders. But, do we think this is a systematic problem or did we just get a fluke sample? Here is where hypothesis testing can be handy. To assess how confident we are that we did not just get a fluke sample, we can assume that the average prison with a court order follows the order and uses 300 citations. We can then use the properties of a normal distribution to assess the likelihood of getting a sample with an average of 925 citations if the true average number of citations is 300. In place of doing this by hand, we will let Stata calculate the relevant statistics for us using the `ttest` command.

```
ttest discipline==300 if order_discipline == 1
```

In the above code, we are telling to conduct a hypothesis test that the average number of disciplinary citations among prisons with a court order is 300 (i.e., we are testing the hypothesis that the prisons are following the court order, on average). The `discipline==300` part of the code tells Stata to test whether the average of `discipline` is equal to 300 citations. The `if order_discipline == 1` part of the code sets the condition that only the sample of prisons with a court order is included in our hypothesis test. Using that code will give us output that looks like this:

```
One-sample t test
------------------------------------------------------------------------------
Variable |     Obs        Mean    Std. err.   Std. dev.   [95% conf. interval]
---------+--------------------------------------------------------------------
discip~e |      53    925.5283    163.7075    1191.809    597.0252    1254.031
------------------------------------------------------------------------------
    mean = mean(discipline)                                       t =   3.8210
H0: mean = 300                                   Degrees of freedom =       52

   Ha: mean < 300               Ha: mean != 300               Ha: mean > 300
 Pr(T < t) = 0.9998         Pr(|T| > |t|) = 0.0004          Pr(T > t) = 0.0002
```

In the above output, Stata gives us a table with the N, mean, standard error, standard deviation, and 95\% confidence interval for the sample. Below the table, you can see that Stata restates the null hypothesis you tested. In this case, it was `H0: mean = 300`, which is another way of saying that the null hypothesis we tested was that the average number of citations at prisons in the sample was 300. Of course, as the table should, the average we actually see in our sample is 925 citations. At the bottom of the output, Stata calculates the relevant test statistics for three alternative hypotheses: that the mean is actually less than 300, that the mean is not equal (`!=` is Stata's way of saying "not equal") to 300, and that the mean is actually greater than 300. Looking at the middle hypothesis, we can see that the p-value for the alternative hypothesis that the mean is not equal to 300 is 0.0004. This tells us that if the mean _actually was_ 300 (i.e., that the null hypothesis really was true), only 0.04% of samples of 53 prisons would have 925 citations given out on average. Put another way, we can be very confident that the null hypothesis is not true and we have strong evidence that prisons are not following the court orders on average.

Note that the 95% confidence interval also provides some evidence that we can reject the null hypothesis even if we didn't have the p-value immediately handy. The confidence interval suggests that 95% of samples of 53 prisons with court orders will have a mean between 597 citations and 1254 citations. In other words, most samples will have a mean greater than the 300 citation court ordered limit, and we can confidently reject the null hypothesis with that information alone. The p-value allows us to be more precise, but the confidence interval can still be informative.

## Two-Sample T-Test
Of course, we are often interested in alleviating problems and assessing whether our attempts to alleviate problems are successful. Let's say we are interested in assessing whether work release programs, programs where inmates are allowed to leave the jail or prison and work at a job, improve the climate at the prison. Here, we might calculate the average number of citations for prisons with work release programs and prisons without work release programs, like so:

```
. mean discipline, over(workrelease)

Mean estimation                                      Number of obs = 3,263

--------------------------------------------------------------------------
                         |       Mean   Std. err.     [95% conf. interval]
-------------------------+------------------------------------------------
c.discipline@workrelease |
                      0  |   951.4821   38.91204      875.1876    1027.777
                      1  |    207.597   19.06668      170.2132    244.9809
--------------------------------------------------------------------------
```

Looks promising for our program! Just a comparison of averages seems to suggest that prisons with work release programs, on average, have far fewer citations given to inmates than prisons without work release programs. Of course, we might be worried that such programs are less common in higher security level prisons. Let's restrict our analysis to only minimum security level prisons:

```
. mean discipline if security_level == 4, over(workrelease)

Mean estimation                                      Number of obs = 1,592

--------------------------------------------------------------------------
                         |       Mean   Std. err.     [95% conf. interval]
-------------------------+------------------------------------------------
c.discipline@workrelease |
                      0  |   336.5699   31.20597      275.3608    397.7791
                      1  |   116.8658   7.503047      102.1489    131.5827
--------------------------------------------------------------------------
```

Our work release program still looks promising, but the gap between the two groups of prisons is certainly much smaller. Now, we want to be confident that this difference we are seeing is a real difference and not just statistical noise. Put another way, we want to test the null hypothesis that the average difference in citations between prisons with and without work release is actually 0.

Again, we will return to our t-test command, just like we did before, only this time, we will tell Stata to test the difference in means between two groups: prisons without work release and prisons with work release. We will keep our restriction to minimum security prisons. By using the `, by()` option in Stata, we can tell Stata to run the t-test between all categories in the the variable work release. Stata will use the null hypothesis that the difference between the categories is actually 0 and compute the test statistics needed for testing that hypothesis. The code looks like this:

```
. ttest discipline if security_level == 4, by(workrelease)

Two-sample t test with equal variances
------------------------------------------------------------------------------
   Group |     Obs        Mean    Std. err.   Std. dev.   [95% conf. interval]
---------+--------------------------------------------------------------------
       0 |     765    336.5699    31.20597    863.1145    275.3103    397.8296
       1 |     827    116.8658    7.503047    215.7697    102.1385    131.5931
---------+--------------------------------------------------------------------
Combined |   1,592    222.4397    15.73096    627.6634    191.5841    253.2953
---------+--------------------------------------------------------------------
    diff |            219.7042    31.01002                158.8793     280.529
------------------------------------------------------------------------------
    diff = mean(0) - mean(1)                                      t =   7.0849
H0: diff = 0                                     Degrees of freedom =     1590

    Ha: diff < 0                 Ha: diff != 0                 Ha: diff > 0
 Pr(T < t) = 1.0000         Pr(|T| > |t|) = 0.0000          Pr(T > t) = 0.0000

```

Here, Stata now computes a table with the N, mean, standard error and deviation, and 95% confidence interval for both groups; the same statistics for the two groups combined; and the difference between the two group averages. Again, the difference seems quite large: prisons without work release, on average, hand out 219 more citations than prisons with work release.

Below the table, you can see that Stata has provided the null and alternative hypotheses. The null is that the average of both groups is actually the same, or that there is no average difference in citations between prisons with and without work release (Stata puts this as `H0: diff = 0`). Again, looking to the alternative hypothesis that the difference between these two groups of prisons is not 0, we can see that there is a 0.0000 probability of a sample of 1,592 prisons showing an average difference of 219 citations if the null hypothesis of no difference was true. In other words, we can be very confident in rejecting the null hypothesis that work release programs make no difference in citation rates!