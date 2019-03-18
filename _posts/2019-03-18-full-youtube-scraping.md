---
title: "[스크레이핑 - 유튜브] 유튜브에서 비디오, mp3, 정보, 자막 한번에 스크레이핑"
date: 2019-03-18
categories: youtube scraping
---

![image](https://user-images.githubusercontent.com/48308562/54528214-a35ef000-49bf-11e9-9a4c-080f56de7eb6.png)


```python
# pip install pytube
# pip install youtube_transcript_api
# mp3 변환을 위한 ffmpeg codec을 운영체제에 맞게 다운로드
# Window  https://www.filehorse.com/download-ffmpeg-64/
# mac(linux)  https://www.ffmpeg.org/download.html
# ffmpeg 파일을 환경변수에 추가하거나 파이썬 실행파일 폴더로 이동 후 실행

import os
import subprocess
import pytube
import re
import requests
import json
from youtube_transcript_api import YouTubeTranscriptApi

url = input("input url address: ")
# https://www.youtube.com/watch?v=WZ-5-oTHnnY
yt = pytube.YouTube(url)
stream = yt.streams.first()
# second하면 두번째로 좋은 화질이 얻어지나? 확인해보기
# 최고화질 동영상 선택
# stream = <Stream: itag="22" mime_type="video/mp4" res="720p" fps="30fps" vcodec="avc1.64001F" acodec="mp4a.40.2">

default_filename = stream.default_filename
# default_filename = pytube module의 youtube함수로 만들어진 stream이 가지고 있는 함수인 것 같다.
# 유튜브 title을 출력 = Captive State Teaser Trailer 1 (2019)  Movieclips Trailers.mp4
mp3_filename = default_filename[0:-3] + 'mp3'
# Captive State Teaser Trailer 1 (2019)  Movieclips Trailers.mp3
caption_filename = default_filename[0:-3] + '_caption_txt'
# Captive State Teaser Trailer 1 (2019)  Movieclips Trailers.txt
info_filename = default_filename[0:-3] + '_info_txt'

# 1) 최고화질 동영상 다운로드
dir_path = 'C:/Users/2Dub/youtube_scraping/'
dir_name = default_filename[0:-4].replace(' ', '_')
# 파일 이름에 공백 없애주자
parent_dir = dir_path + dir_name + '/'
os.mkdir(parent_dir)
stream.download(parent_dir)

# 2) 자막 다운로드
# videoId = url의 (v=) 다음에 오는 11개 문자열 compile
pat = re.compile("(v=)([a-zA-Z0-9-_]{11})")
# v= 문자열을 검색할땐 포함하지만 search할 땐 그룹으로 제외해준다.
video_id = pat.search(url).group(2)

data = YouTubeTranscriptApi.get_transcript(video_id, languages = {'en'})
# language 지정 가능, auto로 번역된것도 자동으로 가져와 준다.
with open(parent_dir+caption_filename, 'w') as tf:
    tf.write(str(data))

# 3) mp3 변환 후 다운로드
# ffmpeg(Codec) 실행하면 mp3로 변환
# subprocess module = 쉘에서 실행된 결과물을 스크립트로 가져올 수 있디.
# http://blog.naver.com/PostView.nhn?blogId=sagala_soske&logNo=221280201722&redirect=Dlog&widgetTypeCall=true&directAccess=false
# 여기선 쉘 결과를 파일로 저장하는 스크립트 코드.
# 쉘에 출력된 결과물을 저장 하지 않으면 초기화 되어버려 subprocess로 저장해준다.
# cmd 명령어 수행
subprocess.call(['ffmpeg', '-i',
    os.path.join(parent_dir, default_filename),
    # 위에 path 지정하지 않으면 밑에도 수행되지 않는다.
    os.path.join(parent_dir, mp3_filename)
])

# 4) 유튜브 정보

movie_trailer_info = {
    'title': '',
    'publishedAt': '',
    'description': '',
    'defaultLanguage': ''
}

def get_trailer_video_link(target_url):
    response = requests.get(target_url).text
    # json should be str
    items = json.loads(response)
    title = items['items'][0]['snippet']['title']
    publishedAt = items['items'][0]['snippet']['publishedAt']
    description = items['items'][0]['snippet']['description']
    defaultLanguage = items['items'][0]['snippet']['defaultLanguage']
    movie_trailer_info = {
    'title': title,
    'publishedAt': publishedAt,
    'description': description,
    'defaultLanguage': defaultLanguage
    }
    return movie_trailer_info

apiKey ='AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0'
target_url = 'https://www.googleapis.com/youtube/v3/videos?part=snippet&id=JyGGLB542ks&' + '&key=' + apiKey

data = str(get_trailer_video_link(target_url))
with open(parent_dir+info_filename, 'w', encoding="utf8") as tf:
    # jason need utf-8 encoding
    tf.write(data)

# print(items['items'][0]['snippet']['publishedAt'])
# print(items['items'][0]['snippet']['title'])
# print(items['items'][0]['snippet']['description'])

print('Complete')
```
