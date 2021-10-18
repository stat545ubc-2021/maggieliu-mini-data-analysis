Mini Data-Analysis Deliverable 2
================
Yi Tong (Maggie) Liu

# Introduction

This is **Maggie’s submission for Milestone 2 of the Mini Data Analysis
Project**.

**Recap:** In Milestone 1, I chose a dataset from the `datateachr`
package by Hayley Boyce and Jordan Bourak. The dataset I chose wwas
`cancer_sample`. The analyses in this notebook will be based on the
`cancer_sample` dataset, and will aim to work towards answering my
research question: *Can we predict cancer diagnosis from quantitative
features calculated from images of biopsies of breast masses?*

Running the source code in this notebook requires installation of the
following packages: `devtools`, `datateachr`, and `tidyverse`.

    install.packages("devtools")
    devtools::install_github("UBC-MDS/datateachr")
    install.packages("tidyverse")

We begin by loading the necessary pacakages.

``` r
library(datateachr) # provides the cancer_sample dataset
library(tidyverse) # provides data analysis libraries, including ggplot2, dplyr, and tibble
```

# Task 1: Process and summarize your data (15 points)

## 1.1 Research questions from Milestone 1 (2.5 points)

The 4 research questions I defined in Milestone 1 were:

1.  What are the relationships between each individual nucleus
    measurement and the diagnosis outcome? For example, is a larger
    nucleus radius positively correlated with malignant diagnosis?
2.  What are the relationships between each pair of nucleus measurements
    among malignant and benign diagnoses? For example, among malignant
    diagnoses, what is the relationship between surface area and radius?
    What about among benign diagnoses?
3.  What impact does the standard error for each measurement have on its
    relationship with the diagnosis?
4.  Can we accurately predict whether a diagnosis will be benign or
    malignant using the provided measurements from images of nuclei?

## 1.2 Summarizing and graphing tasks (10 points)

In this section, I’ll implement one summarizing task and one graphing
task for each of the research questions listed in section 1.1. The
options are listed below.

**Summarizing:**

1.  Compute the range, mean, and two other summary statistics of one
    numerical variable across the groups of one categorical variable
    from your data.
2.  Compute the number of observations for at least one of your
    categorical variables. Do not use the function table()\!
3.  Create a categorical variable with 3 or more groups from an existing
    numerical variable. You can use this new variable in the other
    tasks\! An example: age in years into “child, teen, adult, senior”.
4.  Based on two categorical variables, calculate two summary statistics
    of your choosing.

**Graphing:**

1.  Create a graph out of summarized variables that has at least two
    geom layers.
2.  Create a graph of your choosing, make one of the axes logarithmic,
    and format the axes labels so that they are “pretty” or easier to
    read.
3.  Make a graph where it makes sense to customize the alpha
    transparency.
4.  Create 3 histograms out of summarized variables, with each histogram
    having different sized bins. Pick the “best” one and explain why it
    is the best.

The tasks I’ve chosen are summarizing - 1 and graphing - 1.

Before we start, let’s look at a sample of the data.

``` r
cancer_sample %>%
  select(ends_with("_mean"), diagnosis) %>%
  head(10)
```

    ## # A tibble: 10 × 11
    ##    radius_mean texture_mean perimeter_mean area_mean smoothness_mean
    ##          <dbl>        <dbl>          <dbl>     <dbl>           <dbl>
    ##  1        18.0         10.4          123.      1001           0.118 
    ##  2        20.6         17.8          133.      1326           0.0847
    ##  3        19.7         21.2          130       1203           0.110 
    ##  4        11.4         20.4           77.6      386.          0.142 
    ##  5        20.3         14.3          135.      1297           0.100 
    ##  6        12.4         15.7           82.6      477.          0.128 
    ##  7        18.2         20.0          120.      1040           0.0946
    ##  8        13.7         20.8           90.2      578.          0.119 
    ##  9        13           21.8           87.5      520.          0.127 
    ## 10        12.5         24.0           84.0      476.          0.119 
    ## # … with 6 more variables: compactness_mean <dbl>, concavity_mean <dbl>,
    ## #   concave_points_mean <dbl>, symmetry_mean <dbl>,
    ## #   fractal_dimension_mean <dbl>, diagnosis <chr>

