Mini Data-Analysis Deliverable 3
================

# Welcome to your last milestone in your mini data analysis project\!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

  - Manipulating special data types in R: factors and/or dates and
    times.
  - Fitting a model object to your data, and extract a result.
  - Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)
directly. Fill in the sections that are tagged with `<!--- start your
work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from `output:
html_document` to `output: github_document`. Commit and push all of your
work to your mini-analysis GitHub repository, and tag a release on
GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 40 points (compared to the usual 30
points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

**Research Questions**: In Milestone 2, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
library(tidyverse)
library(stringr) # provides string operation functions
library(GGally) # extends ggplot2 for correlation plots
library(caret) # provides recall and precision functions for prediction models
```

From Milestone 2, you chose two research questions. What were they? Put
them
here.

<!-------------------------- Start your work below ---------------------------->

1.  What are the relationships between each pair of nucleus measurements
    among malignant and benign diagnoses? For example, among malignant
    diagnoses, what is the relationship between surface area and radius?
    What about among benign diagnoses?
2.  Can we accurately predict whether a diagnosis will be benign or
    malignant using the provided measurements from images of nuclei?
    <!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot
below.

<!-------------------------- Start your work below ---------------------------->

Since the dataset only has one categorical column with 2 groups, we will
create a new categorical variable with 4 groups from another variable
(`radius_mean`)

Let’s look at how `compactness_mean` is distributed across different
radius levels.

``` r
cancer_sample_radius_levels <- cancer_sample %>%
  mutate(radius_mean_level = case_when(
    radius_mean < 12 ~ "small",
    radius_mean < 15 ~ "medium",
    radius_mean < 20 ~ "large",
    TRUE ~ "very large",
  ))

cancer_sample_radius_levels %>%
  ggplot(aes(x=compactness_mean, y=..density.., colour=radius_mean_level)) +
  geom_density()
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->
<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats`
    package (3 points). Then, in a sentence or two, briefly explain why
    you chose this grouping (1 point here for demonstrating
    understanding of the grouping, and 1 point for demonstrating some
    justification for the grouping, which could be subtle or
    speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):
    
    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)
          - Note that you might first have to *make* a time-based column
            using a function like `ymd()`, but this doesn’t count.
          - Examples of something you might do here: extract the day of
            the year from a date, or extract the weekday, or let 24
            hours elapse on your dates.
    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).
          - For example, you could say something like “Investigating the
            day of the week might be insightful because penguins don’t
            work on weekends, and so may respond
differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 1

We will reorder the `radius_mean_level` in increasing order of level,
that is, in the order `small`, `medium`, `large`, `very large`. In the
before chart, we see that the x-axis shows `radius_mean_level` out of
order. It makes sense to order them in increasing radius mean for better
readability in visualizations.

Before:

``` r
cancer_sample_radius_levels %>%
  ggplot(aes(radius_mean_level)) + 
  geom_bar()
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

After:

``` r
cancer_sample_radius_levels_reordered <- cancer_sample_radius_levels %>%
  mutate(radius_mean_level = fct_relevel(radius_mean_level, c("small", "medium", "large", "very large")))
  
cancer_sample_radius_levels_reordered %>%
  ggplot(aes(radius_mean_level)) + 
  geom_bar()
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

<!----------------------------------------------------------------------------->

<!-------------------------- Start your work below ---------------------------->

**Task Number**: 2

As we see in the previous bar chart, the `very large` level for
`radius_mean` does not include many data points `large` also does not
include many data points. Hence, it makes sense to combine these two
levels into one group: `large`.

