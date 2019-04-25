---
title: '[NLP] konlpy를 활용한 형태소 분석을 통해 명사 출현 빈도 분석 '
date: 2019-03-26
categories : [NLP]
---


## Java 설치하기

한국어 형태소 분석을 위해 konlpy를 그냥 설치하려고 했더니

```python
!pip install Konlpy

Failed building wheel for JPype1
failed with error code 1 in C:\Users\zlslsp54\AppData\Local\Temp\pip-install-0jwdszwc\JPype1\
```

JPype1 설치가 안되어 있다는 에러메시지를 받았다. 그래서 자바를 먼저 설치하고 JPype1을 설치해주자.

- https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html 접속
- 자신의 OS 버전과 맞는 Java SE Dev Kit을 설치해준다.
- 환경변수에 추가 해준다. `C:\Program Files\Java\jdk-12`
- JPype1 설치 : https://www.lfd.uci.edu/~gohlke/pythonlibs/#jpype
- JPype랑 모든 Java class libraries에 접근할 수 있게 해주는 툴이다.
- cp는 파이썬 버전, win32 그리고 amd64는 os 비트 숫자를 나타내준다.
- 필자는 python 3.6.7 & 64bit를 사용하기에 `JPype1‑0.6.3‑cp36‑cp36m‑win_amd64.whl` 이 파일을 다운받고 아나콘다 가상환경을 사용하기에 `C:\Users\zlslsp54\Anaconda3`에 설치를 해주고 anaconda prompt에서 가상환경을 activate 해준 상태에서 JPype1 파일이 존재하는 경로로 들어가 설치를 해주면 가상환경에서(만?) 이용할 수 있도록 라이브러리가 생성된다.
- pip install JPype1-0.6.3-cp36-cp36m-win_amd64.whl
- pip install konlpy


## 간단한 형태소 분석 해보기

```python
# 5 종류의 형태소 분석기(한나눔, 꼬꼬마, Kormoran, MeCab, 트위터) 중 트위터 형태소 분석기 사용
# Twitter가 사용하기 가장 좋다.
from konlpy.tag import Twitter
twitter = Twitter()
malist = twitter.pos("언젠가한번쯤은돌아봐주겠죠한없이뒤에서기다리면오늘도차마못한가슴속한마디그댈사랑합니다.", norm=True, stem=True)
print(malist)

[('언젠가', 'Adverb'), ('한번', 'Noun'), ('쯤', 'Suffix'), ('은', 'Josa'), ('돌아보다', 'Verb'), ('한없이', 'Adverb'), ('뒤', 'Noun'), ('에서', 'Josa'), ('기다리다', 'Verb'), ('오늘', 'Noun'), ('도', 'Josa'), ('차마', 'Noun'), ('못', 'Noun'), ('한', 'Determiner'), ('가슴속', 'Noun'), ('한마디', 'Noun'), ('그', 'Noun'), ('대다', 'Verb'), ('사랑', 'Noun'), ('하다', 'Verb'), ('.', 'Punctuation')]
```
- norm 옵션과 stem 옵션을 사실 지정해주지 않아도 실행은 된다.
- norm 옵션은 "그래욬ㅋㅋ?" 을 "그래요" 처럼 변환해준다.
- stem 옵션은 "그렇다"라고 원형을 찾아준다.
- 위 두 파라미터는 base로 설정을 해주자!

머신러닝에 형태소 분석을 적용하기 전에 간단한 예를 살펴보자.

- "토지"책은 Corpus(말뭉치) 형태로 저장되어있다.

