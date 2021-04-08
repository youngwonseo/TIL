# 디자인패턴#1 - 모듈패턴, 이벤트 델리게이션 패턴

디자인 패턴은 소프트웨어를 개발할 때 사용가능한 패턴 및 설계방식을 정의한 것입니다. 개발자라면 GoF의 디자인 패턴을 따로 공부하는 것을 추천해 드립니다. 여기서는 디자인패턴의 개념에 대해서는 간략하게 설명하고 자바스크립트에서, 즉 웹브라우저에서 해당 디자인패턴이 어떤식으로 사용되고 적용되는가에 초점이 맞추어 살펴봅니다.

## 모듈패턴(module pattern)
모듈 패턴은 말 그대로 자바스크립트를 모듈 단위로 만들어 라이브러리로 개발 및 배포 가능하게 만들어주는 패턴입니다. 특히 웹 브라우저에서 사용자와의 인터렉션을 위해 여러 자바스크립트 라이브러리(예로 JQuery)를 추가해서 개발을 진행하는데 해당 라이브러리에 모듈패턴이 적용되어 있다고 볼 수 있습니다. 

먼저 모듈패턴이 사용된 라이브러리(Jquery)를 사용하는 예를 살펴보겠습니다.
```javascript
$.get('/api/users/jazz9008@gmail.com').done(function(result){
	console.log(result);
});
```
위 예처럼 Jquery를 사용할 때(실행 로직은 관심사가 아님) 저희는 $ 라는 전역변수에 접근해 Jquery에서 제공하는 함수를 사용합니다(자바스크립트에서는 $로 변수명을 사용가능).  이처럼 하나의 전역변수을 이용하여 여러 함수 및 변수를 접근하는 개념이 모듈 패턴의 기본개념입니다. 이렇게 사용할 시 다음과 같은 장점들이 존재합니다.
* 파일단위로 관리 가능
* 네임스페이스로 사용가능
* 모듈 단위 개발로 모듈간 의존성 최소화, 그리고 의존성을 파악하기 용의
* 다른 개발자들이 사용가능한 형태의 라이브러리(API)로 배포 용의


### 모듈 패턴의 기본
```javascript
(function(window){
	var myLibrary = {
		helloWorld: function () {
			console.log("Hello World!");
		},
		hello: {
			world: function () {
				console.log("Hello Module!!");
			}
		}
	}
	window.myLibrary = myLibrary; //전역스코프에 myLibrary라는 전역변수로 할당
}(window));
```
먼저 즉시 실행함수를 실행하는데 window를 넘겨 마지막 라인에서 window에 myLibrary라는 이름으로 전역변수를 할당합니다. 이때 해당 전역변수의 내용이 즉시실행함수 안에서 구성됩니다. 위 라이브러리를 파일로 만들어 추가하면 다음과 같이 사용가능합니다.
```javascript
myLibrary.helloWorld(); //Hello World!
myLibrary.hello.world(); //Hello Module!!
```
두 번째 호출된 함수를 보면 최상위 모듈명을  myLibrary로 하고 하위 모듈로 hello가 지정되어 있습니다. 자바스크립트에서는 객체의 사용이 다른언어에 비해 자유로운점이 있기 때문에 위처럼 사용을 해서 네임스페이스를 세분화 되게 구분할 수 있습니다. 즉 함수들을 용도별로 객체화해서 배포함으로써 가독성이 높은 라이브러리를 개발할 수 있습니다.

다음으로 모듈패턴을 사용하는 또다른 방법들이 존재합니다 (설명은 생략합니다).
```javascript
//다른방법 1
var myLibrary = (function(window){
	var myLibrary = {
	};
	return myLibrary;
});

//다른방법 2
var myLibrary = (function(window){
	return {
	};
});
```
모듈패턴은 아주 다양한 형태로 사용되고있습니다. 실제 자바스크립트 라이브러리는 위방식 보다 더 복잡하게 사용됩니다.  특히 여러 모듈 시스템(AMD, CommonJS, UMD등, [본 글의 마지막 reference의 두 번째 링크 참조](https://www.zerocho.com/category/JavaScript/post/5b67e7847bbbd3001b43fd73))에 공통으로 라이브러리를 사용가능하게 하기 위해 폴리필처럼 적용된 부분들이 많이 존재합니다. **이러한 모듈패턴을 잘 이해하고 사용하기 위해서는 여러 자바스크립트 라이브러리를 다운받아서 소스를 분석하는 것입니다.** 보다 자바스크립트 라이브러리를 잘 이해하고 개발하기 위해서는 꼭 여러 라이브러리를 분석하며 공부해야 합니다.

### 네임스페이스 생성과 JQuery플러그인
이번에는 기존 모듈, 즉 기존 라이브러리를 확장해서 사용하는 방식을 살펴봅시다. 
```javascript
(function(windows, undefined){
	var _myLibrary = window.myLibrary;
	if(!_myLibrary){
		_myLibrary = {};
	}
	if(!_myLibrary.unikys){
		_myLibrary.unikys = {};
	}
	_myLibrary.unikys.sayHello = function () {
		console.log("Hello!");
	};
	window.myLibrary = _myLibrary;
}(window));
```
위 예제를 처음부터 순차적으로 보면 네임스페이스를 확장하듯 동작하며 sayHello 라는 함수를 생성합니다. 이 또한 위에서 설명한 것과 같이 자바스크립트의 객체 사용이 자유로운점을 활용한 것입니다. 확장 후 다시 전역변수인 myLibrary에 수정된 모듈을 할당하며 새로운 네임스페이스를 가진 myLibrary를 만드는 것이죠. 물론 확장이 아닌 초기 로딩에서도 위코드는 동작가능합니다. Jquery를 사용할 때 함께 사용가능한 Jquery플러그인도 기존의 Jquery를 위와 같은 방법으로 확장해서 제공하는 것들입니다.


## 이벤트 델리게이션 패턴(event delegation pattern)

### 이벤트 버블링과 캡처링
![](https://javascript.info/article/bubbling-and-capturing/eventflow.png)

### 이벤트 델리케이션 패턴 사용 예





## References
* https://javascript.info/bubbling-and-capturing
* https://www.zerocho.com/category/JavaScript/post/5b67e7847bbbd3001b43fd73
