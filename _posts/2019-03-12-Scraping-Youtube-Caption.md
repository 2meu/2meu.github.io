---
title: "[스크레이핑 - 유튜브] Youtube에서 caption 스크레이핑하기"
date: 2019-03-12
---

## closed caption과 subtitles의 차이점

- subtitle은 대화만 자막으로 표시해준다.
- closed caption은 대화뿐만 아니라 배경 소리, noise까지 모두 자막으로 표시해준다.
- subtitle은 소리는 들리지만 자막이 필요한 사람에게 필요하지만 closed caption은
들리지 않은 사람에게 배경의 소리까지 전해주기위해 필요하다.

## youtube에서 caption을 다운로드 받는 방법 2가지

### Google API를 이용

Google API captions - list에 들어가 HTTP request 정보를 얻는다.

```
https://www.googleapis.com/youtube/v3/captions
?part=snippet
&videoId=2zR1GOLhlU4
&key=AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0
```


- part: snippet
- id: videoId=2zR1GOLhlU4
- key: AIzaSyBt3aTnFJz9zvryJqtHe3wq7hNzjJtjK0

```json
{
 "kind": "youtube#captionListResponse",
 "etag": "\"XpPGQXPnxQJhLgs6enD_n8JR4Qk/ytZKLiDxg4Lk8HvM3aWM7mCpn2w\"",
 "items": [
  {
   "kind": "youtube#caption",
   "etag": "\"XpPGQXPnxQJhLgs6enD_n8JR4Qk/HJ39-ILa_B84Y4-r5gumvMbtmGU\"",
   "id": "51pKUI6fCEIbI7guTPyY0UBuGVLqTdai7gHS2fJbZgU=",
   "snippet": {
    "videoId": "2zR1GOLhlU4",
    "lastUpdated": "2019-03-01T18:37:55.966Z",
    "trackKind": "ASR",
    "language": "en",
    "name": "",
    "audioTrackType": "unknown",
    "isCC": false,
    "isLarge": false,
    "isEasyReader": false,
    "isDraft": false,
    "isAutoSynced": false,
    "status": "serving"
   }
  }
 ]
}
```

자막을 얻으려고 했더니 자막은 나오지 않고 이상한 정보만 나온다.. 정보에 대해서 찾아보자

*(+YouTube's API has something about captions, but only for registered users)*


- snippet.tracekind
  - ASR: A caption track generated using automatic speech recognition.
  - forced: A caption track that plays when no other track is selected
  in the player. For example, a video that shows aliens speaking in an
  alien language might have a forced caption track to only show subtitles
  for the alien language.
  - standard: A regular caption track. This is the default value.


... 별 내용이 없다. 왼쪽에 captions 목록에 download로 가보자.

![image](https://user-images.githubusercontent.com/48308562/54335507-88614880-466c-11e9-8cd0-b3e4a678bd04.png)

caption list에서 얻은 id를 통해서 실행해보라고 한다.


[link](https://www.googleapis.com/youtube/v3/captions/id=51pKUI6fCEIbI7guTPyY0UBuGVLqTdai7gHS2fJbZgU=&key=AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0)


.. `Login required`이라고 보여진다.동영상에 자막에 접근하려면 그 동영상의 주인만
이용할 수 있는 것인가?.. 구글링을 해봐도 이 문제에 대해서 찾을 수 없었다. 그래도
언제나 그렇듯 구글신은 답을 주었다.


![image](https://user-images.githubusercontent.com/48308562/54347895-472c6100-468b-11e9-85f0-80ffe2b4b825.png)

youtube-trascript-api 0.1.3 이라는 꽤 새로나온 것 같은 라이브러리가 존재했다.

바로 실행해보자.

![image](https://user-images.githubusercontent.com/48308562/54348295-14cf3380-468c-11e9-930e-c563a8d68688.png)

정말 쉽게 얻었다.

앞으로는 이 라이브러리를 사용해야겠다.

```python
pip install youtube_transcript_api
# 명령어 conda는 실행이 안된다. 이유는 모르겠음.

from youtube_transcript_api import YouTubeTranscriptApi
YouTubeTranscriptApi.get_transcript("6NqKrhmnVNY")
```


### python 이용

- 고급스크레이핑 + 셀레니움 공부하자no

셀레니움 공부 하고 밑에 코드 뜯어서 공부하기!

```python
import time
from bs4 import BeautifulSoup
import urllib.request
from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
import re
import sys

# Clicks on "Load More" button to display all users videos.
def display_all_videos(driver):
    while(True):
        try:
            element = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.CLASS_NAME, "yt-uix-load-more")))
            element.click()
        except:
            break

# Creates a list of tuples (video_title, video_link) of all
# videos displayed on page
def video_list(driver):
    videos = driver.find_elements_by_class_name("yt-uix-tile-link")
    video_links = []
    for vid in videos:
        video_links.append((vid.text,vid.get_attribute("href")))
    return video_links

# Clicks on CC(Closed Caption) button in YouTube video
def enable_subtitles(driver):
    elem = WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.CLASS_NAME, "ytp-subtitles-button")))
    elem.click()

def subtitles_link(driver):
    time.sleep(1)
    timings = driver.execute_script("return window.performance.getEntries();")

    # Find string in timings that contains the substring 'srv3'
    # which is the subtitles link.
    link = ""
    for t in timings:
         for v in t.values():
             if "srv3" in str(v):
                 link = v
    return link

def create_file(title, link,subtitles):
    # remove illegal chars for file name
    title = "".join([c for c in filename if c.isalpha() or c.isdigit() or c==' ']).rstrip()

    try:
        file = open(title + '.txt', 'w')    
        file.write('LINK: ' + link + '\n')
        file.write(subtitles)
        file.close()
    except:
        print("Can't create file for: " + title + " : " + link)

def scrape_subtitles(subtitle_link):
    r = urllib.request.urlopen(subtitle_link).read()
    soup = BeautifulSoup(r)

    # Remove tags (<*>), \n, and unecessary whitespace
    s = re.sub(r'<.+?>', '', soup.prettify())   
    s = re.sub(r'\n', '', s)                    
    s = re.sub( '\s+', ' ', s ).strip()         
    return s

def main(argv):
    driver = webdriver.Chrome('C:\Program Files\ChromeDriver\chromedriver.exe')

    # Visit page and load all videos to create a list of
    # tuples(video_name,video_link) of the videos
    driver.get(argv[1])
    display_all_videos(driver)
    videos = video_list(driver)

    # Visit video's page and enable 'CC' to scrape the subtitles and
    # save subtitles to '.txt' file.
    for v in videos:
        driver.get(v[1])
        try:
            enable_subtitles(driver)
            link = subtitles_link(driver)
            subtitles = scrape_subtitles(link)
        except:
            subtitles = "No Closed Caption"
        create_file(v[0],v[1],subtitles)

if __name__ == "__main__":
    main(sys.argv)
```

hihi
