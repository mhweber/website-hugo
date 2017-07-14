---
title: Wordcloud
author: Marc Weber
date: '2017-07-13'
slug: wordcloud
categories:
  - Python
tags:
  - Python
  - wordcloud
subtitle: ''
---

I've been toying with building a wordcloud of all my publications in both R and python on and off for some time, and while there's a really nice R library for doing this [wordcloud2](https://cran.r-project.org/web/packages/wordcloud2/vignettes/wordcloud.html), this example uses [wordcloud](https://github.com/amueller/word_cloud), a python word cloud generator.

I pasted all my publications into a single text file which is read into python and used by wordcloud as shown in code and results below.


```python
from os import path
from wordcloud import WordCloud

# Read the whole text.
text = open('J:/GitProjects/WordCloud/AllPapers.txt').read()

# Generate a word cloud image
wordcloud = WordCloud().generate(text)

# Display the generated image:
# the matplotlib way:
import matplotlib.pyplot as plt
#plt.imshow(wordcloud, interpolation='bilinear')
plt.axis("off")
figsize(14, 10)
# lower max_font_size
wordcloud = WordCloud(max_font_size=40).generate(text)
plt.figure()
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis("off")
plt.show()
```

![](/img/wordcloud.png)

A basic wordcloud, but pretty cool!  