``` r
cancer_sample_radius_levels_reordered %>% 
  mutate(radius_mean_level = fct_collapse(radius_mean_level, large = c("large", "very large"))) %>%
  ggplot(aes(radius_mean_level)) + 
  geom_bar()
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate
these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: Can we accurately predict whether a diagnosis
will be benign or malignant using the provided measurements from images
of nuclei?

**Variable of interest**:
`diagnosis`

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

  - **Note**: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
      - You could fit a model that makes predictions on Y using another
        variable, by using the `lm()` function.
      - You could test whether the mean of Y equals 0 using `t.test()`,
        or maybe the mean across two groups are different using
        `t.test()`, or maybe the mean across multiple groups are
        different using `anova()` (you may have to pivot your data for
        the latter two).
      - You could use `lm()` to test for significance of
regression.

<!-------------------------- Start your work below ---------------------------->

``` r
cancer_radius_malignancy <- cancer_sample %>%
  mutate(is_malignant = case_when(
    diagnosis == "M" ~ 1,
    TRUE ~ 0
  )) %>%
  select(-c(diagnosis, ID))

# separate into training and testing data
set.seed(123)
train_size = nrow(cancer_sample) * 0.7
train_index <- sample(
  seq_len(nrow(cancer_radius_malignancy)), 
  size=train_size)

train_data <- cancer_radius_malignancy[train_index,]
test_data <- cancer_radius_malignancy[-train_index,]

# create the logistic regression model to predict malignancy
logit <- glm(
  is_malignant ~ radius_mean + perimeter_mean + area_mean + 
    compactness_mean + smoothness_mean + symmetry_mean, 
  data = train_data, family = "binomial")
```

    ## Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

``` r
print(logit)
```

    ## 
    ## Call:  glm(formula = is_malignant ~ radius_mean + perimeter_mean + area_mean + 
    ##     compactness_mean + smoothness_mean + symmetry_mean, family = "binomial", 
    ##     data = train_data)
    ## 
    ## Coefficients:
    ##      (Intercept)       radius_mean    perimeter_mean         area_mean  
    ##          -4.7852           -5.1364            0.5070            0.0344  
    ## compactness_mean   smoothness_mean     symmetry_mean  
    ##           0.5230           66.3677           11.4931  
    ## 
    ## Degrees of Freedom: 397 Total (i.e. Null);  391 Residual
    ## Null Deviance:       515 
    ## Residual Deviance: 153.2     AIC: 167.2

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

  - Be sure to indicate in writing what you chose to produce.
  - Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
  - Obtain your results using the `broom` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not
compatible.

<!-------------------------- Start your work below ---------------------------->

We will use `broom::augment` to see the fitted values and residuals on
our dataset. The column we are interested in is `.fitted`. We specify
`type.predict="response"` to ensure that the fitted values column shows
us the probability of the entry being in class 1. By default, we see the
log odds ratio but we are interested in the probability.

``` r
broom::augment(logit, type.predict="response") %>%
  select(is_malignant, starts_with(".")) %>%
  head(10)
```

    ## # A tibble: 10 × 7
    ##    is_malignant .fitted  .resid .std.resid    .hat .sigma     .cooksd
    ##           <dbl>   <dbl>   <dbl>      <dbl>   <dbl>  <dbl>       <dbl>
    ##  1            1 0.262    1.64       1.67   0.0430   0.621 0.0189     
    ##  2            0 0.0425  -0.295     -0.296  0.0113   0.626 0.0000735  
    ##  3            0 0.00212 -0.0651    -0.0651 0.00123  0.627 0.000000375
    ##  4            0 0.00899 -0.134     -0.135  0.0111   0.627 0.0000147  
    ##  5            1 0.872    0.523      0.538  0.0578   0.626 0.00136    
    ##  6            1 0.931    0.377      0.380  0.0157   0.626 0.000171   
    ##  7            0 0.0235  -0.218     -0.219  0.00826  0.627 0.0000289  
    ##  8            0 0.0140  -0.168     -0.168  0.00382  0.627 0.00000780 
    ##  9            0 0.0569  -0.342     -0.344  0.00898  0.626 0.0000788  
    ## 10            1 0.743    0.771      0.789  0.0455   0.625 0.00247

Next, we will compute predictions on our test dataset.

``` r
# run the model on our test data
threshold <- 0.5
probabilities <- logit %>% predict(test_data, type="response")
pred_classes <- ifelse(probabilities > threshold, 1, 0)

# 
true_classes_factor <- as.factor(test_data$is_malignant)
pred_classes_factor <- as.factor(pred_classes)
precision <- posPredValue(pred_classes_factor, true_classes_factor, positive="1")
recall <- sensitivity(pred_classes_factor, true_classes_factor, positive="1")
f1 <- 2 * precision * recall / (precision + recall)

