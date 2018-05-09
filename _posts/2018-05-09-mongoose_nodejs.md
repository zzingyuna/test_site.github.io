---
layout: post
---

# Mongoose를 이용하여 Nodejs에 MongoDB 연결하기  


```

// 1. mongoose 모듈 가져오기
var mongoose = require('mongoose');
// 2. testDB 세팅
mongoose.connect('mongodb://localhost:27017/test');
// 3. 연결된 testDB 사용
var db = mongoose.connection;
// 4. 연결 실패
db.on('error', function(){
    console.log('Connection Failed!');
});
// 5. 연결 성공
db.once('open', function() {
    console.log('Connected!');
});


// 6. Schema 생성. (혹시 스키마에 대한 개념이 없다면, 입력될 데이터의 타입이 정의된 DB 설계도 라고 생각하면 됩니다.)
var users = mongoose.Schema({
    name : 'string',
    age : 'number'
});


// 7. 정의된 스키마를 객체처럼 사용할 수 있도록 model() 함수로 컴파일
var users = mongoose.model('users', users);

// 10. Student 레퍼런스 전체 데이터 가져오기
users.find(function(error, users){
    console.log('--- Read all ---');
    if(error){
        console.log(error);
    }else{
        console.log(users);
    }
})

```



출처  
[https://javafa.gitbooks.io/nodejs_server_basic/content/chapter12.html](https://javafa.gitbooks.io/nodejs_server_basic/content/chapter12.html)  