### 1.2.1 Research question 1

**Question:** What are the relationships between each individual nucleus
measurement and the diagnosis outcome? For example, is a larger nucleus
radius positively correlated with malignant diagnosis?

**Summarizing:** (1) Compute the range, mean, and two other summary
statistics of one numerical variable across the groups of one
categorical variable from your data.

We will compute the range (split into min and max), mean, median,
variance, and interquartile range.

``` r
cancer_sample %>%
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
  column_to_rownames(var="diagnosis") %>%
  t(.)
```

    ##                               B        M
    ## range_min_radius_mean  6.981000 10.95000
    ## range_max_radius_mean 17.850000 28.11000
    ## mean_radius_mean      12.146524 17.46283
    ## median_radius_mean    12.200000 17.32500
    ## variance_radius_mean   3.170222 10.26543
    ## iqr_radius_mean        2.290000  4.51500

**Graphing:** (1) Create a graph out of summarized variables that has at
least two geom layers.

Let us look at the radius\_mean column across diagnoses.

``` r
cancer_sample %>%
  select(diagnosis, radius_mean) %>%
  group_by(diagnosis) %>%
  ggplot(aes(diagnosis, radius_mean), .groups="keep") +
  geom_boxplot(aes(color=diagnosis, fill=diagnosis), alpha=0.5) +
  stat_summary(fun=mean, colour="black", geom="point", shape=18, size=3, show.legend=FALSE) +
  stat_summary(fun=mean, aes(label=paste("mean: ", round(..y.., 2))), colour="black", geom="text",vjust=-0.7) +
  xlab("Diagnosis") + 
  ylab("Mean radius of nuclei in sample image") + 
  ggtitle("Mean radius of nuclei in sample image by diagnosis")
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

### 1.2.2 Research question 2

**Question:** What are the relationships between each pair of nucleus
measurements among malignant and benign diagnoses? For example, among
malignant diagnoses, what is the relationship between surface area and
radius? What about among benign diagnoses?

**Summarizing - 1: Compute the range, mean, and two other summary
statistics of one numerical variable across the groups of one
categorical variable from your data.**

**Graphing - 1: Create a graph out of summarized variables that has at
least two geom layers.**

### 1.2.3 Research question 3

**Question:** What impact does the standard error for each measurement
have on its relationship with the diagnosis? **Summarizing - 1: Compute
the range, mean, and two other summary statistics of one numerical
variable across the groups of one categorical variable from your data.**

**Graphing - 1: Create a graph out of summarized variables that has at
least two geom layers.**

### 1.2.4 Research question 4

**Question:** Can we accurately predict whether a diagnosis will be
benign or malignant using the provided measurements from images of
nuclei?

**Summarizing - 1: Compute the range, mean, and two other summary
statistics of one numerical variable across the groups of one
categorical variable from your data.**

**Graphing - 1: Create a graph out of summarized variables that has at
least two geom layers.**

## 1.3 Reflecting on the summarizing and graphing tasks (2.5 points)

# Task 2: Tidy your data (12.5 points)

## 2.1 Identifying data tidy-ness (2.5 points)

First let’s `glimpse` the data to see what it looks like.

``` r
glimpse(cancer_sample)
```

    ## Rows: 569
    ## Columns: 32
    ## $ ID                      <dbl> 842302, 842517, 84300903, 84348301, 84358402, …
    ## $ diagnosis               <chr> "M", "M", "M", "M", "M", "M", "M", "M", "M", "…
    ## $ radius_mean             <dbl> 17.990, 20.570, 19.690, 11.420, 20.290, 12.450…
    ## $ texture_mean            <dbl> 10.38, 17.77, 21.25, 20.38, 14.34, 15.70, 19.9…
    ## $ perimeter_mean          <dbl> 122.80, 132.90, 130.00, 77.58, 135.10, 82.57, …
    ## $ area_mean               <dbl> 1001.0, 1326.0, 1203.0, 386.1, 1297.0, 477.1, …
    ## $ smoothness_mean         <dbl> 0.11840, 0.08474, 0.10960, 0.14250, 0.10030, 0…
    ## $ compactness_mean        <dbl> 0.27760, 0.07864, 0.15990, 0.28390, 0.13280, 0…
    ## $ concavity_mean          <dbl> 0.30010, 0.08690, 0.19740, 0.24140, 0.19800, 0…
    ## $ concave_points_mean     <dbl> 0.14710, 0.07017, 0.12790, 0.10520, 0.10430, 0…
    ## $ symmetry_mean           <dbl> 0.2419, 0.1812, 0.2069, 0.2597, 0.1809, 0.2087…
    ## $ fractal_dimension_mean  <dbl> 0.07871, 0.05667, 0.05999, 0.09744, 0.05883, 0…
    ## $ radius_se               <dbl> 1.0950, 0.5435, 0.7456, 0.4956, 0.7572, 0.3345…
    ## $ texture_se              <dbl> 0.9053, 0.7339, 0.7869, 1.1560, 0.7813, 0.8902…
    ## $ perimeter_se            <dbl> 8.589, 3.398, 4.585, 3.445, 5.438, 2.217, 3.18…
    ## $ area_se                 <dbl> 153.40, 74.08, 94.03, 27.23, 94.44, 27.19, 53.…
    ## $ smoothness_se           <dbl> 0.006399, 0.005225, 0.006150, 0.009110, 0.0114…
    ## $ compactness_se          <dbl> 0.049040, 0.013080, 0.040060, 0.074580, 0.0246…
    ## $ concavity_se            <dbl> 0.05373, 0.01860, 0.03832, 0.05661, 0.05688, 0…
    ## $ concave_points_se       <dbl> 0.015870, 0.013400, 0.020580, 0.018670, 0.0188…
    ## $ symmetry_se             <dbl> 0.03003, 0.01389, 0.02250, 0.05963, 0.01756, 0…
    ## $ fractal_dimension_se    <dbl> 0.006193, 0.003532, 0.004571, 0.009208, 0.0051…
    ## $ radius_worst            <dbl> 25.38, 24.99, 23.57, 14.91, 22.54, 15.47, 22.8…
    ## $ texture_worst           <dbl> 17.33, 23.41, 25.53, 26.50, 16.67, 23.75, 27.6…
    ## $ perimeter_worst         <dbl> 184.60, 158.80, 152.50, 98.87, 152.20, 103.40,…
    ## $ area_worst              <dbl> 2019.0, 1956.0, 1709.0, 567.7, 1575.0, 741.6, …
    ## $ smoothness_worst        <dbl> 0.1622, 0.1238, 0.1444, 0.2098, 0.1374, 0.1791…
    ## $ compactness_worst       <dbl> 0.6656, 0.1866, 0.4245, 0.8663, 0.2050, 0.5249…
    ## $ concavity_worst         <dbl> 0.71190, 0.24160, 0.45040, 0.68690, 0.40000, 0…
    ## $ concave_points_worst    <dbl> 0.26540, 0.18600, 0.24300, 0.25750, 0.16250, 0…
    ## $ symmetry_worst          <dbl> 0.4601, 0.2750, 0.3613, 0.6638, 0.2364, 0.3985…
    ## $ fractal_dimension_worst <dbl> 0.11890, 0.08902, 0.08758, 0.17300, 0.07678, 0…

We have 31 numerical columns and 1 categorical column (`diagnosis`).

As we saw in Milestone 1, the dataset does not have any null values.

``` r
# Count the total number of NA values in the cancer_sample dataset
cancer_sample %>%
  rowwise %>%
  summarise(NA_count = sum(is.na(.))) %>%
  sum()
