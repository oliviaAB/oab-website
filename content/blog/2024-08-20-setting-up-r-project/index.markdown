---
title: "Setting up a reproducible data analysis project in R"
subtitle: "featuring GitHub, {renv}, {targets} and more"
excerpt: "How I setup a new data analysis project in R to ensure reproducibility and reusability."
date: 2024-08-20
author: "Olivia Angelin-Bonnet"
draft: true
images:
series:
tags:
categories:
layout: single-sidebar
---

This is the blog post version of a talk that I gave as part of the [ECSSN and NZSA joint Webinar Series](https://www.statsoc.org.au/event-5815368?CalendarViewType=1&SelectedDate=8/7/2024). The slides are available here.

## Introduction

This presentation is a follow-up to my last seminar on the relevance of software engineering best practices in statistics and data science (watch the [recording](https://www.youtube.com/watch?v=e5P-OljGjO0) or see the [slides](https://nzsa-ssa-seminar-2023-slides.netlify.app/#/title-slide)). This first talk gave an overview of best practices, and I wanted to show how I apply them in my day-to-day job. So in this post, I will walk you through how I set up a typical analysis project in R. Most of the time, the projects that I work on involve:

- receiving a dataset from the stakeholders

- cleaning the data

- performing an exploratory data analysis, usually via the constructions of several plots

- fitting a model / running a main analysis

- generating a report for the stakeholders.

For this example, we will work on analysing the [palmerpenguins](https://allisonhorst.github.io/palmerpenguins/) dataset, which can be found in the package of the same name. It consists of a collection of size measurements for three penguin species observed in the Palmer Archipelago in Antarctica. 

## Setting up

### Creating a new GitHub repository

The very first thing that I do, when I start working on the project, is to create a GitHub repository that will host all of the code I generate for the project. There are many ways to do this, including:

- creating a new repository online from [github.com](https://github.com/)

- creating a new folder locally, then pushing it online

- using a GitHub GUI such as [GitHub Desktop](https://github.com/apps/desktop)

If you are confused or not sure how to proceed, I recommend going through chapters 14 to 16 of the [Happy Git and GitHub for the useR](https://happygitwithr.com/usage-intro) book.

My preferred method is to create the repository online. I try to pick an informative name for the repository, add a brief description (if your workplace uses project codes, that is the ideal place to add it), and add a README file. I also like to use the `.gitignore` template for R, although it is not necessary as it will be generated in a following step.

![Creating a new repository on github.com.](images/01_creating_github_repo.gif)


### Cloning the repository

Once this is done, I need to clone the repository to my machine, to have a local copy on which I can work. Again, there are several ways to do so, and I will only demonstrate one of them. Note that for this step, it is essential to have your git credentials sorted -- I recommend reading the [git credentials vignette](https://usethis.r-lib.org/articles/git-credentials.html) from `{usethis}` or the Danielle Navarro's [blog post on git credentials helpers](https://blog.djnavarro.net/posts/2021-08-08_git-credential-helpers/) (which is aimed at Linux users, but was very insightful nevertheless!).

I like to use the `create_from_github()` function from the `{usethis}` package. The advantage is that it does a couple of things automatically:

- it clones the repository to a directory of my choice (specified through the `destdir` argument)

- it turns the cloned repository into an Rstudio project (by adding a `.Rproj` file in the root directory)

- it creates/updates the `.gitignore` file to ignore relevant R files

- it then opens the newly created project in a separate RStudio session

The command should be run from RStudio (I like to close the current project and run it outside of any project).

![Cloning a GitHub repository with `usethis::create_from_github()`.](images/02_cloning_repo.gif)


### Initialising `{renv}`

Next, I initialise the `{renv}` package to use in the project. `{renv}` helps to create reproducible environments for R projects by:

- recording which packages and which version of these packages are used in the project

- isolating the packages library used in the project from the rest of the computer.

The set-up is extremely simple -- it is simply a matter of running the `init()` function from `renv()`, which does two things:

- it creates a `renv.lock` file, which will be where all the packages dependencies of the projects will be recorded

- it creates an `renv/` folder, inside which the packages that are used in the project will be installed. This way, you can install any version of any package that you want, without affecting the other projects on your computer.

The `renv.lock` file will be tracked on GitHub, as it is the recipe that allows others to reproduce the computational environment that I am using for the project. The `renv/` folder on the contrary comes with its own `.gitignore` file, as we don't want our package library to end up on GitHub.

![Setting-up `{renv}` with `renv::init()`.](images/03_renv_init.gif)


The `init()` command should be run once when you set-up the project. After that, there are four commands that you will use. First, when you want to install a package, use `renv::install("tidyverse")`. They will be installed in the project library (i.e. somewhere in the `renv/` folder). Then, before every commit, you can check that the lock file (which records the dependencies of your project) is up-to-date, by running `renv::status()`. This will let you know whether you have added new packages to your scripts (or removed some) since you last recorded your computational environment. If the lock file is not up to date, you can record the changes by running `renv::snapshot()`. Finally, if a collaborator wants to install the packages that you used (with the appropriate version), after cloning your repository, they can run `renv::restore()`, which will use the lock file to guide which packages should be installed.

### Setting up the directory structure

My project directories always follow the same organisation -- this makes it easier for people to understand the layout and to find the necessary information. I use the following set of folders:

- `data/`: where the data necessary for the project will live (if using local version of data)
- `analysis/`: where to put the analysis scripts
- `R/`: where to put scripts with helper functions
- `output/`: where to save generated tables, figures, etc.
- `reports/`: where to put the files that will be used to generate reports

If you are interested in the topic of how to organise data science computational projects in R, I recommend you check out [Marwick et al. (2017)](https://doi.org/10.1080/00031305.2017.1375986) "Packaging Data Analytical Work Reproducibly Using R (and Friends)" and the associated `{rrtools}` package.

![Very useful and interesting gif of me creating the folders with RStudio.](images/supp_04_creating_folders.gif)

Once this is done, and I've copied a version of the palmerpenguins dataset in the `data/` folder, here is what my directory looks like:

```{.markdown}
.
├── .git/
│   └── ...
├── .Rproj.user/
│   └── ...
├── renv/
│   └── ...
├── data/
│   └── penguins_raw.csv
├── analysis/
├── R/
├── reports/
├── output/
├── README.md
├── renv.lock
├── palmerpenguins_analysis.Rproj
├── .Rprofile
└── .gitignore
```

### Populating the README

A key aspect of making our work reproducible is to have sufficient documentation. A part of this is having a good README, which allows users discovering your code in the future to have a good overview of the project. As with the directory structure, I like to follow a consistent format for my README file. In particular, I make sure to record the following information:

- a brief **description of the project**: context, overall aim, some key information about the experiment and the analyses intended/performed.

- a list of **key contributors** and their roles, as bullet points.

- information about the **input data** (where it is stored), who provided it, what it contains.

- what **analyses** were performed, and which files were generated as a result (e.g. cleaned version of the data).

- a list of the **key folders and files**.

- which sets of commands to run if you want to **reproduce the analysis**.

For example, the README file for this project will look something like:

````{.markdown filename="README.md"}
# Analysis of penguins measurements

This project aims at understanding the differences between the size of three species of
penguins (Adelie, Chinstrap and Gentoo) observed in the Palmer Archipelago, Antarctica.
The data was collected by Dr Kristen Gorman between 2007 and 2009.

## Key contributors

- Jane Doe: project leader

- Kristen Gorman: data collection

- Olivia Angelin-Bonnet: data analysis

## Input data

The raw penguins measurements, stored in the `penguins_raw.csv` file, were downloaded from the 
[`palmerpenguins` package](https://allisonhorst.github.io/palmerpenguins/index.html).

Data source: [...]

## Analysis

- Data cleaning: a cleaned version of the dataset was saved as `penguins_cleaned.csv` on
[OneDrive](path/to/OneDrive/folder).

- *To be filled*

## Repository content

- `renv.lock`: list of packages used in the analysis (and their version)
- `analysis/`: collection of `R` scripts containing the analysis code
- `R/`: collection of `R` scripts containing helper functions used for the analysis
- `reports/`: collection of `Quarto` documents used to generate the reports

## How to reproduce the analysis

```r
# Install the necessary packages
renv::restore()
```
````

Note that I didn't fill all of the sections right away; for example the Analysis section will be filled as I add more to my analysis.

### Commit and Push

Now that the set-up is complete, I like to make a first commit, and then push my changes. This ensures that any modifications I make afterwards (typically get started on the work!) will have its own dedicated commit. I like to use the RStudio interface for that.

![Commit and push changes on GitHub via the RStudio Git panel](images/05_commit_push.gif)
 
## After I've started coding

Once the set-up is complete, it is time for the fun stuff: read in the data and start coding! I generally start by creating an R script called `first_script.R` (or `temp.R`) in the `analysis/` folder, and start playing with the data there. I usually get to the data cleaning stage, and once I have a vague idea of what I need to do there, I stop and tidy up (what we will do in the next sections). For example, I might get to this point:


``` r
library(tidyverse)
library(janitor)
library(ggbeeswarm)
library(here)

# Reading and cleaning data
penguins_df <- read_csv(here("data/penguins_raw.csv"), show_col_types = FALSE) |> 
  clean_names() |> 
  mutate(
    species = word(species, 1),
    year = year(date_egg),
    sex = str_to_lower(sex),
    year = as.integer(year),
    body_mass_g = as.integer(body_mass_g),
    across(where(is.character), as.factor)
  ) |> 
  select(
    species,
    island,
    year,
    sex,
    body_mass_g,
    bill_length_mm = culmen_length_mm,
    bill_depth_mm = culmen_depth_mm,
    flipper_length_mm
  ) |> 
  drop_na()

## Violin plot of body mass per species and sex
penguins_df |> 
  ggplot(aes(x = species, colour = sex, fill = sex, y = body_mass_g)) +
  geom_violin(alpha = 0.3, scale = "width") +
  geom_quasirandom(dodge.width = 0.9) +
  scale_colour_brewer(palette = "Set1") +
  scale_fill_brewer(palette = "Set1") +
  theme_minimal()

## Violin plot of flipper length per species and sex
penguins_df |> 
  ggplot(aes(x = species, colour = sex, fill = sex, y = flipper_length_mm)) +
  geom_violin(alpha = 0.3, scale = "width") +
  geom_quasirandom(dodge.width = 0.9) +
  scale_colour_brewer(palette = "Set1") +
  scale_fill_brewer(palette = "Set1") +
  theme_minimal()

## Scatter plot of bill length vs depth, with species and sex
penguins_df |> 
  ggplot(aes(x = bill_length_mm, y = bill_depth_mm, colour = species, shape = sex)) +
  geom_point() +
  scale_colour_brewer(palette = "Set2") +
  theme_minimal()
```

This is a good start, but often, the analyses that I do involve a lot more than generating a few plots, and as a result, the R script would become long and convoluted. This would make it much harder to understand what is happing in the script, and to find specific commands that generate a particular plot, for example. In addition, if I make some changes to a part of my analysis, I don't want to have to re-execute the entire script -- imagine if some of the steps take hours to run, we don't want to do that every time we change the colours in a plot!

This is why I started using the amazing `{targets}` package. Rather than explaining what the package does (which is already done very well in the [`{targets} manual`](https://books.ropensci.org/targets/)), I will show how I use it.

### Turn your code into functions

The first step to converting your plain R scripts into `{targets}` pipelines is to create functions.

### Turn your script into a `{targets}` pipeline

### Visualise your pipeline

### Execute your pipeline

### Extract the pipeline results

### Change in a pipeline step

### Change in the data -- using `{assertr}` for data checking

### Writing a report with Quarto and `{targets}`

### How to reproduce the analysis

### Conclusion
