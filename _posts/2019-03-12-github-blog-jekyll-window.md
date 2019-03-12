---
title: "window에서 github 블로그 jekylle 테마 연동"
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
activate 해준다. 그후 `bundle exec jekyll serve`를 실행하여 local background에서도 바로 웹페이지를 확인할 수 있도록 해준다. 그렇지 않으면 git으로 add-commit-push도 하고 시간이 어느정도 흐른 후에 웹페이지에서 확인할 수 있기에 블로그를 만드는 상황에서 어려움이 있다.

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

---
### Ref
- [ruby 설치](http://error404.co.kr/dev/2018/04/14/jekyll-error/)
- [chcp65001 encoding error](http://error404.co.kr/dev/2018/04/14/jekyll-error/)
- [3](https://simhyejin.github.io/2016/06/30/Markdown-syntax/)
