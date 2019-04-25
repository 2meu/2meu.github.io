---
title: '[Audio data] Audio Data Analysis using Deep Learning'
date: 2019-04-12
categories : [Audio]
---

## File format

오디오 파일 형식엔 대표적으로 3개의 파일 형식이 존재한다.

- wav(Waveform Audio File) format
- mp3(MPEG-1 Audio Layer 3) format
- WMA (Window Media Audio) format

![image](https://user-images.githubusercontent.com/48308562/56014737-73daa380-5d31-11e9-9da6-4a133ccdd576.png)

실제 오디오 데이터는 시간의 변화에 따른 진폭으로 기록되어진다.(amplitude of audio change with respect to time.)


## Data Handling

![image](https://user-images.githubusercontent.com/48308562/56015134-d41e1500-5d32-11e9-91d8-1e35e5ba3adf.png)

오디오 파일이 존재한다면 가장 먼저 해야할 작업은 오디오 데이터를 컴퓨터가 이해할 수 있는 데이터로 바꿔주는 일이다. 위 그림처럼 2초 동안의 오디오 파일이 있다면 잠깐씩 쉬면서 파란 점들의 데이터를 얻는 과정을 **sampling of audio data** 라고 하며 파란 점이 뽑히는 비율을 정해주는 것을 **sampling rate** 라고 부른다.(sampling rate가 높을 수록 많은 데이터가 뽑힌다.)

오디오 데이터를 컴퓨터가 이해하는 데이터로 바꿔주는 또 다른 방법엔 **frequency domain(주파수 영역)** 가 존재한다. 이 방법을 수행한다면 우리는 작은 computational 공간만 필요하기에 좋은 방법이다.

![image](https://user-images.githubusercontent.com/48308562/56015498-31ff2c80-5d34-11e9-86e7-4d7072cfe1af.png)

위 사진은 1개의 오디오 signal을 3개의 pure한 signal로 분해한 모습이다. 각 signal은 frequency domain(주파수 영역)에서 unique한 값을 갖는다.

다음 단계는 이 나눠진 오디오 signal에서 features를 뽑아내야한다.

![image](https://user-images.githubusercontent.com/48308562/56016014-e77eaf80-5d35-11e9-8fd9-0f51d28166a7.png)

위 사진은 audio feature의 category를 보여준다.

- Time domain features (eg. RMSE of waveform)
- Frequency domain features (eg. Amplitude of individual frequency)
- Perceptual features(지각적 특징?)
- Windowing features (eg. Hamming distances of windows)

## 잡음 제거 예제

이번에 할 데이터 분석은 잡음을 판단하는 classification 모델을 만들어 볼 것이다.

https://datahack.analyticsvidhya.com/contest/practice-problem-urban-sound-classification/

![image](https://user-images.githubusercontent.com/48308562/56016658-f9615200-5d37-11e9-9f7a-23e7c65f35f2.png)

위 url에 접속하여 **audio files from here** Train, Test, Sample Submissions csv 파일을 다운로드 받는다.

![image](https://user-images.githubusercontent.com/48308562/56112884-47c05c00-5f97-11e9-942a-e9f780ae724d.png)


데이터는 아래와 같이 오디오의 고유한 인덱스 값인 ID와 어떤 잡음이 섞여있는지를 나타내는 Class가 존재하고 총 8732개(labeled train 5437, unlabeled test 3299)의 파일이 존재한다.

- ID: unique ID if sound excerpt
- Class: type of sound
  - air conditioner,
  - car horn,
  - children playing,
  - dog bark,
  - drilling,
  - engine idling,
  - gun shot,
  - jackhammer,
  - siren, and
  - street music

![image](https://user-images.githubusercontent.com/48308562/56017170-688b7600-5d39-11e9-9ea3-453e7258bb71.png)

주피터에서 소리를 들어보고싶다면 아래처럼 코드를 작성해보자.(children playing)

![image](https://user-images.githubusercontent.com/48308562/56020380-f9fee600-5d41-11e9-9285-a0d8beae4df1.png)

이제 이 오디오 파일들을 주피터 노트북에 numpy 배열로 가져와보자. 그러기 위해 우리는 librosa library를 사용할 것이다. data에는 오디오 파일의 (1) numpy array가 들어있고 (2) sampling_rate에는 말 그대로 sampling_rate가 들어있다.

+ sampling_rate(또는 sampling frequency)란 단위시간(주로 초)당 샘플링 횟수를 의미하고 샘플 주파수(Fs)와 같은 말이 된다. 단위는 헤르츠이다.

![image](https://user-images.githubusercontent.com/48308562/56022098-f1101380-5d45-11e9-95e7-ed5a19cf2bd2.png)

*빨간색이 샘플링 횟수를 의미.*

```python
# !pip install librosa

import librosa
data, sampling_rate = librosa.load('./Train/2022.wav')
```

```python
print(data)

array([-0.03080229, -0.05436678, -0.05742148, ..., -0.01646075,
       -0.024482  , -0.01962386], dtype=float32)
```

```python
print(sampling_rate)

22050 # 1초에 22050번 샘플링 되었다.
```

그럼 librosa를 통해 얻은 (1) audio data array 와 (2) sampling_rate 를 이용해 원래 audio 파일의 flow가 어떻게 생겼는지 보기 위해 아래 코드를 작성해보자.

```python
%pylab inline
import os
import pandas as pd
from librosa import display
import glob


plt.figure(figsize(12, 4))
display.waveplot(data, sr=sampling_rate)
```

![image](https://user-images.githubusercontent.com/48308562/56113370-a1755600-5f98-11e9-8c09-7488efdb9394.png)

다음 코드를 작성하여 다른 오디오 파일의 Class와 파형 그림을 random으로 알아보자

```python
import pandas as pd
import librosa
train = pd.read_csv('./train.csv')

i = random.choice(train.index)

data_dir = 'C:/Users/zlslsp54/sean/study/urban_sound_classification'

audio_name = train.ID[i]
path = os.path.join(data_dir, 'Train', str(audio_name) + '.wav')

print('Class: ', train.Class[i])

x, sr = librosa.load(path)
plt.figure(figsize=(12, 4))
librosa.display.waveplot(x, sr=sr)
```

- Class: street_music
![image](https://user-images.githubusercontent.com/48308562/56117165-78f25980-5fa2-11e9-9e73-c508e5ece7de.png)

- Class: dog_bark
![image](https://user-images.githubusercontent.com/48308562/56117265-b1923300-5fa2-11e9-9142-d31624cd236f.png)

- Class: dog_bark
![image](https://user-images.githubusercontent.com/48308562/56117321-cd95d480-5fa2-11e9-800d-29d2d4855594.png)

- Class: children_playing
![image](https://user-images.githubusercontent.com/48308562/56117365-e0100e00-5fa2-11e9-8834-1bd537e62ddb.png)

아직 파형만 봐서는 잘 모르겠다.

train의 Class를 분포를 확인해보자.

```python
train.Class.value_counts()/5435
```
```python
jackhammer          0.122907
engine_idling       0.114811
siren               0.111684
children_playing    0.110396
street_music        0.110396
drilling            0.110396
air_conditioner     0.110396
dog_bark            0.110396
car_horn            0.056302
gun_shot            0.042318
Name: Class, dtype: float64
```

jackhammer가 가장 많으니 test를 모두 jackhammer로 채울 수 있다... 는 방법은 직관적이지만 완벽한 방법은 아니다.

## Building better model

1. Load audio files
2. Extract features from audio
3. Convert the data to pass it in our deep learning model
4. Run a deep learining model and get results