# create a table to display model evaluation scores
tribble(
  ~precision, ~recall, ~f1,
  precision, recall, f1
)
```

    ## # A tibble: 1 × 3
    ##   precision recall    f1
    ##       <dbl>  <dbl> <dbl>
    ## 1     0.913  0.863 0.887

``` r
# plot the radius mean against its predicted probability of being malignant
# each point is coloured by its true malignancy
test_data %>%
  ggplot(aes(radius_mean, probabilities, color=is_malignant)) +
  geom_point(alpha=0.4)
```

![](mini-project-3_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->
<!----------------------------------------------------------------------------->

As we can see from the graph and table, the model creates very good
separation of malignant and benign cases correctly. Our precision,
recall, and F1 scores are also very high, indicating that the model is
quite good at classifying images as benign from only `radius_mean`,
`perimeter_mean`, `area_mean`, `compactness_mean`, `smoothness_mean`,
and `symmetry_mean`.

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

  - **Robustness criteria**: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
  - **Reproducibility criteria**: You should be able to delete the csv
    file, and remake it simply by knitting this Rmd
file.

<!-------------------------- Start your work below ---------------------------->

We will use the summary for `radius_mean` from milestone 2. The table is
recreated below.

``` r
cancer_sample_radius_mean_summary <- cancer_sample %>%
  select(diagnosis, radius_mean) %>%
  group_by(diagnosis) %>%
  summarise(
    .groups="keep",
    range_min_radius_mean=min(radius_mean),
    range_max_radius_mean=max(radius_mean),
    mean_radius_mean=mean(radius_mean),
    median_radius_mean=median(radius_mean),
    variance_radius_mean=var(radius_mean),
    iqr_radius_mean=IQR(radius_mean)
  ) %>%
  pivot_longer(cols=-diagnosis) %>% 
  pivot_wider(id_cols = name, names_from = diagnosis, values_from = value )

cancer_sample_radius_mean_summary
```

    ## # A tibble: 6 × 3
    ##   name                      B     M
    ##   <chr>                 <dbl> <dbl>
    ## 1 range_min_radius_mean  6.98 11.0 
    ## 2 range_max_radius_mean 17.8  28.1 
    ## 3 mean_radius_mean      12.1  17.5 
    ## 4 median_radius_mean    12.2  17.3 
    ## 5 variance_radius_mean   3.17 10.3 
    ## 6 iqr_radius_mean        2.29  4.51

Now, let’s write `cancer_sample_summary` to a csv file in the `output`
directory.

``` r
output_dir <- "output"
csv_file <- "milestone3_cancer_sample_radius_mean_summary.csv"
write_csv(cancer_sample_radius_mean_summary, here::here(output_dir, csv_file))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your `output`
folder. Use the functions `saveRDS()` and `readRDS()`.

  - The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

``` r
rds_file <- here::here(output_dir, "milestone3_logit.rds")
saveRDS(logit, file=rds_file)
readRDS(rds_file)
```

    ## 
    ## Call:  glm(formula = is_malignant ~ radius_mean + perimeter_mean + area_mean + 
    ##     compactness_mean + smoothness_mean + symmetry_mean, family = "binomial", 
    ##     data = train_data)
    ## 
    ## Coefficients:
    ##      (Intercept)       radius_mean    perimeter_mean         area_mean  
    ##          -4.7852           -5.1364            0.5070            0.0344  
    ## compactness_mean   smoothness_mean     symmetry_mean  
    ##           0.5230           66.3677           11.4931  
    ## 
    ## Degrees of Freedom: 397 Total (i.e. Null);  391 Residual
    ## Null Deviance:       515 
    ## Residual Deviance: 153.2     AIC: 167.2

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

  - In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
  - In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without
them\! They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a
sentence or two what is in the folder, in plain language (it’s enough to
say something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

  - All Rmd files have been `knit`ted to their output, and all data
    files saved from Exercise 3 above appear in the `output` folder.
  - All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
  - There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B\!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
