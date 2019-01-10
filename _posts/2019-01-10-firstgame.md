---
layout: post
---

# 두더지 게임

자바스크립트로 만든 두더지 게임

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
  background-color: lightblue;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: verdana;
  font-size: 20px;
}

button.on{
background-image: url('https://data.ac-illust.com/data/thumbnails/38/3801adeee015000abdea7faec50e9446_t.jpeg');
background-size: 120px;
width:100px;
height:100px;
}
button.off{
background-color: blue;
width:100px;
height:100px;
}
</style>
    <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
</head>
<body>

<h1>두더지 잡기!!</h1>
<p>남은 시간 : <span id="time"></span></p>
<p>점수 : <span id="score"></span></p>

<button onclick="Start()">Start</button>
<br />
<br />
<button onclick="myFunction(this)" id="btn1" name="monster" class="off"></button>
<button onclick="myFunction(this)" id="btn2" name="monster" class="off"></button>
<button onclick="myFunction(this)" id="btn3" name="monster" class="off"></button><br/>
<button onclick="myFunction(this)" id="btn4" name="monster" class="off"></button>
<button onclick="myFunction(this)" id="btn5" name="monster" class="off"></button>
<button onclick="myFunction(this)" id="btn6" name="monster" class="off"></button><br/>
<button onclick="myFunction(this)" id="btn7" name="monster" class="off"></button>
<button onclick="myFunction(this)" id="btn8" name="monster" class="off"></button>
<button onclick="myFunction(this)" id="btn9" name="monster" class="off"></button>

<script>
    var score = 0;
    var game;
    var time = 30;
    var finish;

    function myFunction(item) {
        //var x = document.getElementById("demo");
        //x.style.fontSize = "25px"; 
        //x.style.color = "red"; 
    
        //alert(item.className);
        console.log(item.className);
        if(item.className=="on"){
            score = parseInt($("#score").text());
            $("#score").text(score+1);
        }
    }

    function ChangeColor(){
        var num = Math.round(Math.random()*10);
        if(num > 9 || num < 1){ num = 1;}
        $("button.on").addClass("off");
        $("button.on").removeClass("on");
        $("#btn"+num).removeClass("off");   
        $("#btn"+num).addClass("on");        
    }

    function Start(){
        game = setInterval(function() { ChangeColor(); }, 500);
        time = 30;
        finish = setInterval(function() { 
                    if(time < 1){ 
                        clearInterval(game); 
                        clearInterval(finish); 
                        $("button[name=monster]").removeClass("on");
                        $("button[name=monster]").removeClass("off");
                        $("button[name=monster]").addClass("off");
                        alert("축하축하~!!\n"+$("#score").text()+"점 획득하셨습니다!");
                    } 
                    $("#time").text(time);
                    time--;
                }, 1000);
    }
    
    $(document).ready(function(){   
        $("#score").text(score);  
        $("#time").text(time);
    });
</script>
</body>
</html>

```
