---
title: Twitter Analysis Post"
data: 2020-07-21
tags: [data science, tweepy, twitter analysis]
header:
    image: "/images/perceptron/percept.jpg"
exceprt: "........"
mathjax: "true"
---

# Twitter Analysis of Influential People and Top Governement Leaders 
I recently did an analysis to identify top social media influencers in Africa for a digital campaign.
The purpose was to identify which of these influencers a company can partner with or hire to amplify 
their planned digital campaign. It was also  to identify the topics that are important to these influencers.
## Approach
The list of top 100 African influencers were scrapped from a website called [100 most influential Twitter users in Africa](https://africafreak.com/100-most-influential-twitter-users-in-africa). I used Beautiful Soup to Scrap the twitter handles
from the website. I then manipulated and cleaned the data using Python Panda's library.

### Using Beautiful Soup to perform web scrapping.
It is good practise to start by importing the necessary modules that you will need for this task
namely; pandas, numoy, matplotlib and seaborn. The line %matplotlib inline is used to easily 
display the plots that you will create.

``` python
pip install lxml
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```
For webscrapping, an important library called urllib.request is required to open URLs. BeautifulSoup is used
to extract data from HTML files.
``` python
from urllib.request import urlopen #urllib opens urls
from bs4 import BeautifulSoup #beautifulsoup extracts data from html files
```
