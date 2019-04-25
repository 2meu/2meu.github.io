---
title: "[Crawling] youtube crawling only using python without API"
date: 2019-03-12
categories: [Crawling]
---

## Youtube Crawling

### BeautifulSoup 공부

-  2 methods to read web source
```python
from bs4 import BeautifulSoup
import urllib.request as req
import requests
url = 'https://finance.naver.com/marketindex/'
res = requests.get(url).text
res = req.urlopen(url)
```


- find: find() 메서드는 객체를 사용해 여러 개의 조건을 한 번에 지정할 수 있다는 것이 특징

```python
#ex-1)
soup.find('any tag names ex) div, h1, a...)
soup.find(id="megen")
soup.find(class_="items") - cause of class is 예약어

#ex-2)
cond = {'data-lo':'us', 'class':'black'}
print( soup.find("li", cond).string ) - 한번에
print( soup.find(id="ve-list").find("li", cond).string ) - 연속

#ex-find_all)
links = soup.find_all('li') - all "li" tags come together in the list
for a in links:
  href = a.attrs['href']
  text = a.string
```


- CSS 선택자 사용: '>'를 이용해 순차적으로 tag를 찾아갈 수 있다. - select, select_one

`text = soup.select("div#meigen > ul.items > li")`

### code

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

## Q. BeautifulSoup를 이용해 parsing한 html.text와 원본 사이트의 html sauce와 다른가?

다르다! 요즘 웹사이트들은 자바스크립트를 이용해 로드된 페이지에 동적으로 데이터를 추가 로드해 변형시키는 방싱이 많다보니 requests처럼 페이지 소스만을 받아오는 경우와는 다른 점이 많다. 원하는 정보가 위치하고 있는 부분이 JavaScript로 추가된 곳이라면 requests로 받은 리스폰에선 그 내용이 없다.
ex) channel 의 playlist창을 띄우면 눈으로 보는 약 30개의 동영상만이 parsing되고 아직 눈으로 보이지 않는 밑의 수많은 동영상들은 파싱되지 않는다.

  셀레니움을 쓰자!
  셀레니움장점 = ['직관적이다', '맘 편하게 손 가는 대로 크롤링해올 수 있다', '덜 복잡하다. 그나마']
  셀레니움단점 = ['느리다', '컴퓨터 자원을 많이 먹는다', '다중 작업에 취약하다']
  requests장점 = ['빠르다', '리소스를 적게 먹는다']
  requests단점 = ['JS가 있으면 골치아파진다.', '코드가 길어진다', '페이지 스타일이 변하면 다시 짜줘야 한다']

## 결론
  *최근 웹 페이지와 youtube 페이지는 html/css 만이 아니라 자바스크립트로 동적으로
  움직이기에 python으로만 크롤링 하기엔 비효율적이며 자바스크립트와 유튜브 자체
  API를 통해 크롤링 하는게 효율적이다.*
