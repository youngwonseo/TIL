# 스코프

스코프(scope)는 현재 접근할 수 있는 변수 및 메소드의 범위를 나타냅니다.  많은 프로그래밍 언어에서는 중괄호({})로 스코프를 나타내지만 자바스크립트는 이와 달리 정해진 구분에 의해서 스코프를 결정합니다. 스코프를 이해하기위해 다음의 예제를 먼저 살펴봅시다.
```html
<div id="div0"></div>
<div id="div1"></div>
<div id="div2"></div>
<script>
	var i, len=3;
	for(i=0; i< len; i++){
		document.getElementById("div"+i).addEventListener("click", function(){
			alert("You clicked div #" + i);
		}, false);
	}
</script>
```
위 코드는 각 div에 클릭 이벤트를 추가합니다. 이때 각 div를 클릭하면 div의 번호에 따라 alert를 띄웁니다. 실제 위처럼 코드를 작성하는 자바스크립트 개발자가 있을 수 있지만 위코드는 정상대로 동작되지 않습니다. 각 div를 클릭하면 div에 대한 숫자가 alert되지많고 모두 "You clicked div #3"을 보이기 때문입니다.  이는 스코프의 문제라기 보다 스코프의 생성와 유지에 대한 이해도 부족에서 발생하는 문제입니다. 그럼 스코프에 대해 이해도를 높혀봅시다.

> 위 코드는  i라는 전역변수를 콜백안에서 참조하고 있는데, 이벤트 발생 시점에 전력 i를 계속 참조하기 때문에 이벤트등록이 끝난(for문이 끝나서 i가 3)시점의 i를 참조하기 때문에 모든 이벤트가  3을 출력시킵니다.


## 스코프의 생성
위에서 자바스크립트는 중괄호로 스코프를 결정짓지 않는다고 했습니다. 자바스크립트에서 스코프를 만드는 구문은 다음과 같은 3가지의 syntax입니다.
* function
* with
* catch

### function
```js
function foo() {
	var b = "Can you access me ?";
}
console.log(b);   //undefined
```
잘 아시는 함수입니다.  위 예제처럼 foo안에서 선언된 b는 foo 외부에서는 접근할 수 없습니다. 
>여기서 undefined란 브라우저마다 출력하는 구문이 차이가 날 수 있는데 정의가 되어있지않아 오류가 발생하는것을 나타냅니다.

### catch
catch와 with은 function과는 조금 다르게 동작합니다.  이 두 구문은 인자로 받은 값들은 해당 스코프에 포함시키지만 내부에서 새로 정의된 변수는 외부에서 접근이 가능합니다.
```js
try{
	throw new exception("fake exception");
}catch(err){
	var test = "can you see me ?";
	console.log(err instanceof ReferenceError === true);
}
console.log(test); //"can you see me ?"
console.log(err); //undefined
```
즉 위 예제이서 err는 외부에서 접근이 불가능하지만 test의 경우 접근이 가능합니다.
### with
with도 catch와 비슷하게 동작합니다. 
```js
with({inScope: "You can't see me"}) {
	var test2 = "You cat see me";
}
console.log(inScope); //undefined
console.log(test2); //"You cat see me"
```
with구문을 처음 보시는 분들도 계실것입니다. with구분은 object를 인자로 받는데 모호성이 존재하여 현재는 쓰지 말아야할 구문이며 현재 ECMAScript6에서는 deprecated되었습니다. 다음은 모호성의 예입니다.
```js
function doSomething(value, obj){
	with(obj) {
		console.log(value) //obj.value인가, doSomething의 인자인 value인가?
	}
}
```

## 스코프의 지속성
그렇다면 맨 처음 예제의 문제를 해결하기 위해서는 어떤방법을 써야할까요? 여러가지 방법이 존재하지만 여기서는 함수를 이요항방법과 클로저를 이용하는 방법을 통해 스코프의 지속성유지하여 문제를 해결해 봅시다.

> 클로저는 이 다음 게시을에 상세한 설명이 되어 있습니다.

### 함수를 이용한 문제해결
```html
<div id="div0"></div>
<div id="div1"></div>
<div id="div2"></div>
<script>
	function setDivClick(index){
		document.getElementById("div"+index).addEventListener(
			"click",
			function(){
				alert("You clicked div #" + index);
			}, 
			false
		);
	}
	var i, len=3;
	for(i=0; i< len; i++){
		setDivClick(i);
	}
</script>
```
setDivClick이라는 함수를 정의하여 인자로 i값을 넘겨 이벤트를 등록하였습니다. 이경우 setDivClick이 하나의 스코프를 생성하여 이벤트에서 index를 참조합니다. 이때 참조하는  index는 setDivClick안에서 유지된다고 볼 수 있습니다.


### 클로저를 이용한 문제해결
두 번째 방법은 클로저를 이용한 방법입니다. 클로저는 다음글에서 자세하게 다뤄져 있습니다. 클로저라는 용어를 처음 접한 분도 있을 수 있는데 자바스크립트에서 굉장히 중요한 개념이니 정확하게 이해해 봅시다.
```html
<div id="div0"></div>
<div id="div1"></div>
<div id="div2"></div>
<script>
	var i, len=3;
	for(i = 0; i < len; i++){
		document.getElementById("div"+i).addEventListener(
			"click",
			(function(index){
				retrn function(){
					alert("You clicked div #" + index);
				};
			}(i)),
		false);
	}
</script>
```
콜백에 대해 함수가 이중으로 처리되어있습니다. (function(index){ })(i)로 선언되어있는데 이를 즉시실행함수(IIFE: Immediate Invoke Function Expression)이라고 합니다. 즉시실행함수는 이름처럼 콜백을 등록할때에도 즉시 실행되는데, 위 예에서는 스코프를 생성하고 콜백함수를 반환하여 콜백을 등록합니다. 즉 index를 사용할 스코프를 생성하죠. 생성된 스코프는 다음과 같습니다.
![](/public/js-scope.png)

위는 스코프 체인을 나타낸 것이며 즉시실행함수로 스코프를 만들고 해당 스코프에 존재하는 i를 콜백함수가 참조하고 있습니다. 클로저는 매우 중요한 요소이기 때문에 정확하게 이해하는 것을 강조하며 다음글에서 자세히 다루겠습니다. 


## 결론
스코프에 대해 살펴보았습니다. 함수 또는 클로저를 사용하여 변수를 해당 스코프에서 유지시켰는데 실제 개발시 발생하는 문제를 해결하기 위해서는 한 가지 방법으로만 해결하는 것이 아니라 적절한 해결방법을 사용해야 합니다. 즉 위 내용을 정확하게 이해하고 적절하게 활용합시다. 

## References
* [속깊은 Javascript - 양성익 지음](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)