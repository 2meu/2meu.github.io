---
title: '[Crawling] pymysql에 유튜브 크롤링 데이터 저장하기'
date: 2019-03-21
categories : [Crawling]
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

## 수정 - 외래키 지정

![image](https://user-images.githubusercontent.com/48308562/55455865-5a927280-5620-11e9-91d7-a21b0ddbce1f.png)

위 사진처럼 Heid에서 데이터를 확인 결과 defaultLanguage와는 상관없이 caption이 스페인어로 붙어온 경우가 생겼다. 스페인어로 되어있는 caption들의 영화를 한번에 삭제 해주기 위해서 caption 테이블의 title을 information 테이블의 title에 외래키를 주는 것이 좋을 것 같다. 우선 `drop table information, caption`을 통해 테이블을 모두 삭제 해준다. 기존 information에서 id가 AUTO_INCREMENT 설정을 가진 PRIMARY KEY였기에 id 부분을 제거하고 title에 PRIMARY KEY를 설정 해줍니다.

```python
sql = '''
CREATE TABLE IF NOT EXISTS information (
    title VARCHAR(255) NOT NULL PRIMARY KEY,
    description TEXT,
    defaultLanguage VARCHAR(255),
    publishedAt VARCHAR(255)
    ) ENGINE = InnoDB
'''
```

그 후에 caption쪽에서 FOREIGN KEY 설정을해 주도록 합니다.

```python
sql = '''
CREATE TABLE IF NOT EXISTS caption (
    id int(11) NOT NULL PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    defaultLanguage VARCHAR(255),
    caption TEXT,
    start VARCHAR(255),
    duration VARCHAR(255),
    FOREIGN KEY(title)
    REFERENCES information(title)
    ON DELETE CASCADE
    ON UPDATE CASCADE
    ) ENGINE = InnoDB
    '''
```

설정을 완료한 후에 다시 trailer라는 채널의 유튜브에서 여러 영상을 다운로드 받은 후

![image](https://user-images.githubusercontent.com/48308562/55455865-5a927280-5620-11e9-91d7-a21b0ddbce1f.png)

아까 확인했던 Heid에서 다시 스페인어가 존재하는 영화의 제목을 확인 후에

![image](https://user-images.githubusercontent.com/48308562/55456312-b3aed600-5621-11e9-9c2b-6eaea9501fa2.png)

간단히 Heid에서 행 삭제를 해주면

![image](https://user-images.githubusercontent.com/48308562/55456374-d5a85880-5621-11e9-9c10-6d29df20e443.png)

caption 테이블에 존재하던 스페인어 자막의 영화 트레일러가 사라진 모습을 볼 수 있습니다.

추가적으로 텍스트를 분석할 것이기 때문에 자막에 존재하는 [Music] 행을 삭제 해준다.

`delete from caption where caption = '[Music]';`

+ [Music] 말고도 [Applause]등과 같은 표현들이 더 있으므로 정규표현식을 통해 없애준다.
`delete from caption where caption regexp "^\\[";`
#### 대괄호로 시작하는 문장 추출해서 없애기
[] 대괄호를 표현하기 위해선 \\[ 이런식으로 대쉬를 앞에 두번 써준다.


추후에 파이썬에서 영상정보를 받아올 때 영어 자막만 받아오도록 수정해야겠다.