```

    ## [1] 0

Evaluating tidyness:

  - Each row is an observation: Each row is identified by an `ID`, and
    has values for each of the measurements.
  - Each column is a variable: Each column is either a measurement (the
    `dbl` type columns), or `ID`, or a categorical outcome
    (`diagnosis`).
  - Each cell is a value: As there are no NA values, and each cell is
    either a `dbl` or a `chr`, each cell indeed contains a value.

**We conclude that our data is tidy.**

## 2.2 Tidy-ing or untidy-ing the data (5 points)

Since our data is already tidy, let’s untidy it. We’ll do this by
combining the columns `area_worst` and `area_se`.

**Original tidy dataset**

``` r
# displaying columns for tidy data (original dataset)
cancer_sample %>%
  select(c("ID", "diagnosis", "area_mean", "area_worst", "area_se")) %>%
  head(10)
```

    ## # A tibble: 10 × 5
    ##          ID diagnosis area_mean area_worst area_se
    ##       <dbl> <chr>         <dbl>      <dbl>   <dbl>
    ##  1   842302 M             1001       2019    153. 
    ##  2   842517 M             1326       1956     74.1
    ##  3 84300903 M             1203       1709     94.0
    ##  4 84348301 M              386.       568.    27.2
    ##  5 84358402 M             1297       1575     94.4
    ##  6   843786 M              477.       742.    27.2
    ##  7   844359 M             1040       1606     53.9
    ##  8 84458202 M              578.       897     51.0
    ##  9   844981 M              520.       739.    24.3
    ## 10 84501001 M              476.       711.    23.9

**BEFORE: Untidy data**

``` r
# untidying the data and displaying the new untidy column
(cancer_sample_untidy <- cancer_sample %>%
  unite(col="area_worst_se", c(area_worst, area_se), sep=",") %>%
  select(c("ID", "diagnosis", "area_mean", "area_worst_se"))) %>% 
  head(10)