```Corpus
</teiHeader>
<text>
<body>
<text>
<head>제 1편 만세(萬歲) 이후</head>
<head>1장 끈 떨어진 연</head>
<p>종로거리를 허둥지둥 걷고 있던 억쇠는 점포마다 문이 닫혀 있는 것을 새삼스럽게 깨닫는다.</p>
<p>"참말로 가게문을 다 닫았구마."</p>
<p>어젯밤 여관에서 들은 얘기가 생각났다. 1030호, 서울의 1030호 상점이 일제히 문을 닫는다는 얘기였다. 갑자기 맥이 빠진다. 억쇠는 어디를 향해 자신이 가고 있는지, 가야 하는지 알 수 없다는 것도 새삼스럽게 깨달아진다.</p>
<p>괴나리봇짐을 엉덩이에 붙이듯 한 손에 들고 우두커니 길 건너편을 바라본다. 건너편 길을 상현이 걷고 있기라도 한 것처럼. 반백이 된 맨상투에 집 나온 지 오래여서 무명 바지저고리엔 땟국이 흐르고 울상이 된 얼굴도 걸레같이 지저분하다. 전차가 땡땡 종을 울리며 지나간다. 지난 삼월에는 경전(京電) 종업원이 파업을 하여 전차는 운행이 중지되었었고 그 무렵 서울의 상가는 한 달 넘게 동맹 철시(同盟撤市)를 했었다. 지금은 시월이다. 또다시 서울의 상가는 동맹 철시를 한 것이다.</p>
<p>'맘들을 합친께 돈에 무서븐 장사꾼도 돈 마다하고 장시를 안 하는데…… 돈보다도 나라가 있이야겄다 그거겄는데, 그렇지마는 저런다고 독립이 될까 몰라? 그러크름 생목심이 날아가고 조선 천지가 들고일어났어도 왜놈우새끼들 어디 끄떡이나 해야 말이제?'</p>
<p>억쇠는 휘적휘적 걷기 시작한다. 아직 날씨는 쌀쌀하다 할 수 없으나 억쇠는 왠지 한기가 든다. 배도 고팠다. 국밥 한 그릇 사먹을 만한 곳이 눈에 띄지 않았고 먹고 싶은 생각도 별로 없다.</p>
<p>'믿을 수가 없다. 이자는 누가 머라 캐도 믿을 수 없단 말이다. 처음에사 만세만 부르믄 독립이 될 줄 알았제. 그러크름 말들 하니께. 흥, 떡 줄 사람은 꿈도 안 꾸는데 김칫국부터 마신 겍이라. 되는 기이 머가 있노. 하낫도 되는 기이 없단 말이다. 우리댁 나으리만 해도 안 그렇건데? 이십 년을 넘기 기다리도 아무 소앵이 없었인께. 군사를 이끌고 쳐들어오기는커냥 사람 얼굴조차 가물치 콧구멍 아니가. 함흥차사라, 함흥차사. 되지도 않을 일이라믄 진작 말 일이제. 식솔들만 생고생을 시키고. 좌우당간에 충신이 되든 역적이 되든 군사를 몰고 와서 쌈을 해야 무신 결판이 나지. 만판 만세 불러봐야 소앵이 있나. 목만 터지제. 목만 터지건데? 모가지는 날아 안 가고? 그거를 두고 개죽음이라 하는 기라. 나겉이 무식한 놈이사 군대쟁이 영문 모르고 나섰지마는.'</p>
<p>울컥울컥 치미는 것을 억쇠는 참는다. 되리라는 독립이 안 되는 것보다 당장 시급한 것은 상현을 찾아야 했으며, 찾는다는 일이 막연했고 닷새 동안 서울 장안을 헤매었지만 아직도 빈 거리를 정처없이 걷고 있다는 일이 울적했던 것이다. 마음과 몸이 지칠 대로 지쳐서 길바닥에 드러눕고 싶은 심정, 그러나 걷기는 걸어야 한다. 이제는 상현의 생사를 근심하기보다 상현에 대한 증오와 분노가 치민다.</p>
<p>'가문에 없는 인사가 어디서 하나 생기가지고 망나니는 접방 나앉으라 칸다. 이름이 좋아 불로초다. 빛좋은 개살구다. 나라일을 하기는 무신 놈의 나라일을 해. 주색잡기도 나라일이라 말가. 대대로 청백리로서 평판이 난 가문에, 어물전 망신은 꼴뚜기가 시킨다 카더마는 이부사 댁도 이자는 콩가리 집안이다. 그놈의 신식 공분가 먼가 그기이 사람 망쳤제. 아주 못쓰게 망쳐놨다 카이. 좌우당간에 어디 가서 사램을 찾노. 세상없이도 찾아보고 가얄 긴데, 그냥 내리가 보제? 초상 날 기다, 초상 날 기라 카이.'</p>
<p>속으로 중얼거리며 걷는데</p>
<p>"여보시오, 늙은이."</p>
<p>"야? 나 말입니까."</p>
<p>억쇠의 눈이 휘둥그래진다. 금테 안경을 쓰고 콧수염에 양복을 잘 차려입은 신사다. 나이는 삼십 안팎인 듯 안경 속의 눈매가 갸름하다.</p>
```

*예제: 국립국어원의 "토지7"책의 명사 TOP50 빈도수 단어 추출하기*

```python
import codecs
from bs4 import BeautifulSoup
from konlpy.tag import Twitter

# utf-16 인코딩으로 파일을 열고 글자를 출력하기
fp = codecs.open("BEXX0004.txt", 'r', encoding='utf-16')
type(fp)

keys = sorted(word_dic.items(), key = lambda x : x[1], reverse=True) # sort by numner + 역순으로
for word, count in keys[:50]:
    print("{0}({1}) ".format(word, count), end = "")
print()

것(5946) 말(3216) 그(3093) 사람(1947) 안(1782) 생각(1485) 내(1278) 이(1257) 놈(1245) 일(1134) 수(1089) 얼굴(1002) 집(966) 나(954) 상현(867) 때(858) 홍(840) 거(816) 못(702) 소리(672) 돈(660) 여자(657) 눈(639) 곳(636) 제(627) 얘기(627) 우리(615) 좀(603) 또(588) 한복(588) 니(585) 술(576) 그것(552) 조준(549) 네(543) 구(534) 명(531) 자신(501) 듯(480) 년(477) 더(468) 하나(462) 댁(450) 서희(450) 손(444) 때문(444) 게(438) 두(402) 어디(399) 길(384)
```


