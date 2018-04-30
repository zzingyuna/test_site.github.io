---
layout: post
---


# nodejs 공부


### 루프문

```no-highlight

// loop문 
var x = 0
do {
	x++;
	console.log("x: "+x);
} while (x<3);

```


### 콜백함수 처리 순서 확인

```no-highlight

var fs = require("fs");

// callback 비교
// 1. callback이 없는 경우
var data = fs.readFileSync('input.txt');

console.log(data.toString());
console.log('end');

```
<p>결과)</p>
<p>input.txt 파일 읽은 내용 먼저 뿌려주고</p>
<p>end</p>

```no-highlight
var fs = require("fs");

// callback 비교
// 2. callback이 있는 경우
fs.readFile('input.txt', function (err, data){
	if(err) return console.error(err);
	console.log(data.toString());
});

console.log('end');
```
<p>결과)</p>
<p>end</p>
<p>input.txt 파일 읽은 내용 먼저 뿌려주고</p>



### 콜백함수 처리 순서 확인

주석에 표기된 순서로 호출됨

```no-highlight

// 이벤트 호출 

// events 모듈 사용
var events = require('events');

// EventEmitter 객체 생성
var eventEmitter = new events.EventEmitter();

// 3.EventHandler 함수 생성
var connectHandler = function connected(){
    console.log("Connection Successful");
    
    // data_recevied 이벤트를 발생시키기
    eventEmitter.emit("data_received");
}

// 2.connection 이벤트와 connectHandler 이벤트 핸들러를 연동
eventEmitter.on('connection', connectHandler);

// data_received 이벤트와 익명 함수와 연동
// 4.함수를 변수안에 담는 대신에, .on() 메소드의 인자로 직접 함수를 전달
eventEmitter.on('data_received', function(){
    console.log("Data Received");
});

// 1.connection 이벤트 발생시키기
eventEmitter.emit('connection');

// 5.
console.log("Program has ended");

```



### node js test5

주석에 표기된 순서로 호출됨

```no-highlight
var http = require('http');

// HTTPRequest의 옵션 설정
var options = {
   host: 'localhost',
   port: '80',
   path: '/study'  
};

// 콜백 함수로 Response를 받아온다
var callback = function(response){
   // response 이벤트가 감지되면 데이터를 body에 받아온다
   var body = '';
   response.on('data', function(data) {
      body += data;
   });
   
   // end 이벤트가 감지되면 데이터 수신을 종료하고 내용을 출력한다
   response.on('end', function() {
		// 데이저 수신 완료
		console.log(body);
		var fs = require('fs');
		fs.writeFile("savefile.txt", body, function(err) {
			if(err) {
				return console.log(err);
			}

			console.log("The file was saved!");
		}); 
   });
}
// 서버에 HTTP Request 를 날린다.
// server가 실행되어 있어야 페이지를 가져온다
var req = http.request(options, callback);
req.end();

```



### node js test5

주석에 표기된 순서로 호출됨

```no-highlight
var http = require('http');
var fs = require('fs');
var url = require('url');


// 서버 생성
http.createServer( function (request, response) {  
   // URL 뒤에 있는 디렉토리/파일이름 파싱
   var pathname = url.parse(request.url).pathname;
   
   
   console.log("Request for " + pathname + " received.");
   
   // 파일 이름이 비어있다면 index.html 로 설정
   if(pathname=="/"){
       pathname = "/index.html";
   }
   
   // 파일을 읽기
   fs.readFile(pathname.substr(1), function (err, data) {
      if (err) {
         console.log(err);
         // 페이지를 찾을 수 없음
         // HTTP Status: 404 : NOT FOUND
         // Content Type: text/plain
         response.writeHead(404, {'Content-Type': 'text/html'});
      }else{	
         // 페이지를 찾음	  
         // HTTP Status: 200 : OK
         // Content Type: text/plain
         response.writeHead(200, {'Content-Type': 'text/html'});	
         
         // 파일을 읽어와서 responseBody 에 작성
         response.write(data.toString());		
      }
      // responseBody 전송
      response.end();
   });   
}).listen(8081);


console.log('Server running at http://127.0.0.1:8081/');

```