```

    ## # A tibble: 10 × 4
    ##          ID diagnosis area_mean area_worst_se
    ##       <dbl> <chr>         <dbl> <chr>        
    ##  1   842302 M             1001  2019,153.4   
    ##  2   842517 M             1326  1956,74.08   
    ##  3 84300903 M             1203  1709,94.03   
    ##  4 84348301 M              386. 567.7,27.23  
    ##  5 84358402 M             1297  1575,94.44   
    ##  6   843786 M              477. 741.6,27.19  
    ##  7   844359 M             1040  1606,53.91   
    ##  8 84458202 M              578. 897,50.96    
    ##  9   844981 M              520. 739.3,24.32  
    ## 10 84501001 M              476. 711.4,23.94

**AFTER: Tidy data (again)**

To tidy the data again, we need to: 1. Split the `area_worst_se` column
into `area_worst` and `area_se` by the `,` separator, and 2. Convert new
`area_worst` and `area_se` columns from type `chr` to `dbl`.

Note on type conversion: Since we know that there are no NA values and
each entry in the `area_worst_se` column is guaranteed to be in the
format `[numeric][comma][numeric]`, we can safely separate and then
convert the types.

``` r
# tidying back the untidy data
(cancer_sample_tidy <- cancer_sample_untidy %>%
  separate("area_worst_se", into = c("area_worst", "area_se"), sep=",") %>%
  mutate(across(c("area_worst", "area_se"), as.numeric))) %>%
  select(c("ID", "diagnosis", "area_mean", "area_worst", "area_se")) %>%
  head(10)
```

    ## # A tibble: 10 × 5
    ##          ID diagnosis area_mean area_worst area_se
    ##       <dbl> <chr>         <dbl>      <dbl>   <dbl>
    ##  1   842302 M             1001       2019    153. 
    ##  2   842517 M             1326       1956     74.1
    ##  3 84300903 M             1203       1709     94.0
    ##  4 84348301 M              386.       568.    27.2
    ##  5 84358402 M             1297       1575     94.4
    ##  6   843786 M              477.       742.    27.2
    ##  7   844359 M             1040       1606     53.9
    ##  8 84458202 M              578.       897     51.0
    ##  9   844981 M              520.       739.    24.3
    ## 10 84501001 M              476.       711.    23.9

**Explanation**

Initially, our data was tidy. Then, we combined two numeric columns into
one column of type `chr`. The resulting dataset was untidy because each
column did not represent a single variable (our `area_worst_se` column
contained two variables). The untidy column became type `chr` since we
combined them with a string separator `","`. We tidy’d the data up again
by separating the two variables, `area_worst` and `area_se`, into their
own columns. We ensured that the two columns had the correct type by
converting them from `chr` to `dbl`.

## 2.3 Narrowing down research questions (5 points)
