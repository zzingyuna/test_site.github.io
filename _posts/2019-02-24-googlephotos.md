---
layout: post
---

# 구글 api를 활용한 구글 포토 데이터 가져오기

구글 api를 활용한 포토 목록 가져오기  
  
  
  
### 아래 사이트에서 계정 작업  
[https://console.developers.google.com/](https://console.developers.google.com/)  
  
-- 내 개인 포토 계정  
https://photos.google.com/album/AF1QipNUdA_hS9CoOVtV3rS1c7B2mwqA_xifTaX7_90N
  
-- 알천 구글포토 공유 링크  
https://photos.google.com/share/AF1QipN8EpdoUEsg-heX3tF6wX7v-TXIlaBdHqvcBN5I-3AR_Rpx1g_CkuTArueNddPjlg?key=WWEyTXoyclVXWHQ2QTRSZnRFUzRoVUJyZnJ6RDFR
  
- api 권한 설정 페이지  
https://developers.google.com/apis-explorer/?hl=ko#p/
  
클라이언트 ID	
307558157269-tfuep5e4ece7hj0lun4gog6lki2mpht2.apps.googleusercontent.com
  
클라이언트 보안 비밀	
tOMcEukTtplWLiauC-vRvWOJ
  
- api client 라이브러리 javascript 사용 방법  
https://developers.google.com/api-client-library/javascript/start/start-js
  
구글포토api 관련 링크  
[https://developers.google.com/photos/library/guides/get-started](https://developers.google.com/photos/library/guides/get-started)  
[https://developers.google.com/photos/library/reference/rest/?hl=bg](https://developers.google.com/photos/library/reference/rest/?hl=bg)  
  
-- 잘 동작함 (들어가면 click누르면 앨범 리스트 데이터 가져옴)  
https://accounts.google.com/o/oauth2/v2/auth?response_type=code&redirect_uri=http://localhost/study/test.html&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fphotoslibrary.readonly%20profile&client_id=307558157269-tfuep5e4ece7hj0lun4gog6lki2mpht2.apps.googleusercontent.com





---

##### 1.계정 Auth권한 할당
클라이언트 ID 및 API KEY 생성  
  
  <img src="https://zzingyuna.github.io/image/googlephoto1.JPG"/>  
  <img src="https://zzingyuna.github.io/image/googlephoto2.JPG"/>  
  <img src="https://zzingyuna.github.io/image/googlephoto3.JPG"/>  
  <img src="https://zzingyuna.github.io/image/googlephoto4.JPG"/>  
  <img src="https://zzingyuna.github.io/image/googlephoto5.JPG"/>  
  
  
  
```
<html>
<head>
</head>
<body>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://apis.google.com/js/api.js"></script>
<script src="https://apis.google.com/js/platform.js?onload=onLoadCallback" async defer></script>

<a href="#" onclick="test(); return false;">click!!!</a>
<div>
	<ul id="photolist">
	</ul>
</div>
<script>

    function onLoadCallback() {

      console.log('onLoadCallback');
      gapi.load('auth2', function() {
        gapi.auth2.init({
            client_id: '307558157269-tfuep5e4ece7hj0lun4gog6lki2mpht2.apps.googleusercontent.com',
            //This two lines are important not to get profile info from your users
            fetch_basic_profile: false,
            scope: 'email'
        });        
      });     
    }

</script>

<script>
function start() {
  // 2. Initialize the JavaScript client library.
	gapi.client.request({
	  //'path': 'https://photoslibrary.googleapis.com/v1/albums',
	  'path': 'https://photoslibrary.googleapis.com/v1/mediaItems',
	})
	.then(function(response) {
	console.log(response.result);
		var strList = "";
		for(var i=0; i< response.result.mediaItems.length ; i++){
			strList = strList+ "<li><img src='"+response.result.mediaItems[i].baseUrl+"'/>"+response.result.mediaItems[i].filename+"</li>";
			$("#photolist").html(strList);
		}
	}, function(reason) {
	console.log('Error: ' + reason.result.error.message);
	});
};


function test(){
gapi.load('client', start);
}
</script>
    
    
</body>
</html>

```
  
  
   
   
