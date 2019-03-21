---
title: [크롤링 - 유튜브] 객체지향 + sqlite에 저장하기
date: 2019-03-21
---

동영상과 mp3 파일을 제외한 제목, 설명, 언어, 자막의 정보만 sqlite에 저장하는 코드
main.py와 database_handler.py를 만들어 객체지향적으로 만들고 .sh 파일과 함께 합쳐 git에 배포형식으로 올려보자.

* 현재 문제점: excutemany에서 파일 형식이 맞지 않는거 같다.

```
(crawling) C:\Users\2Dub\sean\Youtube-Crawler\my_code>python get_information.py
Traceback (most recent call last):
  File "get_information.py", line 85, in <module>
    insert_videos_info(video_info)
  File "get_information.py", line 73, in insert_videos_info
    VALUES (?, ?, ?, ?)''', video_info)
sqlite3.ProgrammingError: Incorrect number of bindings supplied. The current statement uses 4, and there are 5 supplied.
```

```python
import pytube
import re
import requests
import json
import sqlite3
from youtube_transcript_api import YouTubeTranscriptApi

#url = input("URL address: ")
#apiKey = input("API key: ")
url = 'https://www.youtube.com/watch?v=TMrIOUQKXCM'
apiKey = 'AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0'
target_url = 'https://www.googleapis.com/youtube/v3/videos?part=snippet&id=JyGGLB542ks&' + '&key=' + apiKey

video_info = {
    'title': '',
    'description': '',
    'defaultLanguage': '',
    'caption':''
}

yt = pytube.YouTube(url)
stream = yt.streams.first()
default_filename = stream.default_filename
# 유튜브 title을 출력 = Captive State Teaser Trailer 1 (2019)  Movieclips Trailers.mp4

try:
    def get_information(target_url, video_info):

        response = requests.get(target_url).text
        items = json.loads(response)
        title = items['items'][0]['snippet']['title']
        description = items['items'][0]['snippet']['description']
        defaultLanguage = items['items'][0]['snippet']['defaultLanguage']

        video_info['title'] = title
        video_info['description'] = description
        video_info['defaultLanguage'] = defaultLanguage

        return video_info

except Exception as e1:
    print("Error: ",e1)
    pass

# 자막 다운로드
try:
    def caption_download(target_url, video_info):
        pat = re.compile("(v=)([a-zA-Z0-9-_]{11})")
        video_id = pat.search(url).group(2)
        caption = YouTubeTranscriptApi.get_transcript(video_id, languages = {'en'})
        video_info['caption'] = str(caption)
        # caption is list format
        return video_info

except Exception as e1:
    print("Error:",e1)
    pass


# DB 저장
connection = sqlite3.connect('youtube_crawling.db')

cursor = connection.cursor()
cursor.execute('''CREATE TABLE IF NOT EXISTS youtube_videos (
                  title text NOT NULL,
                  description text,
                  defaultLanguage text,
                  caption text
                  )''')

def insert_videos_info(video_info):
    cursor.executemany('''INSERT INTO youtube_videos (title, description, defaultLanguage, caption)
                          VALUES (?, ?, ?, ?)''', video_info)
    connection.commit()


def close_connection():
    if connection is not None:
        connection.close()


video_info = get_information(target_url, video_info)
video_info = caption_download(target_url, video_info)

insert_videos_info(video_info)
close_connection()

```
