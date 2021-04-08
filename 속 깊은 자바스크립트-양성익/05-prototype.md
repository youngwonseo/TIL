# 프로토타입

지금의 자바스크립트(ECMAScript6~)는 class키워드를 지원하기 때문에 자바스크립트에서도 얼마든지 객체지향을 프로그래밍을 할 수 있습니다. 하지만 이전의 자바스크립트에서는 객체지향이라기 보다 이벤트와 함수 기반 언어였고 현재도 많은 라이브러리(이전버전부터 만들어진)가 class를 사용하지 않습니다(이전에 개발된 라이브러리들, 또한 브라우저에서의 호환을 위해). 그렇다고 해서 자바스크립트에서 객체지향을 배제한것은 아닙니다. 이전버전의 자바스크립트에서는 객체지향을 위해 class키워드 대신 function과 prototype을 사용했습니다. 일반적인 html페이지 내의 인라인 자바스크립트 사용시에는 prototype을 사용하지 않을때도 있으나 **라이브러리나 재사용 가능한 모듈 개발을 위해서는 필수적으로 사용**되었습니다.
비록 현재 자바스크립트 버전이 class를 지원하나 기존의 라이브러리를 분석하거나 수정, 개발과 함께 자바스크립트의 이해도를 높이기 위해 prototype의 개념과 사용법은 꼭 익혀야 합니다.

> 아래는 전부 class기반이 아닌 function기반의 문법입니다.

## 자바스크립트의 클래스
위에서 자바스크립트가 이벤트와 함수기반 언어라 명시하였습니다. class가 없던 시절부터 자바스크립트는 객체지향을 구현할 수 있는 언어였는데 이때 function을 사용하였습니다. 다음은 간단한 클래스 정의와 사용에 대한 예제 입니다.
```javascript
function Person(name, company){
  this.name = name;
  this.company = company;
}

var youngwon =  new Person("youngwon seo","youngwon.io");
alert(youngwon.name);
```
다른언어들과 비교해보면 class가 아닌 function으로 클래스를 정의하고 해당 클레스 정의가 **생성자**처럼 동작합니다. 

## this의 이해
위의 객체정의에서 클래스(function) 내부에서 this를 사용하고 있는데 다른 프로그래밍언어에 대한 경험 있으면 this가 본인 자신을 가르킨다는 것을 알고 있을 것입니다. 하지만 자바스크립트에서는 호출되는 방법에 따라 this의 의미가 달라지는데 여기서는 **함수에 대한 4가지 호출방법**에 대해  this가 어떻게 달라지는지 살펴봅시다. 이후 prototype과 자바스크립트에서의 객체지향을 제대로 이해하고 사용하기 위해 꼭 알아둡시다.
* 일반 함수로의 호출
* 멤버함수로의 호출
* call() 함수를 이용한 호출
* apply()함수를 이용한 호출

```javascript
function whatsThis(){
  return this.toString();
}

var unikys = {
  what: whatsThis,
  toString: function() {
    return "[object unikys]";
  }
};

whatsThis();					//#1
unikys.what();					//#2
whatsThis.call();				//#3
whatsThis.apply(unikys);		//#4
unikys.what.call(undefined);	//#5
unikys.what.call(unikys);		//#6
var unikysWhat = unikys.what;
unikysWhat();
```
총 여섯가지로 함수를 호출하였습니다. 결과는 다음과 같습니다.
![](/public/prototype-result.png)
이제 하나씩 분석해 봅시다.
#1의 경우 일반적인 함수 호출입니다. this가 window, 즉 전역으로 설정됩니다. 
#2의 경우 unikys안에 종속된 함수로 this가 unikys가 됩니다. 여기까지는 문제될게 없어보입니다.
#3의 경우 call을 사용하여 함수를 호출합니다. 함수의 원형(괄호없이 함수의이름)에 call을 사용하여 함수를 호출하는데 this가 전역객체가 됩니다.
#4의 경우는 apply를 사용하여 함수를 호출하는데 this가 unikys가 됩니다. 
여기서 한가지 집고 넘어가야할 사실은 call이나 apply이나 함수의 원형을 이용하여 호출하는 함수입니다. 근데 이 메서드들은 첫 번째 인자로 binding할 객체를 받습니다. 즉 #3은 매개변수에 아무것도 입력하지 않은경우 이기때문에 this가 전역객체가 되었고 #4는 매개변수로 unikys를 넘겼기 때문에 this가 unikys가 된것입니다.
#5는 undefined를 넘겼기 때문에 전역객체가 this이고 #6는 unikys가 매개면수로 넘어갔기 때문에 this가 unikys가 되는 것입니다. 또한 #5와 #6에서 살펴본것과 같이 어떤 객체안에 포함된 함수라도 call을 통하면(apply도 마찮가지로) 첫 번째 인자가 this로 바인딩됩니다.
#7의 경우 객체안에 포함된 함수가 전역변수에 할당되어 호출되는데 this가 전역객체가 됩니다.
**위에서 살펴본 것과 같이 자바스크립트에서는 this는 함수나 스코프 기반이 아니라 호출되는 방법에 따라 지정됩니다. 꼭 인지하고 사용합시다.**

