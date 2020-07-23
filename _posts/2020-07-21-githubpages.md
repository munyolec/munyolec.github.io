---
title: Twitter Analysis of Influential People and Top Governement Leaders 
data: 2020-07-21
tags: [data science, tweepy, twitter analysis]
header:
    #image: "/images/perceptron/percept.jpg"
exceprt: "........"
mathjax: "true"
---
I recently did an analysis to identify top social media influencers in Africa for a digital campaign.
The purpose was to identify which of these influencers a company can partner with or hire to amplify 
their planned digital campaign. It was also  to identify the topics that are important to these influencers.
## Approach
The list of top 100 African influencers were scrapped from a website called [100 most influential Twitter users in Africa](https://africafreak.com/100-most-influential-twitter-users-in-africa). I used Beautiful Soup to Scrap the twitter handles
from the website. I then manipulated and cleaned the data using Python Panda's library.

It is good practise to start by importing the necessary modules that you will need for this task
namely; pandas, numpy, matplotlib and seaborn. The line %matplotlib inline is used to easily 
display the plots that you will create.

``` python
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
I passed the url to the method called simple_get(url) to get my dataset and specified the html class attribute that i wanted to get the names from which was 'twitter-tweet'. I used BeautifulSoup to extract the text without the html tags.
```python
url=('https://africafreak.com/100-most-influential-twitter-users-in-africa')
response=simple_get(url)
res=get_elements(response, tag='h2', search={'find_all': {'class_': 'twitter-tweet'}})
str_cells=str(res)
cleantext2=BeautifulSoup(str_cells,"lxml").get_text()
```
The code below generates an empty list, extracts the text in the html tags and appends it to the list. For this particular project I used regex to extract text that had the @ sign, which would contain the twitter handles I was looking for. I really struggle with regex so I used the [RegExr](https://regexr.com/) website to guide me on this. This required importing the re(for regular expressions) module. I then stored my data in a pandas dataframe.
```python
dataa=[]
for item in res:
    if '(@' in item:
        cleant = re.compile(r'@(\w*)')
        cleant2=(re.search(cleant,item))        
        if cleant2.group(0) is not None:
            dataa.append({'value': cleant2.group(0) })        
df=pd.DataFrame(dataa)
df.columns = ["Top 10 African Influencers twitter Handles"]
df1=df.sort_index(ascending=True)

users= []
index=0
while index < 100:
    users.append(df2.iat[index,0])
    index=index+1
```
I then used the tweepy API to get twitter data such as number of followers and total number of retweets and likes for each user. From there I calculated popularity score by summing total number of retweets and likes, reach score was calculated by getting the difference between the number of followers and number of friends. From this data one can choose to rank an influencer either using number of followers, reach score or popularity score. 
```python 
my_demo=[]
for target in users:
    try: #catch an error incase the twitter username doesnot exist
        tweets1 = auth_api.user_timeline(screen_name=leader1 ,count=5)#recent 5 tweets
        tweet_list1 = [tweet1 for tweet1 in tweets1]     
        item_11 = auth_api.get_user(leader1)
        name1=item_11.screen_name
        followers1=item_11.followers_count
        friends1=item_11.friends_count
        tweets1=item_11.statuses_count  
        reach1=followers1-friends1
        account_age_days1=delta1.days
        counter1 = 0              #initialize retweet counter to zero
        favcounter1= 0        #initialize like counter to zero
        rtcounter1=0
        ftcounter1=0
        for tweet1 in tweet_list1:    #for each tweet in the tweet list
            retweetcount1 = tweet1.retweet_count #get the retweet count of that tweet
            rtcounter1+=retweetcount1 
            favorite1 = tweet1.favorite_count  #get the likes of that tweet
            ftcounter1+=favorite1
            counter1 += retweetcount1 #updating total number of retweets
            favcounter1 += favorite1  #updating total number of likes
            popularity_score1 = rtcounter1 + ftcounter1 
      except tweepy.TweepError :
     pass
```
I also checked for the common hashtags used by these influential people and I was able to do that using the following method. 
```python
def get_tweet_details(username):
  hashtags = []
  mentions = []
  tweet_count = 0
  end_date = datetime.utcnow() - timedelta(days=30)
  for status in Cursor(auth_api.user_timeline, id=username).items():
    tweet_count += 1
    if hasattr(status, "entities"):
      entities = status.entities
      if "hashtags" in entities:
        for ent in entities["hashtags"]:
          if ent is not None:
            if "text" in ent:
              hashtag = ent["text"]
              if hashtag is not None:
                hashtags.append(hashtag)
      if "user_mentions" in entities:
        for ent in entities["user_mentions"]:
          if ent is not None:
            if "screen_name" in ent:
              name = ent["screen_name"]
              if name is not None:
                mentions.append(name)
      if status.created_at < end_date        break
  return {
      'hashtags': hashtags,
      'mentions': mentions,
      'count': tweet_count
  }
  
  
  def get_hashtags(user):
  try:
    hashuser = auth_api.get_user(user)
    user_name=hashuser.screen_name
    #print("printing user details for "+user)
    details=get_tweet_details(user)
    hash=Counter(details['hashtags']).most_common(20)
    return {'user_name': user, 'hashtags': hash}
  except tweepy.TweepError:
    #print('Treepy Error!!')
    return 
  
def arrange_user_hashtags():
  user_hashtags=[]
  for user in users:
    hash=get_hashtags(user)
    user_hashtags.append(hash)
  return user_hashtags

user_details=arrange_user_hashtags()
```
