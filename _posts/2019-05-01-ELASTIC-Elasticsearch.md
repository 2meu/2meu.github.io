---
title: '[ELASTIC] Elasticsearch'
date: 2019-05-01
categories : [Elastic]
---

## Create an index on CMD

put index customer

`curl -X PUT "localhost:9200/customer?pretty"`

show indices

`curl -X GET "localhost:9200/_cat/indices?v"`

health를 보면 yellow라고 되어있는데 이는 Elasticsearch는 default로 replica를 생성하는데 우리는 현재 node를 1개만 사용하고 있기 때문에 replica가 allocated하지 못해서 생기는 error이다.
