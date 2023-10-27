---
title: sismonr
subtitle: "Simulation of In-Silico Multi-Omics Networks in R"
excerpt: |
  An R package for generating and simulating in silico gene regulatory networks.
author: Olivia Angelin-Bonnet
date: "2018-09-12"
categories:
- R-package
- GRN-simulation
draft: false
featured: true
layout: single #-sidebar
links:
- icon: github
  icon_pack: fab
  name: Github repository
  url: https://github.com/oliviaAB/sismonr
- icon: book
  icon_pack: fas
  name: Documentation
  url: https://oliviaab.github.io/sismonr/
- icon: laptop
  icon_pack: fas
  name: Workshop material
  url: https://genomicsaotearoa.github.io/Gene_Regulatory_Networks_Simulation_Workshop/
- icon: paperclip
  icon_pack: fas
  name: Publication
  url: https://doi.org/10.1093/bioinformatics/btaa002
- icon: youtube
  icon_pack: fab
  name: Presentation
  url: https://www.youtube.com/watch?v=ydeeOlGOC4U&list=PLYc7-GqWDJu2N09Y--9hej5BfFLjzcLHu&index=1&pp=gAQBiAQB
---

`sismonr` is an R package that I developed during my PhD for the simulation of gene expression profiles for in silico regulatory systems. Some innovative features of the model include:

- Simulation of protein-coding and noncoding genes
- Simulation of transcriptional and post-transcriptional regulation
- Simulation of the ploidy (number of gene copies) of the system

One originality of this package is that it relies on Julia code "under the hood" to speed up the computations. However, the user does not need to interact with Julia in order to use the package.

The package is publicly available on [GitHub](https://github.com/oliviaAB/sismonr), and has been published in [Bioinformatics](/publication/2020-sismonr-bioinformatics/). In addition, I developed together with [NeSI](https://www.nesi.org.nz/) a [two-day workshop](https://genomicsaotearoa.github.io/Gene_Regulatory_Networks_Simulation_Workshop/) based on `sismonr`. I also had the chance to give a talk on `sismonr` at one of NESI's webinar, the recording is available on [Youtube](https://www.youtube.com/watch?v=ydeeOlGOC4U&list=PLYc7-GqWDJu2N09Y--9hej5BfFLjzcLHu&index=1&t=1s&pp=gAQBiAQB).