## Word2Vec: 문장 내부의 단어를 벡터로 변환하는 도구

단어의 의미를 벡터로 표현하는 Word2Vec를 이용하여 **연관된 단어를 추출** 하거나 단어와 단어의 유사도를 확인할 수 있습니다. 또한 의미를 선형 계산할 수 있어서 '왕자 - 남성 + 여성 = 공주" 등의 계산도 할 수 있습니다. 글을 읽게 하고 단어를 2차원 벡터상에 표현시켜 단어간의 유사도를 파악하는 식으로 진행됩니다.
![image](https://user-images.githubusercontent.com/48308562/55209316-4831c680-5225-11e9-9ba0-f1c020c2f07c.png)


## Gensim의 Word2Vec로 "토지" 읽어보기
자연어 처리를 위한 라이브러리입니다. Word2Vec 기능 이외에도 글자 처리를 위한 여러 가지 기능을 가지고 있습니다.
##### (Corpus뿐 아니라 그냥 텍스트 데이터도 읽을 수 있는건가?)

- pip install gensim

```python
import codecs
from bs4 import BeautifulSoup
from konlpy.tag import Twitter
from gensim.models import word2vec

fp = open("BEXX0004.txt", 'r')
fp

<_io.TextIOWrapper name='BEXX0004.txt' mode='r' encoding='cp949'>
```

- 국립국어원에서 Corpus형식으로 받아온 text의 파일 형식이 cp949 형식으로 인코딩 되어 있는 모습입니다.(code page 949는 Microsoft의 한국어 문자 인코딩 테이블) 이를 전 세계의 모든 문자를 일관되게 표현할 수 있는 유니코드로 codecs 모듈을 이용해 다시 인코딩을 해줍니다.
- 파이썬에서의 인코딩/디코딩 tip
  - 파이썬 코드 안에서는 모두 유니코드 처리
  - 현재 파이썬 프로그램 밖과 문자열 데이터를 주고 받을 때
    - 받을 때: 최대한 빨리 유니코드로 인코딩하여 유니코드로 처리
    - 보낼 때: 유니코드로 작성하다가 최대한 늦게 다른 코드로 인코딩하여 전송


```python
fp = codecs.open("BEXX0004.txt", "r", encoding="utf-16")
soup = BeautifulSoup(fp, "html.parser")
body = soup.select_one("body > text")
text = body.getText()

#텍스트 한 줄씩 처리하기
twitter = Twitter()
results = []
lines = text.split("\n")

malist_pre = twitter.pos(lines[1])

for line in lines:
    # 형태소 분석하기
    # 단어의 기본형 사용
    malist = twitter.pos(line, norm=True, stem=True)
    r = []
    for word in malist:
        # 어미/조사/구두점 등은 대상에서 제외
        if not word[1] in  ["Josa", "Emoi", "Punctuation"]:
            r.append(word[0])
    rl = (" ".join(r)).strip()
    results.append(rl)

# 파일로 출력하기
wakati_file = 'toji.wakati'
with open(wakati_file, 'w', encoding='utf-8') as fp:
    fp.write("\n".join(results)) # 리스트마다 \n 조인    


# Word2Vec 모델로 만들기
data = word2vec.LineSentence(wakati_file)
model = word2vec.Word2Vec(data, size=200, window=10, hs=1, min_count=2, sg=1)
model.save("toji.model") #word2vec가 사용 가능한 형태로 만들어 짐.
print('ok')    

from gensim.models import word2vec
model = word2vec.Word2Vec.load("toji.model")

model.most_similar(positive=["집"])

[('찾아가다', 0.8188856840133667),
 ('판', 0.8169862031936646),
 ('주선', 0.811568021774292),
 ('사겄다', 0.8092672228813171),
 ('되찾다', 0.7998690009117126),
 ('진주', 0.7975897192955017),
 ('이평', 0.7945327758789062),
 ('출가', 0.7933693528175354),
 ('하동', 0.7931393384933472),
 ('심부름', 0.7921744585037231)]
```
- size는 벡터를 몇 차원으로 만들 것이냐인데 데이터가 많을수록 차원을 늘려줘도 되지만 너무 많다고 좋은건 아니다.
- positive 말고 negative도 가능.
