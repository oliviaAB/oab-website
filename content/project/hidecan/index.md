---
title: hidecan
subtitle: "Generating hidecan plots for GWAS and differential expression results visualisation"
excerpt: |
  An R package for generating HIDECAN plots, which are visualisations summarising the results of one or more Genome-wide association study (GWAS) and transcriptomics differential expression (DE) analysis, alongside candidate genes of interest.
author: Olivia Angelin-Bonnet
date: "2023-02-15"
categories:
- R-package
- visualisation
draft: false
featured: true
layout: single #-sidebar
links:
- icon: code
  icon_pack: fas
  name: CRAN package
  url: https://cran.rstudio.com/web/packages/hidecan/index.html
- icon: github
  icon_pack: fab
  name: Github repository
  url: https://github.com/PlantandFoodResearch/hidecan
- icon: book
  icon_pack: fas
  name: Documentation
  url: https://plantandfoodresearch.github.io/hidecan/
- icon: paperclip
  icon_pack: fas
  name: Publication
  url: https://www.biorxiv.org/content/10.1101/2023.03.30.535015v1
- icon: youtube
  icon_pack: fab
  name: Presentation
  url: https://www.youtube.com/watch?v=FoOEV_bjP0w&list=PLYc7-GqWDJu2N09Y--9hej5BfFLjzcLHu&index=3&pp=gAQBiAQB
---

`hidecan` is an R package that I developed following the publication of my PhD work on the mechanisms of tuber bruising in the tetraploid potato. In this paper, we proposed a new visualisation to present in one single plot results from genome-wide associations studies (GWAS), differential expression (DE) alongside manually curated lists of genes of interest (e.g. candidate genes mined from prior research). We named this new visualisation HIDECAN plots (which stands for HIgh-scoring markers, Differentially Expressed genes and CANdidate genes). The hidecan package follows from this work, by providing a way for users to generate their own HIDECAN plots from their GWAS and DE results. The package offers a suite of functions to easily go from tables of results or of genes of interest to the plot. The package also offers a shiny app that users can use to import their data and generate a plot interactively rather than through code. Lastly, we also offer a helper function to extract necessary information from the results of the [GWASpoly](https://github.com/jendelman/GWASpoly) package (for GWAS in autotetraploid organisms) into a format that can hidecan accepts. The goal is to facilitate the use of hidecan at the end of an analysis pipeline without having to spend too much time on results formatting.

The package is publicly available on the [CRAN](https://cran.rstudio.com/web/packages/hidecan/index.html) and on [GitHub]( https://plantandfoodresearch.github.io/hidecan/), and a [preprint](https://www.biorxiv.org/content/10.1101/2023.03.30.535015v1) is available on BioRxiv. I gave a presentation on hidecan during the 2023 Tools for Polyploids workshop, the recording is available on [Youtube](https://www.youtube.com/watch?v=FoOEV_bjP0w&list=PLYc7-GqWDJu2N09Y--9hej5BfFLjzcLHu&index=3&pp=gAQBiAQB).
