---
layout: post
---

# 크롬 확장프로그램 만들기

### 확장 프로그램 만들기


확장 프로그램('Change Text'버튼을 누르면 문구가 변경되고, 확장프로그램 옵션에서 선택한 색깔을 브러우저 배경색으로 변경해주는 기능)  
아래 소스 파일들을 하나의 폴더 내에 만든 후 'icon.png' 파일을 만들어 넣어준다  

###### manifest.json  
```
{
    "manifest_version": 2,
    "name": "Getting Started Example",
    "description": "Getting Started Tutorial",
    "version": "1.0",
    "permissions": ["activeTab","storage","declarativeContent"],
    "page_action": {
      "default_popup": "popup.html",
      "default_icon": {
        "16": "icon.png",
        "32": "icon.png",
        "48": "icon.png",
        "128": "icon.png"
      }
    },
    "options_page": "options.html",
    "icons": {
      "16": "icon.PNG",
      "32": "icon.PNG",
      "48": "icon.PNG",
      "128": "icon.PNG"
    },
    "background": {
      "scripts": ["background.js"],
      "persistent": false
    }
}
```
>manifest.json파일에서 'permissions' 어떤 기능을 사용(허용)할 것이지 세팅  
>options_page에 옵셥을 누르면 보여질 내용을 적용  


###### background.js  
```
chrome.runtime.onInstalled.addListener(function() {
    chrome.storage.sync.set({color: '#3aa757'}, function() {
      console.log('The color is green.');
    });
    chrome.declarativeContent.onPageChanged.removeRules(undefined, function() {
      chrome.declarativeContent.onPageChanged.addRules([{
        conditions: [new chrome.declarativeContent.PageStateMatcher({
          //pageUrl: {hostEquals: 'developer.chrome.com'},
        })
        ],
            actions: [new chrome.declarativeContent.ShowPageAction()]
      }]);
    });
  });

```
>chrome://extensions/페이지에서 '뷰 검사 백그라운드 페이지' 버튼을 누르면 console로 뿌린 내용이 보여진다.  
>declarativeContent를 통해 새탭, 새페이지를 열때는 배경색 적용이 안된 초기값으로 보여지도록 하고  
>주석처리된 pageUrl:부분을 주석제거하면 해당 url만 확장프로그램이 실행되도록 설정할 수 있다.  


###### popup.html  
```
<!DOCTYPE html>
  <html>
    <head>
      <style>
        BODY {width : 100px; min-height:50px; }
        #changeColor {
          height: 30px;
          width: 30px;
          outline: none;
        }
      </style>
    </head>
    <body>
      <p id="view_text"><p>
      <button id="changeText">Change Text..</button>
      <button id="changeColor"></button>
      <script src="popup.js"></script>
    </body>
  </html>
```
>확장프로그램 실행시 뜨는 화면  


###### popup.js  
```

function sayHello(){
    document.getElementById("view_text").innerText = "Hello, World!";
}
window.onload = sayHello;


let changeColor = document.getElementById('changeColor');
chrome.storage.sync.get('color', function(data) {
  changeColor.style.backgroundColor = data.color;
  changeColor.setAttribute('value', data.color);
});

changeColor.onclick = function(element) {
    let color = element.target.value;
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
        chrome.tabs.executeScript(
          tabs[0].id,
          {code: 'document.body.style.backgroundColor = "' + color + '";'});
    });
};


let changeText = document.getElementById('changeText');
changeText.onclick = function(element) {
    document.getElementById("view_text").innerText = "Change Text..";
};

```
>처음 확장프로그램 실행시 팝업이 뜨면서 'Hello, World!' 문구와 버튼 두개가 뜬다  
>'Change Text'버튼을 클릭하면 'Hello, World!' 문구가 변경되고,  
>색깔 버튼을 클릭하면 해당 색으로 브러우저 배경화면이 변경된다.  


###### options.html  
```
 <!DOCTYPE html>
  <html>
    <head>
      <style>
        button {
          height: 30px;
          width: 30px;
          outline: none;
          margin: 10px;
        }
      </style>
    </head>
    <body>
      <div id="buttonDiv">
      </div>
      <div>
        <p>Choose a different background color!</p>
      </div>
    </body>
    <script src="options.js"></script>
  </html>
```
>chrome://extensions/페이지에서 옵션을 누르면 색깔을 선택할 수 있는 옵션창이 뜬다.  
>색깔을 선택하면 확장프로그램에서 보여지는 색깔버튼의 색이 변경된다.  


###### options.js  
```
let page = document.getElementById('buttonDiv');
  const kButtonColors = ['#3aa757', '#e8453c', '#f9bb2d', '#4688f1'];
  function constructOptions(kButtonColors) {
    for (let item of kButtonColors) {
      let button = document.createElement('button');
      button.style.backgroundColor = item;
      button.addEventListener('click', function() {
        chrome.storage.sync.set({color: item}, function() {
          console.log('color is ' + item);
        })
      });
      page.appendChild(button);
    }
  }
  constructOptions(kButtonColors);
```
>옵션창에서 변경할 수 있는 색깔을 kButtonColors에 추가하면 지정할 수 있다.  




### 크롬에 확장프로그램 등록하기

1. 주소줄에 chrome://extensions를 입력하거나 크롬 브라우저의 메뉴 박스를 클릭하여 도구 더보기 > 확장 프로그램을 선택하면 확장 프로그램 관리 페이지를 볼 수 있다.  
2. 페이지 오른쪽 위에 개발자 모드 체크 박스에 체크를 하자.  
3. 개발자 모드 체크 박스에 체크를 하면 압축해제된 확장 프로그램 로드... 라는 버튼이 나타난다. 이를 눌러 뜨는 창에서 우리가 만든 프로그램이 저장된 디렉토리를 찾아서 확인 버튼을 누르자.  
4. 브라우저 오른쪽 상단에 버튼이 새로 나타나는 것을 확인할 수 있다. 혹 없다면 >> 버튼을 클릭해 보자. 숨어 있을 수도 있다. 버튼을 클릭하면 팝업창이 뜨면서 Hello, World!가 출력되는 것을 확인할 수 있다.  




---
##### 출처  
[https://developer.chrome.com/extensions/getstarted](https://developer.chrome.com/extensions/getstarted)  
[http://hodol.kr/xe/note/17558](http://hodol.kr/xe/note/17558)  

