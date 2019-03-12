---
title: "window에서 github 블로그 jekylle 테마 연동"
date: 2019-03-12
categories: github blog jekyll minimal_mistakes-theme window
---

---
## ruby devkit 2.3.3 version 따로 다운로드

![ruby](https://2meu.github.io/assets/img/ruby2.3.3.PNG)

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
