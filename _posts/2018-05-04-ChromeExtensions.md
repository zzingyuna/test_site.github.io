---
layout: post
---

# 크롬 확장프로그램 만들기

### 확장 프로그램 만들기


확장 프로그램(노란 배경에 'Hello, World!' 뿌려주는게 다인..)  
아래 소스 파일들을 하나의 폴더 내에 만든 후 'icon.png' 파일을 만들어 넣어준다  

manifest.json  
```
{
    "manifest_version": 2,
    
    "name": "Hello_World",
    "description": "Print Hello, World! message",
    "version": "1.0",
    
    "browser_action": {
        "default_icon": "icon.png",
        "default_popup": "popup.html"
    },
    "permissions": [
        "activeTab",
        "<all_urls>"
    ]
}
```


popup.html  
```
<!--
<!DOCTYPE html>
<html>
    <head>
        <style>
            BODY {width : 520px; min-height:250px; }
        </style>
        <script src="popup.js"></script>
    </head>
    <body>
    </body>
</html>
-->
```


popup.js  
```
/*
function sayHello(){
    document.body.innerText = "Hello, World!";
}
window.onload = sayHello;
*/
```



### 크롬에 확장프로그램 등록하기

1. 주소줄에 chrome://extensions를 입력하거나 크롬 브라우저의 메뉴 박스를 클릭하여 도구 더보기 > 확장 프로그램을 선택하면 확장 프로그램 관리 페이지를 볼 수 있다.  
2. 페이지 오른쪽 위에 개발자 모드 체크 박스에 체크를 하자.  
3. 개발자 모드 체크 박스에 체크를 하면 압축해제된 확장 프로그램 로드... 라는 버튼이 나타난다. 이를 눌러 뜨는 창에서 우리가 만든 프로그램이 저장된 디렉토리를 찾아서 확인 버튼을 누르자.  
4. 브라우저 오른쪽 상단에 버튼이 새로 나타나는 것을 확인할 수 있다. 혹 없다면 >> 버튼을 클릭해 보자. 숨어 있을 수도 있다. 버튼을 클릭하면 팝업창이 뜨면서 Hello, World!가 출력되는 것을 확인할 수 있다.  




---
##### 출처  
[https://developer.chrome.com/extensions/getstarted](https://developer.chrome.com/extensions/getstarted)  
[http://hodol.kr/xe/note/17558](http://hodol.kr/xe/note/17558)  

