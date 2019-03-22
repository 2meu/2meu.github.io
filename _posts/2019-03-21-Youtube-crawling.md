---
title: '[크롤링 - 유튜브] 객체지향 + sqlite에 저장하기'
date: 2019-03-21
---

동영상과 mp3 파일을 제외한 제목, 설명, 언어, 자막의 정보만 sqlite에 저장하는 코드
main.py와 database_handler.py를 만들어 객체지향적으로 만들고 .sh 파일과 함께 합쳐 git에 배포형식으로 올려보자.


```python
import pytube
import re
import requests
import json
from youtube_transcript_api import YouTubeTranscriptApi
import sqlite3

#url = input("URL address: ")
#apiKey = input("API key")
url = "https://www.youtube.com/watch?v=JyGGLB542ks"
apiKey = "AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0"

try:
    pat = re.compile("(v=)([a-zA-Z0-9-_]{11})")
    video_id = pat.search(url).group(2)

except Exception as e1:
    print("Error: ",e1)
    pass

# youtube information
try:
    def get_information(response):
        title = items['items'][0]['snippet']['title']
        description = items['items'][0]['snippet']['description']
        defaultLanguage = items['items'][0]['snippet']['defaultLanguage']
        publishedAt = items['items'][0]['snippet']['publishedAt']

        return title, description, defaultLanguage, publishedAt

except Exception as e1:
    print("Error: ",e1)
    pass

# 자막 다운로드
try:
    def caption_download(video_id):
        caption = YouTubeTranscriptApi.get_transcript(video_id, languages = {'en'})
        videos_caption = [list(x.values()) for x in caption]
        return videos_caption

except Exception as e1:
    print("Error:",e1)
    pass

title, description, defaultLanguage, publishedAt = get_information(items)
videos_info = [title, description, defaultLanguage, publishedAt]
videos_caption = caption_download(video_id)

# videos_caption db에 title, defaultLanguage 추가
for x in videos_caption:
    x.insert(0,title)
    x.insert(1, defaultLanguage)


# store at sqlite3
connection = sqlite3.connect('crawling.db')
cursor = connection.cursor()

cursor.execute('DROP TABLE IF EXISTS youtube_videos_info')
cursor.execute('''CREATE TABLE IF NOT EXISTS youtube_videos_info (
                  title text NOT NULL,
                  description text,
                  defaultLanguage text,
                  publishedAt text
                  )''')

cursor.execute('DROP TABLE IF EXISTS youtube_videos_caption')
cursor.execute('''CREATE TABLE IF NOT EXISTS youtube_videos_caption (
                  title text NOT NULL,
                  defaultLanguage text,
                  caption text,
                  start float,
                  duration float
                  )''')

cursor.execute('''INSERT INTO youtube_videos_info
                    VALUES(?, ?, ?, ?);''', videos_info)

cursor.executemany('''INSERT INTO youtube_videos_caption
                    VALUES (?, ?, ?, ?, ?)''', videos_caption)

connection.commit()
if connection is not None:
    connection.close()

```


check in python

```python
connection = sqlite3.connect('crawling.db')
cursor = connection.cursor()

query = "SELECT * FROM youtube_videos_info;"
cursor.execute(query)
all_rows = cursor.fetchall()
for i in all_rows:
    print(i)
print("\n" * 3)
```

주피터에서 실행 결과

```python
('Avengers: Endgame Trailer #2 (2019) | Movieclips Trailers', "Check out the official Avengers: Endgame Trailer starring Chris Hemsworth! Let us know what you think in the comments below.\n► Buy Tickets to Avengers: Endgame: http://www.fandango.com/avengers4_215871/movieoverview?cmp=MCYT_YouTube_Desc\n\nWant to be notified of all the latest movie trailers? Subscribe to the channel and click the bell icon to stay up to date.\n\nUS Release Date: April 26, 2019\nStarring: Chris Evans, Robert Downey Jr., Scarlett Johansson\nDirected By: Anthony Russo, Joe Russo\nSynopsis: The fourth installment of the 'Avengers' series.\n\nWatch More Trailers: \n► Hot New Trailers: http://bit.ly/2qThrsF\n► Action/Sci-Fi Trailers: http://bit.ly/2Dm6mTB\n► Drama Trailers: http://bit.ly/2ARA8Nk\n\nFuel Your Movie Obsession: \n► Subscribe to MOVIECLIPS TRAILERS: http://bit.ly/2CNniBy\n► Watch Movieclips ORIGINALS: http://bit.ly/2D3sipV\n► Like us on FACEBOOK: http://bit.ly/2DikvkY \n► Follow us on TWITTER: http://bit.ly/2mgkaHb\n► Follow us on INSTAGRAM: http://bit.ly/2mg0VNU\n\nThe Fandango MOVIECLIPS TRAILERS channel delivers hot new trailers, teasers, and sneak peeks for all the best upcoming movies. Subscribe to stay up to date on everything coming to theaters and your favorite streaming platform.\n\n#AvengersEndgame\n#Avengers4Trailer\n#Avengers4", 'en', '2019-03-14T12:32:18.000Z')

```

local에 있던 db파일을 sqlite 파일에 옮긴 모습
![image](https://user-images.githubusercontent.com/48308562/54817574-a36e3100-4cda-11e9-8e1d-7906ca0eca77.png)


sqlite3 명령어로 확인한 결과

- youtube_videos_information table
![image](https://user-images.githubusercontent.com/48308562/54817878-4aeb6380-4cdb-11e9-94e1-32f4901791f4.png)

- youtube_videos_caption table
![image](https://user-images.githubusercontent.com/48308562/54817910-6191ba80-4cdb-11e9-93da-0ecc2fd3ef12.png)
