# 디자인패턴#4 - 메모이제이션 패턴, Self-invoking constructor 패턴, 콜백 패턴, 커링 패턴

## 메모이제이션 패턴
메모이제이션은 말 그대로 기억을 하는것, 메모를 하는 패턴입니다. 자바스크립트 특성상 새로운 속성을 추가하고 수정할 수 있다는 점에서 이 패턴을 간단하게 활용할 수 있습니다. 

다음은 메모이제이션을 이용한 캐시에 대한 예제입니다.
```html
<html>
  <body>
    <input type="text" id="itemId">
    <button id="search">Search</button>
    <script>
      (function(){
        var inputItemId = document.getElementById("itemId");
        function searchItem(id) {
          var xhr;
          //캐싱되어 있는 값 반환
          if(searchItem.cache.hasOwnProperty(id)) {
            return searchItem.cache[id];
          }

          xhr = new XMLHttpReqeust();
          xhr.open("GET", "/searchItem");
          xhr.onload = function() {
            var item = JSON.parse(xhr.responseText);
            //캐싱
            searchItem.cache[item.id] = item;
          }
          xhr.send();
        }
        searchItem.cache = {};
        
        document.getElementById("search").addEventListener("click", function(){
          searchItem(inputItemId.value);
        })
      }());
    </script>
  </body>
</html>
```
코드를 보면 직관적으로 캐싱이 사용되는 것을 알 수 있습니다. 


## Self-invoking constructor 패턴

자바스크립트는 객체지향적 프로그래밍을 위해 function으로 생성자를 정의하고 new로 객체의 인스턴스를 만들곤 합니다. **이때 생성자 자체도 결국 function이므로 new를 생략해도 오류가 발생하지않고 오작동만 하게되는 경우가 있습니다..** 특히 다른사람이 작성한 코드를 사용할때 의도가 정확하기 보이지 않는 코드를 사용할 때 이러한 상황이 발생할 수 있습니다. Self-invoking constructor패턴 이러한 실수를 고려하여 new를 사용하지 않고 생성자를 호출했을 때 new를 사용하여 생성자를 다시 호출하는 패턴입니다.

```javascript
function Employee(name, manMonth) {
  if(!(this instanceof Employee)) {
    return new Employee(name, manMonth);
  }

  this.name = name;
  this.manMonth = manMonth;
}

var unikys = Employee("Unikys", 1),
    world = new Employee("World", 2);

console.log(unikys);
console.log(world);
```
instanceof를 사용하여 Employee를 체크하는데 new를 사용하지 않으면 this는 글로벌 컨텍스트인 window객체이기 때문에 false를 반환합니다. ECMAScript6에서는 class 키워드가 지원되므로 function으로 객체를 정의할 상황은 점점줄어드나 기존버전과의 호환성 등을 고려하여 function으로 객체와 생성자를 정의할 경우도 있기때문에, 이경우 self-invoking constuctor패턴을 통해 좀더 방어적이고 안전한 코딩을 하는것을 추천합니다.



## 콜백 패턴
자바스크립트는 함수기반으로 동작하기 때문에 이벤트를 등록하기 위해 함수를 등록하여 콜백을 사용하는 패턴을 자주 사용합니다. 콜백패턴은 매우 간단하지만 비동기 처리가 많은 자바스크립트에서는 다양하게 응용되어 사용됩니다. 다음은 ajax라는 함수를 정의하고 callback이라는 매개변수를 받아 비동기 통신이 완료되었을때 수행되도록하는 간단한 예제입니다. 또한 콜백안에 콜백이 존재하는데 순서가 보장되어야 할때 이런식으로 사용됩니다.
 
```javascript
function ajax(method, url, data, callback) {
  var xhr = new XMLHttpRequest();
  xhr.open(method, url);
  xhr.onload = function() {
    if(xhr.status === 200){
      callback.call(this, xhr.responseText);
    }
  }
  xhr.send(data);
}

ajax("POST", "/login", "id=hello&password=world", function(responseText) {
  if(responseText === "Success") {
    alert("Success to login!");
    ajax("GET", "/userInfo", "id=hello", displayUserInfo);
  } else {
    alert("Failed to login");
  }
})

function displayUserInfo(responseText){
  document.getElementById("userInfo").innerHTML  = responseText;
}
```
최근에는 [프로미스(Promise)]()라는 개념이 도입되어 여러개의 비동기 함수 호출에 대한 순서를 보장합니다.


## 커링 패턴
커링패턴은 함수를 설계할 때 인자 전체를 넘겨서 호출하는게 아니라 일부만 넘기고 일부는 먼저 입력해두어 사용하는 패턴입니다. 먼저 입력해 둔다는 것은 자바스크립트의 클로저를 사용해서 입렵값을 유지 시켜놓는 것입니다. 


```javascript

function sum(x, y){
  return x + y;
}

var makeAddr = function(x) {
  return function(y) {
    return sum(x,y);
  }
}

var adderFour = makeAddr(4);
console.log(adderFour(1));  // 5
console.log(adderFour(5));  // 9
```
예제를 살펴보면 sum이라는 두 개의 인자를 받는 함수가 존재했는데 makeAddr을 통해 하나의 인자를 받아놓는 sum을 정의한 것을 볼 수 있습니다.
다음은 커링패턴을 prototype에 추가하여 REST API에 대한 ajax를 사용하는 예제입니다.

```javascript
//Function의 prototype에에 커링패턴을 정의
Function.prototype.curry = function() {
  if(arguments.length < 1) {
    return this;
  }
  var _this = this,
      args = Array.prototype.slice.apply(arguments);
  return function() {
    return _this.apply(this, args.concat(Array.prototype.slice.apply(argument)));
  }
}

//콜백패턴에서 사용한 ajax
function ajax(method, url, data, callback) {
  var xhr = new XMLHttpRequest();
  xhr.open(method, url);
  xhr.onload = function() {
    if(xhr.status === 200){
      callback.call(this, xhr.responseText);
    }
  }
  xhr.send(data);
}

var ajaxGet = ajax.curry("GET"),
    ajaxPost = ajax.curry("POST"),
    ajaxPut = ajax.curry("PUT"),
    ajaxDelete = ajax.curry("DELETE");

ajaxGet("/data", null, function(responseText){
  console.log(responseText);
})
```


## References
* [속깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)
