---
title: "유투브 크롤링을 해보자"
date: 2019-03-12
categories: Youtube Crawling
---

### Youtube Crawling
---

```python
from bs4 import BeautifulSoup
import lxml
import requests

eminem_video_info = {
    'title':'',
    'video_link':'',
    'img_link':'',
    'play_time':'',
    'hits' : '',
    'updated_time':''
}

def get_eminem_video_link(target_url):
    response = requests.get(target_url)
    soup = BeautifulSoup(response.text, "lxml")
    lis = soup.find_all('li', {'class' : 'channels-content-item yt-shelf-grid-item'})
    for li in lis :
        title = li.find('a', {'title' : True})['title']
        video_link = 'https://www.youtube.com' + li.find('a', {'href' : True})['href']
        img_link = li.find('img', {'src' : True})['src']
        play_time = li.find('span', {'class' : 'video-time'}).text
        hits = li.find_all('li')[2].text
        updated_time = li.find_all('li')[3].text
        eminem_video_info = {
            'title' : title,
            'video_link' : video_link,
            'img_link' : img_link,
            'play_time' : play_time,
            'hits' : hits,
            'updated_time' : updated_time
            }
        print(eminem_video_info)
    return eminem_video_info

target_url = 'https://www.youtube.com/channel/UCc4OuY3PeWklqBzQ3ySi3xA/videos'
get_eminem_video_link(target_url)

```
