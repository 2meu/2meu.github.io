---
title: "[Crawling] Selenium, PhantomJS로 스크레이핑"
date: 2019-03-15
categories : [Crawling]
---

자바스크립트를 많이 사용하는 웹 사이트는 웹 브라우저를 사용하지 않을 경우 제대로 동작을 확인할 수 없습니다. 그래서 이러한 사이트는 이전에 소개한 requests 모듈로 대처할 수 없습니다.

Selenium을 이용하면 자동으로 URL을 열고 클릭할 수 있으며, 스크롤하거나, 문자를 입력하는 등의 다양한 조작을 자동화할 수 있습니다. 또한 화면을 캡처해서 이미지로 저장하거나 HTML의 특정 부분을 꺼내는 것도 가능합니다.

PhantomJS는 명령줄에서 사용할 수있는 headless 웹 브라우저입니다.
(따로 시각적인 웹페이지를 제공하지 않는다.)

## window에서 설정

![image](https://user-images.githubusercontent.com/48308562/54410657-bbb8db80-472f-11e9-8eb5-9220b2a3db20.png)

- http://phantomjs.org/download.html 에서 윈도우 버전 다운로드
- 압축파일에 있는 phantomjs.exe파일만 crawling 가상환경 밑에 존재하는 `selenium/webdrivce/phantomjs` 경로에 넣어준다.
- PATH 지정
`C:\Users\2Dub\Anaconda3\envs\crawling\Lib\site-packages\selenium\webdriver\phantomjs`

## 네이버에 로그인해서 구매한 물건 목록 가져오기

```python
from selenium import webdriver
USER = "<아이디>"
PASS = "<비밀번호>"

# PhantomJS 드라이버 추출하기
browser = webdriver.PhantomJS()
browser.implicitly_wait(3)

# 로그인 페이지에 접속하기
url_login = "https://nid.naver.com/nidlogin.login"
browser.get(url_login)
print("로그인 페이지에 접근합니다.")

# 텍스트 박스에 아이디와 비밀번호 입력하기
e = browser.find_element_by_id("id")
e.clear()
# 무언가 적혀있다면 지워주라는 말인가?
e.send_keys(USER)
e = browser.find_element_by_id("pw")
e.clear()
e.send_keys(PASS)

# 입력 양식 전송해서 로그인하기
form= browser.find_element_by_css_selector("input.btn_global[type=submit]")
form.submit()
print("로그인 버튼을 클릭합니다.")

# 쇼핑 페이지의 데이터 가져오기
browser.get("https://order.pay.naver.com/home?tabMenu=SHOPPING")

# 쇼핑 목록 출력하기
product = browser.find_elements_by_css_selector(".p_info span")
print(products)
for product in products:
  print("-", product.text)
```

아무런 정보가 뜨지 않는다.

```python
browser.get("https://order.pay.naver.com/home?tabMenu=SHOPPING")
html = browser.page_source
print(html)
```

을 해보았더니 여전히 로그인 창이다. 로그인이 되지 않은 것이다.
실제로 로그인 페이지에 들어가 로그인을 해보려고 하니 자동 로그인 방지 매크로가 설정되어 있었다.

댓글 조작에 민감한 네이버가 막아놓은것 같다고 한다.

## 네이버 API로 읽어오기

추후 진행


## 결론

네이버로 로그인 하는 selenium은 동작하지 못한다. 네이버, 구글 같은 큰 웹들은 서버 과부화와 보안 문제로 인해 자체 API를 운영중이다. 로그인이 필요한 스크레이핑 같은 경우는 작은 웹사이트에서 사용하고 대형 웹사이트는 자체 API를 이용하자.
