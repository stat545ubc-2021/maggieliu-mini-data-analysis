# STAT545A Fall 2021 Mini Data Analysis

This is Maggie Liu's repository for the STAT545A Fall 2021 Mini Data Analysis project. In this project, we explore some of the datasets provided by the `datateachr` R package, choose a dataset, create visualizations related to that dataset, and run analyses on it to answer some research questions.

The dataset I eventually chose to analyze is `cancer_sample`. See a description of the dataset and its columns on the [UBC-MDS datateachr page](https://rdrr.io/github/UBC-MDS/datateachr/man/cancer_sample.html).

## Project Milestones

Below are brief descriptions of each milestone in this project, along with a checked box if the milestone was completed.

- [x] **Milestone 1** (October 9, 2021)
  - Explore datasets provided by `datateachr` package
  - Narrow down datasets of interest from 4 to 2 to 1
  - Write research questions I'd like to answer about the chosen dataset. The dataset I chose was `cancer_sample`
- [x] **Milestone 2** (October 19, 2021)
  - Summarize data in a way that makes sense for each of the 4 research questions created in Milestone 1
  - Explore tidy and un-tidy data using my dataset, `cancer_sample`
  - Pick 2 of the 4 research questions from Milestone 1 for continued analysis in Milestone 3
  - Create a version of the `cancer_sample` dataset that will help to answer the 2 research questions
- [x] **Milestone 3** (October 28, 2021)
  - Explore special data types in R, such as factors
  - Fit a model to my dataset and extract a result
  - Read and write data as separate files
  - Ensure that this repo is clean and up to date.

## How to interact with this repository

You can interact with this repository by either browsing or running some of the code yourself! **What do you want to do?**

🔍 **I want to browse**

Each `milestone-i` directory contains a corresponding `mini-project-i.md` file. This `.md` file was created by knitting the corresponding `.Rmd` file in the same directory. To browse the code, output, and analysis in each milestone, simply take a look at the `.md` file!

💻 **I want to run the code**

First, ensure that you have the `git` command line tool installed.

- [Install git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

Clone this repo to your local computer. Navigate to the directory where you want to store this repo locally, and run

```
git clone https://github.com/stat545ubc-2021/maggieliu-mini-data-analysis.git
```

This repository is written in R. It's best to interact with this repository using R Studio.

- [Install R](https://www.r-project.org/)
- [Install R Studio](https://www.rstudio.com/products/rstudio/download/)

Make sure you have the necessary R packages. The following code chunk installs all the necessary packages. Use the lines you need to install the packages you're missing (or install all of them if it's your first time in R).

First, install the `datateachr` package if you don't already have it. This provides us with the datasets we need. Run the following in your R console.

```
install.packages("devtools")
devtools::install_github("UBC-MDS/datateachr")
```

Then, install the following packages by running `install.packages("{package_name}")` in your R console.

```
tidyverse
ggcorrplot
GGally
caret
stringr
```

Now that you've installed all the packages, you're ready to run some code! Each `milestone-i` directory contains a correpsonding `mini-project-i.Rmd` file. The `.Rmd` file contains all the source code for that milestone. Open up the `.Rmd` file in RStudio and click the "knit" button. Or press `Command+Shift+K` (Mac) or `Control+Shift+K` (Windows) to knit.

If you wish, you can also run individual code chunks within the `.Rmd` file.

Any additional files that are created, written to, or read from within an `.Rmd` file should be in the `output` directory.

## File List

- **`milestone-1`:** The directory containing the `.Rmd` source code file, `.md` knitted file, and assets (images and plots) for the milestone 1 submission.
- **`milestone-2`:** The directory containing the `.Rmd` source code file, `.md` knitted file, and assets (images and plots) for the milestone 2 submission.
- **`milestone-3`:** The directory containing the `.Rmd` source code file, `.md` knitted file, and assets (images and plots) for the milestone 3 submission.
- **`output`:** The directory containing data-storing files that were created in any of the three milestones (e.g., `.csv` and `.rds` files)
- **`README.md`:** This file. It contains information about the milestones and files in the repository.
