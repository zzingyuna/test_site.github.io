---
layout: post
---

#### Node.js에서 MongoDB Query 날리기

nodeJs에서 MongoDB 쿼리 날리고 결과 뿌리는 간단한 소스는 아래와 같다.  
소스를 실행하기 전에 nodeJs가 설치되어 있어야 하고  
아래와 같이 mongodb 라이브러리도 추가 설치해줘야한다.  
```
npm install mongodb
```



nodeJs 소스  
```
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("[데이터베이스명]");
  var query = { [컬럼명]: [조건] };
  // 예) var query = { name: /^S/ }; name값이 S로 시작하는 데이터 조회
  dbo.collection("[테이블명]").find(query).toArray(function(err, result) {
    if (err) throw err;
    console.log(result);
    db.close();
  });
});
```

- 출처  
[https://www.w3schools.com/nodejs/nodejs_mongodb_query.asp](https://www.w3schools.com/nodejs/nodejs_mongodb_query.asp)  

