---
layout: post
---

# 구글 포토 가져오기

구글 api를 활용한 포토 목록 가져오기
  
  
  
아래 사이트에서 계정 작업  
[https://console.developers.google.com/](https://console.developers.google.com/)  


1.계정 Auth권한 할당
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

<script src="https://apis.google.com/js/api.js"></script>
<script src="https://apis.google.com/js/platform.js?onload=onLoadCallback" async defer></script>

<a href="#" onclick="test(); return false;">click!!!</a>

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
	  'path': 'https://photoslibrary.googleapis.com/v1/albums',
	})
	.then(function(response) {
	console.log(response.result);
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
  
  
   
   
