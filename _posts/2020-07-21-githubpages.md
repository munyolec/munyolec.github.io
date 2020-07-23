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
To get twitter data  you will need to create a twitter developer account and create a new app. From here you will be able to get the credentials that will enable you access the twitter API.
```python
!pip install tweepy
import tweepy
from tweepy import OAuthHandler
from tweepy import API
from tweepy import Cursor
from datetime import datetime, date, time, timedelta
from collections import Counter
```
import BeautifulSoup which will be used to extract data from html
``` python
from bs4 import BeautifulSoup #beautifulsoup extracts data from html files
```
You then need to set your twitter credentials as below:
```python
consumer_key=''
consumer_secret_key=''
access_token=''
access_token_secret=''
```
The next step is to autenticate the credentials and create an instance of the tweepy API class
```python
auth = OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
auth_api = API(auth)
```
I then wrote the method I would use to scrap data from the website from a particular html class
```python
def simple_get(url):
    """
    Attempts to get the content at `url` by making an HTTP GET request.
    If the content-type of response is some kind of HTML/XML, return the
    text content, otherwise return None.
    """
    try:
        with closing(get(url, stream=True)) as resp:
            if is_good_response(resp):
                return resp.content  #.encode(BeautifulSoup.original_encoding)
            else:
                return None

    except RequestException as e:
        log_error('Error during requests to {0} : {1}'.format(url, str(e)))
        return None


def is_good_response(resp):
    """
    Returns True if the response seems to be HTML, False otherwise.
    """
    content_type = resp.headers['Content-Type'].lower()
    return (resp.status_code == 200 
            and content_type is not None 
            and content_type.find('html') > -1)


def log_error(e):
    """
    It is always a good idea to log errors. 
    This function just prints them, but you can
    make it do anything.
    """
    print(e)
    
def get_elements(url, tag='',search={}, fname=None):
    """
    Downloads a page specified by the url parameter
    and returns a list of strings, one per tag element
    """
    
    if isinstance(url,str):
        response = simple_get(url)
    else:
        #if already it is a loaded html page
        response = url

    if response is not None:
        html = BeautifulSoup(response, 'html.parser')
        
        res = []
        if tag:    
            for li in html.select(tag):
                for name in li.text.split('\n'):
                    if len(name) > 0:
                        res.append(name.strip())
                       
                
        if search:
            soup = html            
            
            
            r = ''
            if 'find' in search.keys():
                print('findaing',search['find'])
                soup = soup.find(**search['find'])
                r = soup

                
            if 'find_all' in search.keys():
                print('finding all of',search['find_all'])
                r = soup.find_all(**search['find_all'])
   
            if r:
                for x in list(r):
                    if len(x) > 0:
                        res.extend(x)
            
        return res

    # Raise an exception if we failed to get any data from the url
    raise Exception('Error retrieving contents at {}'.format(url))    
    
    
if get_ipython().__class__.__name__ == '__main__':
    fire(get_tag_elements
```    
I passed the url to the method called simple_get(url) to get my dataset and specified the class that i wanted to get the names from which was 'twitter-tweet'
```python
url=('https://africafreak.com/100-most-influential-twitter-users-in-africa')
response=simple_get(url)
res=get_elements(response, tag='h2', search={'find_all': {'class_': 'twitter-tweet'}})
```
