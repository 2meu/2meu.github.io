---
title: "[Crawling] How to crawling youtube video and mp3 file together"
date: 2019-03-13
categories: [Crawling]
---

## ffmpeg path 지정

동영상을 mp3로 변환하기위한 코덱이다.pyhton에서 subprocess를 통해 수행된다.

![ruby](https://trello-attachments.s3.amazonaws.com/5c7cc2d91aa0cf20079e9df2/905x209/54a7c3e3d41fab78c5226a741638adf6/image.png)

## code

`pip install pytube`

```python
import os
import subprocess
import pytube

url = input("input url address: ")
# https://www.youtube.com/watch?v=L3Ox-Chhjpg
# https://www.youtube.com/watch?v=ApXoWvfEYVU
yt = pytube.YouTube(url)

vids = yt.streams.all()

for i in range(len(vids)):
    print(i, '. ',vids[i])

vnum = int(input("다운 받을 화질은? "))

parent_dir = 'C:/Users/2Dub/tensorflow/crawling/youtube_video_mp3'
vids[vnum].download(parent_dir)

new_filename = input("변환 할 mp3 파일명은?")

default_filename = vids[vnum].default_filename
subprocess.call(['ffmpeg', '-i',
    os.path.join(parent_dir, default_filename),
    os.path.join(parent_dir, new_filename)
])

print('Complete')
```
