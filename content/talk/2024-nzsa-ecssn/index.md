---
title: |
  Setting up a reproducible data analysis project in R - featuring GitHub, {renv}, {targets} and more
excerpt: A walk-through of my setup for a typical analysis project in R, using best practices.
author: Olivia Angelin-Bonnet
event: SSA & ECSSN Joint Webinar Series
event_url: https://www.statsoc.org.au/event-5815368
date: "2024-08-20"
location: "online"
categories:
- conference
- best-practices
- R
draft: false
featured: true
layout: single
show_post_time: false

links:
#- icon: play-circle
#  icon_pack: fas
#  name: Recording
#  url: https://www.youtube.com/watch?v=e5P-OljGjO0
- icon: paperclip
  icon_pack: fas
  name: Slides
  url: https://nzsa-ecssn-2024-seminar-slides.netlify.app/#/title-slide
- icon: door-open
  icon_pack: fas
  name: Event info
  url: https://www.statsoc.org.au/event-5815368
- icon: newspaper
  icon_pack: fas
  name: Blog post
  url: https://www.youtube.com/watch?v=e5P-OljGjO0
---

## Abstrat

This talk is a follow up to [last year’s presentation](https://olivia-angelin-bonnet.netlify.app/talk/2023-nzsa-ssa/) on the relevance of software engineering best practices in statistics and data science. Here, I will go through how I setup a new data analysis project in R, following some of these best practices to ensure reproducibility and reusability. In particular, I will demonstrate how I use GitHub to version control the code, and how I organise a typical analysis directory. I will also showcase some very useful R packages, such as {renv} to document the computational environment and {targets} to turn the scripts into a reproducible analytical pipeline.

