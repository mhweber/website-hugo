---
title: Trying Out Bookdown and Publishing to GitHub Pages
tags: ['bookdown','R Markdown','R','GitHub']
categories: ['R']
date: 2020-02-12
---

I finally decided to try creating a bookdown project and see if I could figure out hosting it on GitHub Pages.  The steps I followed were:

1. Create a new empty GitHub repo on GitHub without a readme

2. Create a new local R Project in a new directory as project type Book Project using bookdown

3. Initialize a .git repository for the project after it is created

4. Using code snippets on the GitHub repo you just created (initial page that shows up when you create the empty repository):

  - add the remote to your repository in git bash
  
  - push repo to remote
  
5. Update the index.Rmd with my info and make initial commit

6. Push initial commit

7. In order to publish easily to Github pages, I followed steps suggest at end [here](https://community.rstudio.com/t/hosting-bookdown-in-github/20427/7):

  - Configure source for GH pages as the master branch /docs folder in GitHub settings for the repo
  
    + In the _bookdown.yml file add a new line at end: output_dir: "docs"
    
    + Build book locally
    
    + Push repo to GitHub, then go to the URL shown in your GitHub settings page 
    

The final [Book Example](https://mhweber.github.io/AWRA_2020_R_Spatial/).
