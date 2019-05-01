---
title: '[ELASTIC] Get DB data from mysql using logstash'
date: 2019-04-26
categories : [Elastic]
---

## Window CMD에서 기본 명령어

C:/elastic/logstash-7.0.0/bin/logstash -f simple.config

## config file 형식

input {
  stdin {}
}
output {
  stdout {}
}

## config/logstash.yml 설정
![image](https://user-images.githubusercontent.com/48308562/56950184-d58f7000-6b6f-11e9-9718-0f53dc7237cc.png)

## config/pipelines.yml 설정
