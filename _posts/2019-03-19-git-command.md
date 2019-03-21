---
title: "[git] 주요 명령어"
date: 2019-03-19
---

## 파일 삭제하기

Git에서 파일을 제거하려면 `git rm`명령으로 tracked 상태의 파일을 삭제한 후 커밋해야한다. 이 명령어는 워킹 디렉토리에 있는 파일도 삭제한다.

git 없이 파일을 옮기거나 삭제했다면 git status 명령어로 확인하면 Changed not staged for commit로 unstaged에 속한다.

이 unstaged에 속한 파일들을 `git rm` 명령어로 다시 삭제 해주어야 staged 상태가 되고 커밋하면 파일을 삭제해준다.

## 파일 이름 변경

`$ git mv file_from file_to`

이는 다음 명령어와 동일하다.

``` bash
$ mv README.txt README
$ git rm README.txt
$ git add README
```

## 히스토리 확인

두개의(-2) 변경된 내용(-p)을 확인
`git log -p -2`

## 커밋 수정하기

커밋을 했는데 Stage하는 것을 깜빡하고 빠트린 파일이 있으면 아래와 같이 고칠 수 있다.

```bash
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```

## staging area를 unstaged로 변경

`$ git reset HEAD <file>`

## 수정되고 add되지 않은 파일 전으로 되돌리기

`$ git checkout -- test2`

## remote 저장소

### 로컬로 가져오기

리모트 저장소에서 데이터를 가져오기

 `$ git fetch [remote-name=origin]`

이 명령어는 로컬에는 없지만 리모트 저장소에 있는 데이터를 모두 가져온다.
아직 로컬 파일에 merge는 되어있지 않은 상태.
그래서 그냥

 `$ git pull <url>`

 해주면 local로 가져온 상태에서 merge까지 해준다.

### 저장소로 보내기

`$ git push [origin=리모트 저장소 이름] [master=브랜치 이름]`

### 저장소 삭제하기

`$ git remote rm origin`

## 태그 사용 - 릴리즈 할 때 사용
