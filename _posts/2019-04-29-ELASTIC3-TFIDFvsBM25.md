---
title: '[ELASTIC] TFIDF vs BM25'
date: 2019-04-26
categories : [Elastic]
---

## 검색이란

 사용자가 입력한 검색 쿼리에 대해 관련있는 문서를 매칭한 후, 관련성에 따라 정렬하는 과정이다. 관련성(relevance)는 주어진 쿼리와 매칭되는 문서를 관련된 정도에 따라 수치화 한 값으로, 검색 점수(score)라고 부른다.

한 단어 뿐만 아니라 여러 단어의 경우도 가능하다. 각 단어들에 대한 검색 점수를 더해야 하며 이러한 각 단어를 term이라고 한다.

![image](https://user-images.githubusercontent.com/48308562/56885309-1f605380-6aa7-11e9-8622-60f0a7bb7e3b.png)

## 3가지 Key

#### TF(Term Frequency): 단어빈도

![image](https://user-images.githubusercontent.com/48308562/56885469-79f9af80-6aa7-11e9-9034-e321146624a2.png)

많이 등장할수록 문서의 점수는 높아진다.

#### IDF(Inverse Document Frequency): 문서 역빈도

![image](https://user-images.githubusercontent.com/48308562/56885593-c5ac5900-6aa7-11e9-9c2d-c0cec21de62c.png)

IDF = 1/DF(문서 빈도)로 한 단어가 여러 문서에 등장할수록 IDF값은 줄어든다. 불용어("a", "the") 와 같이 거의 항상 등장하는 term이 결과에 영향을 미치지 않게 하는 효과도 가지고 있다.

#### norm: 문서 길이 가중치

![image](https://user-images.githubusercontent.com/48308562/56885772-3eabb080-6aa8-11e9-9e7f-7acf889c87eb.png)

문서의 길이가 길수록 점수가 낮아진다. 예를 들어

- "A가 과일 가게에서 사과와 배를 구매했다."
- "B가 과일 가게에서 사과를 구매했다."

라는 2개의 문장이 있고 사과를 검색했을 때 위 두 문서의 검색 점수가 같으면 불공평하기 때문에 첫번째 문장에 좀 더 가중치를 줘야하기 때문에 존재한다.

## BM25 vs TFIDF
왜 BM25가 왜 더 나은가?

![image](https://user-images.githubusercontent.com/48308562/56884894-0b682200-6aa6-11e9-8175-cd6c628add66.png)

그림. 항목별 TF/IDF와 BM25 비교(출처: Elasticsearch, Improved Text Scoring) with BM25

#### TF(Term Frequency): 단어 빈도

TF의 영향이 줄어든다.

TF에서는 단어 빈도가 높아질수록 검색 점수도 지속적으로 높아지는 반면, BM25에서는 특정 값으로 수렴한다. 즉 검색 결과에 좀 더 노출되고 싶기 위해 `[아이폰][아이폰 중고][아이폰 싸게 팔아요]`와 같은 제목의 글을 올려 놓는 경우가 있는데 BM25에서는 이 가중치를 수렴하게 한다. 직관적으로도 `사과가 사과를 맛있게 사과했다.`같은 문장이 있다면 가중치를 줄여야 할 필요가 있어보인다.

#### IDF(Inverse Document Frequency): 문서 역빈도

IDF의 영향이 커진다.

BM25에서는 DF가 높아지면 검색 점수가 0으로 급격히 수렴하므로, 불용어가 검색 점수에 영향을 덜 미친다.

#### norm: 문서 길이 가중치

문서 길이의 영향이 줄어든다.

BM25에서는 문서의 평균 길이(avgdl)를 계산에 사용하며, 문서의 길이가 검색 점수에 영향을 덜 미친다.

## 왜 BM25가 더 나은가?(2)

BM25가 TF/IDF보다 더 나은 이유는 "Elasticsearch가 그렇게 하기 때문이다".

- 논문에서 그렇다고 한다.
- TREC 등의 챌린지에서 그렇다고 한다.
- 사용자들이 그렇다고 한다
- 루씬 개발자도 그렇다고 한다
- Konard Beiske도 BM25 vs Lucene Default Similarity에서 그렇다고 한다.
