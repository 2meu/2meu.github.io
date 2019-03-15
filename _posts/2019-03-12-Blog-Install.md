---
title: "[블로그] window에서 github 블로그 jekylle 테마 연동"
date: 2019-03-12
categories: github blog jekyll minimal_mistakes-theme window
---

## 1. minimal mistakes 테마 적용

#### - minimal-mistakes 테마 zip으로 다운로드 받기

![ruby](/assets/img/minimal-mistakes.PNG)

#### - local에 minimal-mistakes 테마 압축 해제 파일을 이름 변경하여 저장하기

![ruby](/assets/img/changename.PNG)

---

## 2. Ruby 설치
> 최신 버전은 에러가 날 가능성이 있다고 한다.

#### ruby 2.3.3(x64), devkit(x64)(old) version 따로 다운로드

![ruby](/assets/img/ruby2.3.3.PNG)

![ruby](/assets/img/ruby.PNG)

---

#### Devkit 설정, gem 설정
```
C:\cd devkit
ruby dk.rb init
ruby dk.rb install
```
따로 설치한 devkit과 ruby를 연동시켜준다.

```
C:\cd 2meu.github.io
gem install jekyll bundler
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data

```

#### Serve

![ruby](/assets/img/serve.PNG)

윈도우에서는 인코딩 문제로 `chcp 65001` 타이핑 하여
activate 해준다. 그후 `bundle exec jekyll serve`를 실행하여 local background에서도
바로 웹페이지를 확인할 수 있도록 해준다. 그렇지 않으면 git으로 add-commit-push도 하고
시간이 어느정도 흐른 후에 웹페이지에서 확인할 수 있기에 블로그를 만드는 상황에서 어려움이
있다.

---

## Git

```
git init
posts.md 작성
git add *
git commit -m "message"
git push origin master
```


---
## Gemfile 수정

Gemfile을 아래 내용으로 수정한다.
```
source "https://rubygems.org"
gem "jekyll", "~> 3.5"
gem "minimal-mistakes-jekyll"
```
아래 명령어 수행
```
$ bundle
```

위 과정을 왜 거치는지 알아보자.

---

## 깃 블로그에 이미지 쉽게 추가하기.
기본 이미지 추가 마크다운 언어는
`![random_name](/assets/img/image_name.png | URL)`
이다. URL 경우 추가해 주기만 하면 되지만 스크린샷 찍어서 로컬에 저장해서 이름 정해주고
이렇게 작성하기 귀찮은 경우가 많다. 그래서 쉬운 방법으로 github issues의 new를 선택하여
스크린샷한 이미지를 복사하여 붙여넣기 하면 아래 그림과 같이 마크다운 언어로 이미지를
추가할 수 있도록 친절하게 문장을 준다. 이를 복사 붙여넣기만 하면 블로그에 이미지가
생성된다.
![image](https://user-images.githubusercontent.com/48308562/54335854-982d5c80-466d-11e9-80b3-05fef6f4a4db.png)




### Ref
- [ruby 설치](http://error404.co.kr/dev/2018/04/14/jekyll-error/)
- [chcp65001 encoding error](http://error404.co.kr/dev/2018/04/14/jekyll-error/)
- [3](https://simhyejin.github.io/2016/06/30/Markdown-syntax/)