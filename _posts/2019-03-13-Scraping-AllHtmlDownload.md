---
title: "[스크레이핑 - 기초]모든 페이지를 재귀적으로 한꺼번에 다운받는 프로그램"
date: 2019-03-13
---

```python
# 파이썬 메뉴얼을 재귀적으로 다운받는 프로그램
# 모듈 읽어 들이기
from bs4 import BeautifulSoup
from urllib.request import *
from urllib.parse import *

from os import makedirs
import os.path, time, re

# 이미 처리한 파일인지 확인하기 위한 변수
proc_files = {}

#HTML 내부에 있는 링크를 추출하는 함수
def enum_links(html, base):
    soup = BeautifulSoup(html, "html.parser") # class
    links = soup.select("link[rel='stylesheet']")
    linsk += soup.select("a[href]") # 링크
    result = []
    # href 속성을 추출하고, 링크를 절대 경로로 변환
    for a in links:
        href = a.attrs['href']
        url = urljoin(base, href)
        # href가 여러 형태일 수 있는데 url join을 사용하면 완전한
        # url 주소로 만들어준다.
        result.append(url)
    return result

# 파일을 다운받고 저장하는 함수
def download_file(url):
    o = urlopen(url)
    savepath = "./" + o.netloc + o.path #??
    if re.search(r"/$", savepath):
        savepath += "index.html" #폴더라면 index.HTML 끝에 추가
    savedir = os.path.dirname(savepath)
    #모두 다운됐는지 확인
    if os.path.exists(savepath): return savepath
    # 다운 받을 폴더 생성
    if not os.path.exists(savedir):
        print("mkdir=", savedir)
        makedirs(savedir)
    # 파일 다운받기
    try:
        print("download=", url)
        urlretrieve(url, savepath)
        time.sleep(1) # 1 second break
        return savepath
    except:
      print("Failed downloading")
      return None

# HTML을 분석하고 다운받는 함수
def analyze_html(url, root_url):
  savepath = download_file(url)
  if savepath is None: return
  if savepath in proc_files: return # 이미 처리되었다면 실행하지 않음
  proc_files[savepath] = True
  print("analyze_html=", url)
  # 링크 추출
  html = open(savepath, "r", encoding="utf-8").read()
  links = enum_links(html, url)
  for link_url in links:
    # 링크가 루트 이외의 경로를 나타낸다면 무시
    if link_url.find(root_url)!=0:
      if not re.search(r".css$", link_url): continue
    if re.search(r".(html|htm)$", link_url):
      # 재귀적으로 HTML 파일 분석하기
      analyze_html(link_url, root_url)
      continue
  # 기타 파일
  download_file(link_url

if __name__ == "__main__":
  #URL에 있는 모든 것 다운받기
  url = "https://docs.python.org/3.5/library/"
  analyze_html(url, url)
```

## urlopen

*urlopen() 에서는 몇가지 메서드를 지원한다. 자주 사용하는 몇가지 정리하면*

- urlopen().read([nbytes]) : nbyte의 데이터를 바이트 문자열로 읽음 ​
- urlopen().readline() : 한 줄의 텍스트를 바이트 문자열로 읽음
- urlopen().​info() : URL에 연관된 메타 정보를 담은 매핑 객체를 반환​
- urlopen().getcode() : HTTP 응답 코드를 정수로 반환( 200, 404 )
- urlopen().close() : 연결을 닫는다


## os.path.dirname(path)

입력받은 파일/디렉토리의 경로를 반환합니다.

```bash
>>> dirname('C:\\Python30\\tmp\\test.txt')
'C:\\Python30\\tmp'
>>> dirname('C:\\Python30\\tmp')
'C:\\Python30'
```

## urllib.parse

urllib.parse모듈은 url에 한글 검색어를 입력할 수 있도록 도와주는 모듈입니다.
한글로 검색을 하기 위해서는 한글을 인코딩이란 과정을 거쳐서 데이터를 요청해야 하는데 이때 인코딩을 해주는 모듈이 urllib.parse 모듈입니다.

#### netloc? path?
```python
from urllib.parse import *

result = urlparse("http://www.python.org:80:80/guido/python.html")
result

>> ParseResult(scheme='http',netloc='www.python.org:80:80',path='/guido/python.html')
```

#### python 실행하면

```python
if __name__ == "__main__":
  #URL에 있는 모든 것 다운받기
  url = "https://docs.python.org/3.5/library/"
  analyze_html(url, url)
```

쪽에서 에러가 나는데 이유를 찾아보자.


##### Ref
- https://kimdoky.github.io/python/2017/06/12/python-urllib.html
