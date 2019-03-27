---
title: '[크롤링 - 유튜브] pymysql'
date: 2019-03-21
---

# youtube crawling

git
https://bitbucket.org/donghyunPark/youtube_crawling/src/master/


main.py
```python
from url_handler import get_api_information
from get_information import download_info, download_caption
from database_handler_mysql import insert_info, insert_caption, close_connection

#from database_handler import close_connection

#url = input("URL address: ")
#apiKey = input("API key: ")
url= 'https://www.youtube.com/watch?v=TcMBFSGVi1c'
apiKey= ''

try:
    video_id, items = get_api_information(url, apiKey)
except:
    print('Error at get_api_information')

try:
    videos_info = download_info(items)
    videos_caption = download_caption(video_id)
except:
    print('Error at download from json')

# videos_caption db에 title, defaultLanguage 추가
for x in videos_caption:
    x.insert(0, videos_info[0])
    x.insert(1, videos_info[2])

# db 저장
try:
    insert_info(videos_info)
    insert_caption(videos_caption)
except:
    print('Error at storing db')
finally:
    close_connection()
```

url_handler.py
```python
import re
import requests
import json

def get_api_information(url, apiKey):
    pat = re.compile("(v=)([a-zA-Z0-9-_]{11})")
    video_id = pat.search(url).group(2)
    part = "snippet"
    target_url = 'https://www.googleapis.com/youtube/v3/videos?part=' + part + '&id=' + video_id + '&key=' + apiKey
    response = requests.get(target_url).text
    items = json.loads(response)

    return video_id, items

```

get_information.py
```python
import pytube
from youtube_transcript_api import YouTubeTranscriptApi

# 정보 다운로드
def download_info(items):
    title = items['items'][0]['snippet']['title']
    description = items['items'][0]['snippet']['description']
    publishedAt = items['items'][0]['snippet']['publishedAt']
    if items['items'][0]['snippet'].get('defaultLanguage') is None:
        defaultLanguage = "None"
    else:
        defaultLanguage = items['items'][0]['snippet']['defaultLanguage']
    return [title, description, defaultLanguage, publishedAt]

# 자막 다운로드
def download_caption(video_id):
    caption = YouTubeTranscriptApi.get_transcript(video_id, languages = {'en'})
    videos_caption = [list(x.values()) for x in caption]
    return videos_caption
```

database_handler_mysql.py
```python
import pymysql.cursors

conn = pymysql.connect(host='localhost',
                         user='root',
                         password='111111',
                         db = 'youtube',
                         charset='utf8mb4')

cursor = conn.cursor()

try:
    sql = '''
    CREATE TABLE IF NOT EXISTS information (
        id int(11) NOT NULL PRIMARY KEY AUTO_INCREMENT,
        title VARCHAR(255) NOT NULL,
        description TEXT,
        defaultLanguage VARCHAR(255),
        publishedAt VARCHAR(255)
        )
        '''
    cursor.execute(sql)
except:
    print("information table already exists")

try:
    sql = '''
    CREATE TABLE IF NOT EXISTS caption (
        id int(11) NOT NULL PRIMARY KEY AUTO_INCREMENT,
        title VARCHAR(255) NOT NULL,
        defaultLanguage VARCHAR(255),
        caption TEXT,
        start VARCHAR(255),
        duration VARCHAR(255)
        )
        '''
    cursor.execute(sql)
    conn.commit()
except:
    print('caption table already exists')

def insert_info(videos_info):
    with conn.cursor() as cursor:
        sql = '''
        INSERT INTO information (title, description, defaultLanguage, publishedAt)
        VALUES (%s, %s, %s, %s)
        '''
        cursor.execute(sql, videos_info)
    conn.commit()

def insert_caption(videos_caption):
    with conn.cursor() as cursor:
        sql = '''
        INSERT INTO caption (title, defaultLanguage, caption, start, duration)
        VALUES (%s, %s, %s, %s, %s)
        '''
        cursor.executemany(sql, videos_caption)
    conn.commit()

def close_connection():
    if conn is not None:
        conn.close()
```