## 프로토타입 사용하기
프로토타입은 자바스크립트에서 객체지향을 가능하게 해주는 개념이라고 위에서 설명했습니다. 그렇다면 어떻게 프로토타입이 객체지향을 가능하게 해주는지 살펴봅시다.
```javascript
function Person(name, company) {
  this.name = name;
  this.company = company;
}
Person.prototype.getName = function() {
  return this.name;
}
Person.prototype.getCompany = function() {
  return this.company;
}
var youngwon = new Person('youngwon seo','youngwon.io');
var youngsoo = new Person('youngwon soo','youngwon.io');
console.log(youngwon.getName());
console.log(youngsoo.getName());
console.log(youngwon.getCompany ());
console.log(youngsoo.getCompany ());
```
위 예제는 언듯 자바나 c++같은 객체지향언어와 비슷해 보입니다. 클래스를 정의하고 해당 맴버와 맴버함수를 정의하는 것처럼요. 이것이 프로토타입입니다. new키워드를 통해 Person으로 생성자(function)로 생성된 객체들이 prototype을 공유하고 있습니다. 하지만 **자바스크립트에서는 prototype또한 하나의 오브젝트처럼 사용된다는점, 그러므로 언제든지 수정될 수 있다는 점을 기억해야 합니다.** 다음의 예제를 살펴봅시다.
```javascript
//함수 변경
Person.prototype.getName = function() {
  return "My name is " + this.name + ".";
}

//변수 추가
Person.prototype.gender="male";

console.log(youngwon.getName()); //"My name is youngwon seo."
console.log(youngsoo.getName()); //"My name is youngsoo seo."
```
이전 Person의 prototype에 선언한 함수를 위와 같이 수정하면 Person의 생성자로 생성된 모든 객체들이 바뀐형태로 동작하게 됩니다. 하지만 다음과 같은 경우를 살펴봅시다. 
```javascript
youngwon.getName = function(){
	return this.name;
}

console.log(youngwon.getName()); //"youngwon seo."
console.log(youngsoo.getName()); //"My name is youngsoo seo."
```
prototype을 사용하지 않고 youngwon이라는 객체의 getName을 변경하였습니다. prototype을 변경하면 해당 클래스로 생성된 모든 객체들이 변경되었지만 생성된 객체로 변경을 시도하면 해당 객체만 변경됩니다. 이것은 자바스크립트에서 객체를 관리하는 메모리 구조때문인데 다음 그림을 살펴봅시다.
![](/public/js-prototype.png)
생성자 F에서 new 키워드를 이용하여 생성된 객체의 proto는 prototype에 정의된 getName과 getCompany함수를 참조하고 있습니다. 이것은 실제 생성된 Person객체가 해당 함수를 가지고 있는게 아니라 내부 링크(implicit link)로 proto에 접근해서 사용하는 방식을 유지하기때문입니다.
또한 위 그림의 생성자 F를 다음과 같이 접근도 가능합니다.
```javascript
console.log(youngwon.constructor === Person); //true
console.log(youngwon.constructor.prototype === Person.prototype); //true
console.log(youngwon.constructor === youngwon.constructor.prototype.constructor); //true
```

###  객체 내 탐색
객체에서 특정속성을 접근하면 어떻게 동작할까요? 위 설명에 나타난것과 같이 특정 객체는 프로토타입을 참조합니다. 그리고 프로토타입 또한 객체이므로 프로토타입을 참조할 수 있습니다. 즉 객체 자신의 영역에 탐색을하고 없으면 프로토타입에, 또 없으면 프로토타입의 프로토타입을 검색합니다. 이 과정은 프로토타입이 null일때 까지 연쇄적으로 발생하며 이것을 프로토타입 체인(prototype chain)이라고 합니다. 그리고 우리는 여기서 한 가지 중요한 사실을 생각할 수 있는데 그것은 바로 자바스크립트에서 prototype을 이용하여 상속을 나타낼 수 있다는 것입니다!(상속에 대한 자세한 내용은 다음 포트스트에서 다루겠습니다)


## References
* [속깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)