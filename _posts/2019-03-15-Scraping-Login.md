---
title: "[스크레이핑 - 기초] 로그인이 필요한 사이트 스크레이핑"
date: 2019-03-15
categories : login scraping
---

## GET은 가져오는 것이고 POST는 수행하는 것입니다.

이 개념만 잘 생각하고 있으면 상황에 따라서 어느정도 선택을 할 수 있습니다.(물론 그래도 좀 고민되는 예외상황들은 있게 마련이죠.) 좀 자세히 설명하면 GET은 Select적인 성향을 가지고 있습니다. GET은 서버에서 어떤 데이터를 가져와서 보여준다거나 하는 용도이지 서버의 값이나 상태등을 바꾸지 않습니다. 게시판의 리스트라던지 글보기 기능 같은 것이 이에 해당하죠.(방문자의 로그를 남긴다거나 글읽은 횟수를 올려준다거나 하는건 예외입니다.) 반면에 POST는 서버의 값이나 상태를 바꾸기 위해서 사용합니다. 글쓰기를 하면 글의 내용이 디비에 저장이 되고 수정을 하면 디비값이 수정이 되죠. 이럴 경우에 POST를 사용합니다.

### <span style="color:purple"> request module </span>

```python
import requests

# GET
r = requests.get("URL")
#POST
formdata = {"key1":"value1","key2":"value"}
r = requests.post("URL", data=formdata)
#etc
r = requests.put("URL")
r = requests.delete("URL")
r = reuqests.head("URL")
```

### <span style="color:purple"> 이미지 출력 예제 </span>

GET, POST 등의 리턴값에 있는
- text(문자)
- content(바이너리문자)

바이너리 데이터인 이미지를 받아 저장하는 예제

```python
import requests
r = requests.get("http://wikibook.co.kr/wikibook.png")

# 바이너리 형식으로 데이터 저장하기
with open("test.png", "wb") as f:
  f.write(r.content)

print("saved")
```


## 한빛출판 사이트에서 로그인 후 마일리지, 코인 정보 가져오기

```python
import requests
from urllib.parse import urljoin
from bs4 import BeautifulSoup

## 아톰에서 파이썬 실행 시 스크립트 한글 깨짐 해결 코드
import sys
import io
sys.stdout = io.TextIOWrapper(sys.stdout.detach(), encoding = 'utf-8')
sys.stderr = io.TextIOWrapper(sys.stderr.detach(), encoding = 'utf-8')


USER = 'zlslsp54'
PASS = 'eodgus54'

# 세션 시작하기
session = requests.session()
#로그인하기
login_info = {
    "m_id": USER,
    "m_passwd": PASS
}
url_login = "http://www.hanbit.co.kr/member/login_proc.php"
res = session.post(url_login, data=login_info)
res.raise_for_status()

# 마이페이지에 접근하기
url_mypage = "http://www.hanbit.co.kr/myhanbit/myhanbit.html"
res = session.get(url_mypage)
res.raise_for_status()

# 마일리지와 이코인 가져오기

soup = BeautifulSoup(res.text, "html.parser")
mileage = soup.select_one(".mileage_section1 span").string
ecoin = soup.select_one(".mileage_section2 span").get_text()
print("마일리지 = ", mileage)
print("이코인 = ", ecoin)
```

## 결론

파이썬 라이브러리인 requests를 이용해 로그인하고 필요한 데이터를 스크레이핑하는 방법.
단순 쿠키를 사용해 로그인하기 때문에 대부분 유명한 웹페이지에서는 동작하지 않는 경우가 많다.

따라서 Selenium과 다른 웹 페이지(PhantomJS)를 사용해 동적으로 웹 스크레이핑을 할 수 있는 방법을 공부해보자.

[다음절](https://2meu.github.io/Scraping-SeleniumPhantom/)

## Reference
[https://blog.outsider.ne.kr/312](https://blog.outsider.ne.kr/312)
