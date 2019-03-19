---
title: "[스크레이핑 - 유튜브] API를 이용한 Youtube 스크레이핑"
date: 2019-03-13
---

## 한 채널의 모든 동영상의 정보를 얻어오기(with Playlist Google API)

- API얻기 about Best New Trailers = February 2019

2. Data API에서(youtube api - 참조 - channels - list) part = contentDetails,
id = {channel id}를 입력 후 채널에 있는 모든 동영상 리스트가 있는 uploads id를 얻음

3. playlistItems-list에서 part = snippet(모든 정보), id={uploads id} 를 입력하면
원하는 정보가 출력된다.

4. API 페이지 밖에서 실행할땐 part, playlistId, key 순서로 입력
"https://www.googleapis.com/youtube/v3/playlistItems?
part=snippet
&playlistId=PLScC8g4bqD45eu7f_keu6C-SJ31wxZip6
&key=AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0"
![ruby](/assets/img/youtube-api1.PNG)

5. consol창에 javascript 코드 사용하여 snippet에 있는 title, link, publishedAt
정보를 가져온다.
![console](https://trello-attachments.s3.amazonaws.com/5c2bfa13c6ba7a7294e9a816/5c7ceb56f068810be15d8a5f/c7fe24e45a0d9dce052b73026ce8b21d/image.png)


- 엑셀에 label 지정한 후 저장

(ref:https://imyeonn.github.io/blog/web/39/)


```javascript
var apiKey ='AIzaSyBt3aTnFJz9zvryJq-tHe3wq7hNzjJtjK0';
var playlistId = 'PLScC8g4bqD45eu7f_keu6C-SJ31wxZip6';
var url = 'https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&playlistId=' + playlistId + '&key=' + apiKey + '&maxResults=50';

function call(nextToken) {

  // 지금부터 XMLHttpRequest와 관련된 기능을 사용하겠습니다.
  var req = new XMLHttpRequest();
  // url에 해당되는 주소로 접속하고 싶어요.
  var pageToken = '';

  if (nextToken) {
    pageToken = '&pageToken=' + nextToken;
  }
  //  세번째 파라미터(생략 가능)는 요구가 비동기식(Asynchronous)으로 수행될 지를 결정합니다. 만약 이 파라미터가 true(기본값) 으로 설정된 경우에는 자바스크립트 함수가 지속적으로 수행될 수 있어 서버로부터 응답을 받기 전에도 유저와 페이지의 상호작용이 계속 진행됩니다. 이것이 AJAX 의 첫번째 A (Asynchronous : 비동기성) 입니다.
  //false로 설정된 경우 동기적으로 작동합니다. (send() 함수에서 서버로부터 응답이 올 때까지 기다림)역자 덧붙임
  req.open('GET', url + pageToken, true);

  // 위에서 생성한 httpRequest의 onreadystatechange property에 특정 함수 function(aEvt)를 할당하면
  // 요청에 대한 상태가 변화할 때 특정 함수 function(aEvt)가 불리게 됩니다.
  req.onreadystatechange = function (aEvt) {
    //XMLHttpRequest.DONE (상수 4로 정의되어 있습니다.) 라면, 서버로부터 모든 응답을 받았으며 이를 처리할 준비가 되었다는 것을 뜻합니다.
    if (req.readyState == 4) {
      // AJAX 요청이 정상적으로 처리되었는지 아닌지만을 검사하기 위해 응답 코드가 200 OK 인지 검사
      if (req.status == 200) {
        // http_request.responseText – 서버의 응답을 텍스트 문자열로 반환할 것이다.
        // text 형식이므로 JSON 형식으로 만들어주자
        var result = JSON.parse(req.responseText);
        var items = result.items;
        for (var i = 0; i < items.length; i++) {
          var vid = items[i].snippet.resourceId.videoId;
          var vurl = 'http://www.youtube.com/watch&v=' + vid;
          console.log(items[i].snippet.title + '\t' + vurl + '\t' + items[i].snippet.publishedAt);
        }
        if (result.nextPageToken) {
          call(result.nextPageToken);
        }
      } else {
        alert("Error loading page\n");
      }
    }
  };
  // 접속을 시작합니다.
  req.send(null);
}
call();

```
