---
layout: post
---

# 구구단 게임

자바스크립트로 만든 게임 - 구구단 게임

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="description" content="Free Web tutorials">
  <meta name="keywords" content="HTML,CSS,XML,JavaScript">
  <meta name="author" content="John Doe">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Page Title</title>
  <style>
body {
  background-color: gainsboro;
}

h1 {
  color: darkcyan;
  text-align: center;
}

p {
  font-family: verdana;
  font-size: 20px;
}

  </style>
  <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
</head>
<body>

<h1>구구단 게임!!</h1>
<p>컴퓨터가 내는 구구단 맞추기</p>
<br />

<button id="btnStart" onclick="Start()">Start</button>

<br />
<br />
<p>남은 시간 : <span id="time"></span></p>
<p>점수 : <span id="score"></span></p>


<br />
<p id="computer">구구단질문</p>
<input type="text" id="txtUserInput" name="" value=""/>
<button class="butt1" onclick="EventClick();">정답제출</button>

<script>
	var score = 0; //점수
	var time = 30; //남은시간
	var isContinue = true;
	var finish; //남은시간 체크하는 setInterval
	var num1;
	var num2;

	// 게임시작
	function Start(){
		$("#score").text(score);
		$("#time").text(time);
		GetQuestion();
		finish = setInterval(StartTimeCheck, 1000);
		$("#btnStart").hide();
	}

	// 시간줄이기
	function StartTimeCheck(){
		if(time > 0){
			time = time-1; 
			$("#time").text(time);
		}else{
			clearInterval(finish);
			isContinue = false;
			if(confirm("시간이 종료되었습니다.\n새 게임을 시작하시겠습니까?")){
				score = 0;
				time = 30;
				isContinue = true;
				Start();
			}else{
				$("#btnStart").show();
				$("#computer").text("게임종료");
			}
		}
	}

	// 구구단 결과 입력시
	function EventClick(){
		if (!isContinue) {alert("시간이 종료되었습니다."); return;}
		var userVal = $("#txtUserInput").val();
		if((num1 * num2) == userVal){
			alert("맞췄습니다.");
			score = score+1;
			$("#score").text(score);
			GetQuestion();
		}
		else{
			alert("틀렸습니다.");
		}
	}

	function GetQuestion(){
		if(isContinue){
			num1 = Math.round(Math.random()*8)+1;
			num2 = Math.round(Math.random()*8)+1;
			$("#computer").text(num1+" * "+num2+"=");
			$("#txtUserInput").val("");
			$("#txtUserInput").focus();
		}
	}

	// 엔터키 누르면 결과 반영
	$("#txtUserInput").bind('keyup', function(e){
		e.preventDefault();
		if(e.key=="Enter"){
			EventClick();
		}
	});
</script>
</body>
</html>

```
