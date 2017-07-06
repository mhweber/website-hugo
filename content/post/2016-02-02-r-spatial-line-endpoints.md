---
title: R extract line endpoints
tags: ['R','spatial data','maptools']
categories: ['R']
date: 2016-02-02
---

Extracting line end nodes in R

I noticed the [maptools](https://cran.r-project.org/web/packages/maptools/index.html) package in [R](https://www.r-project.org/) had a SpatialLinesMidPoints function but couldn't find any other out of the box functions in any packages to extract line endpoints.  So I modified SpatialLinesMidPoints slightly to do the job:

{{< gist mhweber d0e16ae9ff9beee798ea >}}
