---
title: git 주요 명령어
date: 2019-03-19
---

# 파일 삭제하기

Git에서 파일을 제거하려면 `git rm`명령으로 tracked 상태의 파일을 삭제한 후 커밋해야한다. 이 명령어는 워킹 디렉토리에 있는 파일도 삭제한다.

git 없이 파일을 옮기거나 삭제했다면 git status 명령어로 확인하면 Changed not staged for commit로 unstaged에 속한다.

이 unstaged에 속한 파일들을 `git rm` 명령어로 다시 삭제 해주어야 staged 상태가 되고 커밋하면 파일을 삭제해준다.

# 파일 이름 변경

`$ git mv file_from file_to`

이는 다음 명령어와 동일하다.

` bash
$ mv README.txt README
$ git rm README.txt
$ git add README
`
