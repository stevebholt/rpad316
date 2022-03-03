---
title: "Class Lab 4"
permalink: /labs/class-lab-4/
toc: true
mathjax: true
---

# Review
In our dataset for this activity, which again comes from the ATUS and includes time spent on a variety of activities, we have a set of indicators for race categories and income categories. You will find that for some things, like graphs and two-way tables in tabulate, having categorical variables is valuable, while for other things, like creating a table for reporting outside of Stata, indicator variables are more useful. Let’s create a categorical version household income using the indicator variables in the dataset.

As always, be sure to label both the variable and its values so you do not forget what the variable is later.

```
gen income = .
replace income = 1 if loinc == 1
replace income = 2 if lowmidinc == 1
replace income = 3 if himidinc == 1
replace income = 4 if inc150p == 1

label var income "HH income"
label define inccat 1 "<$20K" 2 "$20K to 60K" 3 "$60K to 150K" 4 ">$150K"
label val income inccat
```

Let’s practice creating a table that captures the average time spent on taking care of others, working, and socializing by income bracket using the income variable we created above. We will begin by creating variables that only includes people who spent at least some time on each activity.

```
gen care2 = care_tot if care_tot > 0
gen wrktime2 = wrktime if wrktime > 0
gen socialize2 = socialize_tot if socialize_tot > 0

label var wrktime2 "Time working"
label var care2 "Time caring for others"
label var socialize2 "Time socializing"
```

Now, let’s plug those variables into the same code we used before, in [Class Lab 3](https://stevebholt.github.io/rpad316/labs/class-lab-3/#summary-tables-with-subgroups), to make our conditional means table using binary indicators. For this table, we will examine time spent on these activities, in minutes, across the different income categories using the categorical variable we created.

```
estpost sum wrktime2 care2 socialize2 if income == 1
eststo lowinctime
estpost sum wrktime2 care2 socialize2 if income == 2
eststo lowmidinc
estpost sum wrktime2 care2 socialize2 if income == 3
eststo himidinc
estpost sum wrktime2 care2 socialize2 if income == 4
eststo highinc
estpost sum wrktime2 care2 socialize2
eststo totals
esttab lowinctime lowmidinc himidinc highinc totals using "output\example1_lab4.csv",
 cell(mean(fmt(2)) sd(par)) replace label unstack csv mtitles("Low income" 
 "Lower-middle income" "Upper-middle income" "High income" "Total")
```

As always, replace the `example1_lab4` part above with the title of the file that makes the most sense for you. The `mtitles()` part of the code labels the columns of the table, in order. The code should produce a table that looks like this:

![Example Table](http://stevebholt.github.io/rpad316/assets/images/table1_lab4.PNG)

What a nice table! The table shows that, on an average day, someone in the U.S. works 435.33 minutes or about 7 hours and 15 minutes. Meanwhile, the average American, on an average day during which they care for someone, spends about 115 minutes caring for the person or about 1 hour and 55 minutes. Generally, it looks like the lower-middle class spends the most time working, on average, while high income individuals spend the least time working. Similarly, both high- and low-income individuals spend about the same amount of time caring for someone else, on average.

Now, as we covered, sometimes we are interested in difference between categories relative to the distribution of all values of x. We can see this by calculating z-scores for each observation, which tells us where that observation lies relative to the whole population. To compute the z-score, we just need the mean and standard deviation of the sample and we can compute a z-score for each observation.

In Stata, we can do this by creating a variable that contains the mean of our variables for the full sample and another that contains the standard deviation of the full sample. To do this, we will use `egen`, a special version of the generate command that can create new variables that are derived from a mathematical function of other variables. We will be using the simplest application of `egen` for calculating z-scores. I will use care time as the example to walk through the steps. First, create a variable equal to the sample mean of time spent caring for others:

```
egen mean_care = mean(care2)
```

This will create a variable that is equal to the mean and apply that value to every observation in your dataset. We will use `egen` again to do the same thing for estimating the standard deviation.

```
egen sd_care = sd(care2)
```

Now, we can use all of these components and create a variable that applies the z-score formula to each observation of x (in this case `care2`) in the sample. Recall that the formula for the z-score is:

```
Z = (X - X-bar)/Sx
```

In Stata, the variables we just created look like this:

![Data Example](http://stevebholt.github.io/rpad316/assets/images/step1_lab4.PNG)

Notice that the variables we created before, `mean_care` and `sd_care`, have the same value for each observation in the dataset, and the value is equal to the sample mean of care time and the sample standard deviation of care time. We can now use each observation's X, mean, and standard deviation to create a variable that contains the z-score of time spent caring for someone else:

```
gen z_care = ((care2 - mean_care)/sd_care)
```

The code creates a variable that looks like this for the same set of observations:

![Data Example](http://stevebholt.github.io/rpad316/assets/images/step2_lab4.PNG)

Now, `z_care` contains each observation's z-score. As a result, when we take the sample mean of `z_care`, it will have a mean of zero with a standard deviation of 1, reflecting the fact that it is a standardized variable expressed in terms of deviations from the mean. Let's follow the same process for each of the other time variables we have to standardize them and create a table of their means and standard deviations to demonstrate this.

```
egen mean_wrktime = mean(wrktime2)
egen sd_wrktime = sd(wrktime2)
gen z_wrktime = ((wrktime2 - mean_wrktime)/sd_wrktime)


egen mean_social = mean(socialize2)
egen sd_social = sd(socialize2)
gen z_social = ((socialize2 - mean_social)/sd_social)

label var z_wrktime "Standardized work time"
label var z_care "Standardized care time"
label var z_social "Standardized social time"
```

Now, let's re-create our previous table, using z-scores instead of minutes in each activity.

```
estpost sum z_wrktime z_care z_social if income == 1
eststo lowinctime
estpost sum z_wrktime z_care z_social if income == 2
eststo lowmidinc
estpost sum z_wrktime z_care z_social if income == 3
eststo himidinc
estpost sum z_wrktime z_care z_social if income == 4
eststo highinc
estpost sum z_wrktime z_care z_social
eststo totals
esttab lowinctime lowmidinc himidinc highinc totals using "output\example2",
 cell(mean(fmt(2)) sd(par)) replace label csv mtitles("Low income"
  "Lower-middle income" "Upper-middle income" "High income" "Total")
```

Which will make a table that looks like:

![Data Example](http://stevebholt.github.io/rpad316/assets/images/table2_lab4.PNG)

In column 5, we see that all three variables have a mean of approximately 0 and standard deviation of 1, as expected. As a result, we can more directly compare the size of the gap between income categories across all activities. We can see that, on average, high income individuals' work time is 0.17 of a standard deviation below the average American. Meanwhile, high income individuals' care-taking time is about 0.05 of a standard deviation above the average American. The gap between high and low income individuals is highest in socializing time, with a gap of about 0.49 of a standard deviation.