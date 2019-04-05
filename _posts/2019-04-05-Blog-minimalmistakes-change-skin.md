---
title: "[블로그] Minimal Mistakes Configuration"
date: 2019-04-05
categories: github blog jekyll minimal_mistakes-theme window _con.yml
---

## skin

-config.yml 파일에 들어가 보면 theme과 skin을 선택하는 부분이 존재합니다.

```
# theme                  : "minimal-mistakes-jekyll"
# remote_theme           : "mmistakes/minimal-mistakes"
minimal_mistakes_skin    : "default" # "air", "aqua", "contrast", "dark", "dirt", "neon", "mint", "plum", "sunrise"
```

minimal_mistakes_skin 부분에 default로 설정되어 있는데 저는 눈의 피로도가 낮은 dark로 skin을 변경하겠습니다. 또한 여기서 theme을 활성화 해주어야 하는데 앞에 # 부분을 제거하여 활성화 해줍니다. (저희는 Ruby의 Gem-based method 환경이므로 theme을 활성화 해주고 Gem-based가 아니라면 remote_theme를 활성화 해줍니다.) 그리고 나서 다시 Ruby terminal에서 bundle로 접속해주면 테마가 바뀌어 있는것을 확인할 수 있습니다. (확실히 하얀색 배경보다 눈이 편안합니다.)

```
chcp chcp65001
bundle exec jekyll serve
```

![image](https://user-images.githubusercontent.com/48308562/55598499-fd1c3400-578d-11e9-8684-6ac4339fe5d3.png)
