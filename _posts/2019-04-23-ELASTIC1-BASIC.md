---
title: [ELASTIC] Basic of Elastic
date: 2019-04-23
categories : [Elastic]
---

## 개요

![image](https://user-images.githubusercontent.com/48308562/56714468-de4b0500-676f-11e9-83f0-0cfef71ca1e3.png)

**Logstash : collect data, filter data**

- Collect data: 다양한 [입력지원](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)을 통해 여러가지 공통 소스에서 이벤트를 동시에 가져옵니다.
  - elasticsearch
  - file
  - github
  - http
  - etc...

- Filter data: grok(data parsing - re module과 비슷): 데이터가 소스에서 저장소로 이동하는 과정에서 구문 분석 + 필드 식별을 통해 구조화 해줍니다.
  - aggregate
  - alter
  - csv
  - date
  - grok: parse arbitrary text and structure it.(필드 분석, 정규화 분석)
    ```
    Examples: With that idea of a syntax and semantic, we can pull out useful fields from a sample log like this fictional http request log:

    55.3.244.1 GET /index.html 15824 0.043
    The pattern for this could be:

    %{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration}
    A more realistic example, let’s read these logs from a file:

    input {
      file {
        path => "/var/log/http.log"
      }
    }
    filter {
      grok {
        match => { "message" => "%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration}" }
      }
    }
    After the grok filter, the event will have a few extra fields in it:

    client: 55.3.244.1
    method: GET
    request: /index.html
    bytes: 15824
    duration: 0.043
    ```

- 출력: 원하는 곳으로 데이터를 라우팅할 수 있습니다. 다양한 출력을 지원하기 때문에 여러 저장소로 데이터를 다운스트림할 수 있습니다.


**Elastic search(store data) :**

- 데이터를 쉽게 처리하고 엑세스 할 수 있도록 합니다.
- document 기반 검색 엔진
```
$
{
  "name" : "Craft"
  "geo" : {
    "city" : "Budapest",
    "lat" : 47.49, "lon" : 19.04
  }
}
```

  - CRUD REST API를 통한 데이터 추가, 업데이트, 검색 및 삭제
  - 토크나이징, 토큰 필터링을 통한 텍스트 분석(Analysis)
  - 기본적인 검색 쿼리
  - 애그리게이션(집계) - Elasticsearch 의 데이터 집계와 분석을 위한 기능

**Kibana : data visualizations**


## Download

[참고 페이지](https://m.blog.naver.com/PostView.nhn?blogId=ambition0917&logNo=221120007065&proxyReferer=https%3A%2F%2Fwww.google.com%2F)

Elasticsearch [다운드 페이지](https://www.elastic.co/downloads/elasticsearch)
  - 실행: bin/elasticsearch.bat
  - 접속확인 url: localhost:9200

Kibana [다운로드 페이지](https://www.elastic.co/downloads/kibana)
  - 실행: bin/kibana.bat
  - 접속확인 url: localhost:5601

로컬 환경에서 간단히 데이터 조회 및 Kibana를 통한 데이터 시각화에 사용할 것이라 기본설정을 유지한다.
Elasticsearch를 실 서비스등에 사용하고자 한다면 노드나 인덱스 관련 좀 더 많은 설정을 필요로한다.


## RDB와 비교

**RDB -> Elastic search structure**
  - Database -> Index
  - Table -> Type
  - Row -> Document
  - Column -> Field
  - Schema -> Mapping

**RDB -> Elastic search Query**
- CRUD(Create, Read, Update, Delete)
  - Select -> Get
  - Update -> Put
  - Insert -> Post
  - delete -> delete
- Advanced search operations
  - paging
  - sorting
  - filtering
  - scripting
  - aggregations
  - etc...

## Real Time Processing and Near Real Time Processing

The difference between "real-time" and "near real-time" is both a difference in precision and magnitude. Real-time systems have time constraints that range from microseconds to hours, but those time constraints tend to be fairly precise. Near-real-time usually implies a narrower range of magnitudes -- within human perception tolerances -- but typically aren't articulated precisely.

Near Real Time means effectively RealTime but without guarantees of hitting specific deadlines. Also known as soft real time (as opposed to hard real time).

To summarize, real time has been used when programmer need to constraint the limited running time of function when it is important. For example when you do programming about vehicle brake system, it is important to limit the deadline time until performing the program work that can make skid mark or even crucial accident.

## Structure

![image](https://user-images.githubusercontent.com/48308562/56713803-c2466400-676d-11e9-99f1-bc31ecb6044f.png)

![image](https://user-images.githubusercontent.com/48308562/56777427-831b2000-680c-11e9-99ec-a7d62d432281.png)

![image](https://user-images.githubusercontent.com/48308562/56777483-d9885e80-680c-11e9-919a-d0f31786a0d0.png)

![image](https://user-images.githubusercontent.com/48308562/56777545-20765400-680d-11e9-9453-aa08c214f34d.png)


## Basic command

**Get in to the consol of Kibana**

**Check cluster health:**

`GET /_cat/health?v`

- Green - everything is good (cluster is fully functional)
- Yellow - all data is available but some replicas are not yet allocated (cluster is fully functional)
- Red - some data is not available for whatever reason (cluster is partially functional)

**Create an index**

`PUT /customer?pretty`

**List of nodes in our cluster**

`GET /_cat/nodes?v`

![image](https://user-images.githubusercontent.com/48308562/56777147-d2605100-680a-11e9-8797-af00e9c5c029.png)

- one pimary shard
- one replica(the default)
- zero document in it
- yellow health means that some replicas are not (yet) allocated

**

## Create and bind nodes

![image](https://user-images.githubusercontent.com/48308562/56781305-b74b0c80-681d-11e9-8bae-b74862e4c7da.png)
- elasticsearch 폴더를 복사하여 3개로 만들고 이름을 각각 node-1~3으로 만들어준다.

![image](https://user-images.githubusercontent.com/48308562/56781347-d053bd80-681d-11e9-8d7b-3ac711ada016.png)

`elasticsearch.bat -E cluster.name=es-1 -E node.name=node-2`

- node-2의 config 파일을 위와 같이 설정해주고 prompt에서 실행하거나 config 설정을 하지 않고 위의 코드와 같이 바로 실행을 해주면 node-1이 master로 설정된 상태(먼저 켜놓은 상태)에서 node-2가 실행되면서 node-1과 node-2가 자동으로 bind된다. (여야 하는데 아무리 해도 bind가 되지 않는다. 설명이 리눅스 기반이라 윈도우 쪽에서 설정 방법을 좀 더 알아봐야 겠다. 우선은 노드 1개로 진행하자)
