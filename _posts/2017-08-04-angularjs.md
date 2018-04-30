---
layout: post
---

# angularJS 공부

#### input박스에 쓰는데로 나타나게..(ng-model)

```
<!DOCTYPE html>
<html ng-app>
<head>
  <title></title>
  <script src="angular-1.6.5/angular.js"></script>
</head>
<body>
  <input ng-model="text">
  <h1>{{text}}</h1>
</body>
</html>
```
결과   ![alt text](https://zzingyuna.github.io/image/angular_study1.JPG)  



#### 루프로 리스트 뿌리기..(ng-repeat)

```
<!DOCTYPE html>
<html>
<head>
<title></title>
<script src="angular-1.6.5/angular.min.js"></script>
</head>
<body>
	<script>
	var app = angular.module("myShoppingList", []); 
	app.controller("myCtrl", function($scope) {
		$scope.products = ["Milk", "Bread", "Cheese", "Orange"];
	});
	</script>

	<div ng-app="myShoppingList" ng-controller="myCtrl">
		<ul>
			<li ng-repeat="x in products">{{x}}</li>
		</ul>
	</div>

</body>
</html>
```
결과   ![alt text](https://zzingyuna.github.io/image/angular_study2.JPG)  



#### input박스에 입력한 내용이 포함된 내용 뿌리기..(ng-repeat)

```
<!DOCTYPE html>
<html>
<head>
<title></title>
	<script src="angular-1.6.5/angular.js"></script>
	<script>
	var app = angular.module("study3", []); 
	app.controller("PhoneListController", function($scope) {
		$scope.phones=[
			{
				"name":"Nexus S",
				"snippet":"Fast just got faster with Nexus S."
			},
			{
				"name":"Motorola XOOM with Wi-fi",
				"snippet":"The Next, Next Generation tablet."
			},
			{
				"name":"MOTOROLA XOOM",
				"snippet":"The Next, Next Generation tablet."
			}
		];
		$scope.title = "Search Model..";
	});
	</script>
</head>
<body ng-app="study3" ng-controller="PhoneListController">

	<h1>{{title}}</h1>

	<input type="text" ng-model="searchKeyword"/>

	<ul>
		<li ng-repeat="phone in phones | filter:searchKeyword">
			이름 : {{phone.name}}<br/>
			설명 : {{phone.snippet}}
		</li>
	</ul>

</body>
</html>
```
결과   ![alt text](https://zzingyuna.github.io/image/angular_study3_1.JPG)  
검색어를 입력하면..   ![alt text](https://zzingyuna.github.io/image/angular_study3_2.JPG)  

