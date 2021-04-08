# 클로저

클로저는 자바스크립트에서 중요한 개념으로 꼭 이해하고 넘어가야할 요소입니다. 그럼 클로저에 대해 알아봅시다.

## 클로저란?
클로저는 다음과 같이 정의할 수 있습니다.

> 특정 함수가 참조하는 변수들이 선언된 렉시컬 스코프(lexical scope)는 계속 유지되는데, 그 함수와 스코프를 묶어서 클러저라고 한다.

즉 특정 함수에서 사용하는 변수들이 함수 내부 스코프에 포함되어 있지 않더라도(참조하는 변수의 스코프가 종료되어도) 스코프를 생성하여 유지시킨다는 말입니다. 말로만 들어선 이해가 가지 않을 수 있습니다. 예제코드를 통해 클로저를 이해해 봅시다.

## 클로저의 예
```js
function counter(){
	var count = 0;
	return {
		increase: function () { 
			return ++count;
		},
		decrease: function () {
			return --count;
		}
	};
}
var counter = counter();
console.log(counter.increase());  //1
console.log(counter.increase());  //2
console.log(counter.decrease());  //1

var counter2 = counter();
console.log(counter2.increase()); //1
```
counter함수를 살펴봅시다. 일단 내부에 count변수를 가지고 있습니다. 그리고 다시 하나의 오브젝트를 리턴하는데 **중요한 점은 이 오브젝트에서 count변수를 사용한다는 것입니다(오브젝트의 내용이 함수라는 것도 상기합시다).** 리턴된 오브젝트를 통해 increase함수와 decrease함수를 호출하는데 counter가 리턴을 하여 스코프가 소멸한것처럼 보이지만, 즉 지역변수인 count가 제거(정확하게 말하면 메모리를 다시 반환)된것 보이지만 실제 반환된 counter를 가지고 increase와 decrease를 실행해도 count를 가진것 처럼 동작됩니다. 이것이 클로저의 핵심입니다. 마치 count변수를 private변수처럼 사용하고 있죠. 또한 counter2를 만들어 사용하면 별도의 count를 가진것(실제로 별도의 스코프를 가지고 있음) 처럼 동작합니다. 
다음은 위 예제의 스코프 체인 구조입니다.
![](/public/js-closure.png)


## 클로저 실제 사용예제
간단한 예제를 통해 클로저를 살펴봤는데 그래도 어디서 사용되는지 감을 잡기 어려울 수 있습니다. 이 의문에 직관적인 대답은 클로저를 가장 많이 사용하는 것은 자바스크립트 라이브러리 모듈에서 private로 변수를 보호하고 싶을때와 static변수를 사용하고 싶을때 사용한다는 것입니다. 또한 콜백함수 인자를 넘겨 넘긴 시점의 값을 유지하여 사용하고 싶을때 입니다. 
다음 예제를 보며 위 말이 무슨 뜻인지 살펴보겠습니다.  이 예제는 각 버튼을 클릭했을때 실행되는 작업이 다르도록 콜백을 설정하는 것입니다.
```html
<div id="wrapper">
	<button data-cb="1">Add div</button>
	<button data-cb="2">Add img</button>
	<button data-cb="delete">Clear</button>
	Adding below...<br/>
	<div id="appendDiv"></div>
</div>
<script>
(function(){
	var appendDiv = document.getElementById("appendDiv");
	document.getElementById("wrapper").addEventListener("click", append);

	function append(e) {
		var target = e.target || e.srcElement || event.srcElement;
		var callbackFunction = callback[target.getAttribute("data-cb")];
		appendDiv.appendChild(callbackFunction());
	};
	var callback = {
		"1":(function () {
			var div = document.createElement("div");
			div.h=innerHTML = "Adding new div";
			return function() {
				return div.cloneNode(true);
			}
		}()),
		"2":(function () {
			var img = document.createElement("img");
			img.src = "";
			return function() {
				return img.cloneNode(true);
			}
		}()),
		"delete": function(){
			appendDiv.innerHTML = "";
			return document.createTextNode("Cleared");
		}
	};
}());
</script>
```

이 예제에서 클로저를 활용하는 부분은 크게 두 부분입니다. 첫 번째는 appendDiv를 가져 오는 부분입니다. 이렇게 **한 번 초기화**되고 append 함수안에서 호출되고 있습니다. 두 번째는 callback 오브젝트의 각 요소들 입니다.  즉시 실행함수로 결과 함수를 리턴하는데 리턴되는 함수안에서 즉시실행함수의 div, img를 참조합니다. 마치 private처럼 숨기고 있고 첫 번째와 비슷하게 한번 초기화하고 계속 반복적으 접근하는 형식으로 사용되고 있습니다. 
이렇게 최초에 초기화된 고정적인 값이나 변수를 자주 이용하는 경우, 클로저를 통해 최초에 한번 초기화하고 콜백에서 반복적으로 참조하는 것이 퍼포먼스상 유리하게 작용할 수 있고, 객체의 수정이 쉽고 자유로운 자바스크립트에서는 좀 더 안정적인 사용을 유도하는 코드 스타일을 만들어내기 때문에 라이브러리를 개발할 때 자주 쓰이는 것입니다(물론 그 외의 장점도 있습니다). 

## 클로저 단점
그렇다면 클로저는 무조건 장점만 가지고 있을까요? 그건아닙니다. 클로저는 다음과 같은 단점을 가지고 있습니다.

* 클로저는 메모리를 소모한다.
* 스코프 생성과 이후 변수 조회에 따른 퍼포먼스 손해가 있다.

두 구문 모두 스코프와 관련되어 있을 수 있는데요, 소멸해야할 스코프들의 변수들이 메모리에 로드되어 있어야하기 때문에 메모리를 소모할 수 밖에 없습니다. 또한 변수를 참조할때 현재 스코프만 아닌, 변수가 없는경우 상위 스코프들을 탐색해서 변수의 존재여부를 판단하며 동작되기 때문입니다.

## 결론
클로저는 자바스크립트에서 굉장히 중요한 요소입니다. 익숙해지지 않으면 사용하기 어렵지만 자바스크립트 개발자라면 정확하게 이해하고 사용가능해야합니다. 이를 위해서는 단점 또한 정확하게 파악하고 있어야 하며 여러 자바스크립트 라이브러리들을 분석하며 클로저가 어떻게 사용되고 있는지 분석 할 필요가 있습니다.

## References
* [속깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)
