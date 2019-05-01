---
title: '[ELASTIC] Logstash basic'
date: 2019-04-26
categories : [Elastic]
---

## Download on Window

[Elastic logstash dowload](https://www.elastic.co/kr/downloads/logstash)

![image](https://user-images.githubusercontent.com/48308562/57002229-e1734480-6bf8-11e9-8086-ed7068e494be.png)

Elasticsearch와 Kibana를 설치했다면 위 사이트에 접속하여 logstash ZIP파일을 다운로드 받아줍니다.

_압축을 해제할때 에러가 난다면 C:\ 바로밑에서 풀어보세요 이름이 길기 때문에 에러가 날 수 있습니다._

![image](https://user-images.githubusercontent.com/48308562/57002291-47f86280-6bf9-11e9-8b2e-4223e5bd7b85.png)

압축을 푼 후에 저같은 경우엔 위 그림과 같이 `C:\elastic\` 하위에 elasticsearch(node1~3), kibana, logstash를 함께 위치시켰습니다.

Logstash의 주 작동 방법은 Logstash에 원하는 명령어를 conf파일로 미리 만들어 놓은 상태에서(simple.conf) CMD 창을 통해 명령어 `C:\elastic\logstash\bin\logstash -f simple.conf` 를 실행해 주면 simple.conf가 수행되는 구조입니다.

## Configuring Logstash - Just do it

 logstash.conf 파일은 **input**, **filter**, **output** 의 구조를 가지고 있습니다. input으로 들어간 파일이 filter를 통해 전처리 과정을 거쳐 output 저장소로 이동하게 되는 구조입니다. 3개의 명령어에 각각 plugin을 지정할 수 있습니다.

conf 파일을 어떻게 구성해야 하는지 간단한 예제로 살펴 봅시다.

![image](https://user-images.githubusercontent.com/48308562/57002665-f7cecf80-6bfb-11e9-96bb-3508926cab4d.png)

`bin\logstash -f config\simple.conf`

저는 config folder밑에 simple.conf 파일을 `notepad/config/simple.conf` 명령어를 통해 만들어줍니다. 그 다음 CMD 창에서 위 명령어를 실행해줍니다.

![image](https://user-images.githubusercontent.com/48308562/57002910-c8b95d80-6bfd-11e9-81ca-823f25f8413d.png)
