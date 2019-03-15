---
title: "[블로그] 다른 컴퓨터에서 깃허브 블로그 수정"
date: 2019-03-13
---

## Git bash 설정


- git clone https://github.com/2meu/2meu.github.io

- `cd 2meu.github.io` 하면 자동으로 remote 되어있는 2meu.github.io file이 생성되어 있다.

![ruby](/assets/img/other-computer1.PNG)

---

## Ruby prompt

#### 1. 의존성 갖는 패키지 설치
- `cd 2meu.github.io`
- `gem install jekyll`
- `gem install minima`
- `gem install jekyll-feed`
- `gem install tzinfo-data`

#### 2. 인코딩 문제
- `chcp 65001`
- `bundle exec jekyll serve` (bundle exec 안하면 에러 발생)
- 이제 local로(localhost:4000) 바로 확인하면서 블로그를 편집하고 git add - commit - push로 관리 해준다.
