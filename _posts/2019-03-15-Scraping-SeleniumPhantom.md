---
title: "[스크레이핑] Selenium, PhantomJS로 스크레이핑"
date: 2019-03-15
---

REF: [위키북스]

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
