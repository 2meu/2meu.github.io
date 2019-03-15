---
title: "[에러] Jupyter No module named 문제 해결"
date: 2019-03-15
categories: jupyter no_module_named
---

## 문제

anaconda prompt에서 conda install selenium을 해주고 navigator에서 selenium이 설치되어있는지 확인 후 anaconda prompt에서 jupyter notebook 실행 후에 from selenium import webdriver를 수행 했지만

`No module named 'selenium' 문제가 발생했다.`

```python
from selenium import webdriver

---------------------------------------------------------------------------
ModuleNotFoundError                       Traceback (most recent call last)
<ipython-input-1-abb2a9e03f2a> in <module>()
----> 1 import selenium

ModuleNotFoundError: No module named 'selenium'
```

## 이유

jupyter notbook과 anaconda prompt에서 path를 확인했더니

Jupyter
```python
import sys
sys.path

['',
 'C:\\Users\\2Dub\\Anaconda3\\python36.zip',
 'C:\\Users\\2Dub\\Anaconda3\\DLLs',
 'C:\\Users\\2Dub\\Anaconda3\\lib',
 'C:\\Users\\2Dub\\Anaconda3',
 'C:\\Users\\2Dub\\Anaconda3\\lib\\site-packages',
 'C:\\Users\\2Dub\\Anaconda3\\lib\\site-packages\\win32',
 'C:\\Users\\2Dub\\Anaconda3\\lib\\site-packages\\win32\\lib',
 'C:\\Users\\2Dub\\Anaconda3\\lib\\site-packages\\Pythonwin',
 'C:\\Users\\2Dub\\Anaconda3\\lib\\site-packages\\IPython\\extensions',
 'C:\\Users\\2Dub\\.ipython']
```

prompt
```bash
(crawling) C:\Users\2Dub>python
Python 3.7.2 (default, Feb 21 2019, 17:35:59) [MSC v.1915 64 bit (AMD64)] :: Anaconda, Inc. on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.path
['', 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\python37.zip', 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\DLLs', 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\lib', 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling', 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\lib\\site-packages']
```

jupyter와 prompt의 path경로가 다르네..

서로 커널이 공유가 되어있지 않은 것 같다.

## 해결

커널을 설치하기 위해

- anaconda prompt를 관리자 권한으로 실행
  - activate Crawling
  - conda install nb_conda

주피터에서 path를 다시 확인해보니

```python
import sys
sys.path

['C:\\Users\\2Dub\\tensorflow',
 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\python37.zip',
 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\DLLs',
 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\lib',
 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling',
 '',
 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\lib\\site-packages',
 'C:\\Users\\2Dub\\Anaconda3\\envs\\crawling\\lib\\site-packages\\IPython\\extensions',
 'C:\\Users\\2Dub\\.ipython']
```

서로 공유가 되었다. 셀레니움도 잘 동작한다.
