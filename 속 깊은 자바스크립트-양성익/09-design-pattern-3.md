# 디자인패턴#3 - Init-time branching 패턴, Self-defining function 패턴

## Init-time branching 패턴
Init-time branching 패턴은 자바스크립트에서 매우 유용한 패턴 중 하나입니다. 이름 그대로 초기화 단계에서 상황에 맞게 분기하여 실행하는 것을 의미합니다. 예로 들면 브라우저 종류나 버전에 따라 다른 함수, 즉 브라우저별로 실행 가능한 함수를 호출하는 것이죠. 
브라우저 버전에 따라 분기를 해야할때 IE8을 가장 많이 고려할 것입니다. 다음은 IE8과 다른 브라우저의 XMLHttpRequest 호완성에 대해 일반적인 버전과 Init-time branching 패턴을 적용한 예제입니다.

### 일반적인 XMLHttpRequest 사용
```javascript
function getXHR() {
  if(window.XMLHttpRequest) {
    return new XMLHttpRequest();
  }
  try {
    return new ActiveXObject("MSXML2.XMLHTTP.6.0");
  } catch (e) {
    try {
      return new ActiveXObject("MSXML2.XMLHTTP.3.0");
    } catch (e) {
      alert("This browser does not support XMLHttpRequest.");
      return null;
    }
  }
}
```
예전에는 navigator 객체를 사용해서 브라우저 버전을 확인했습니다. 즉 addEventListener를 지원하지 않는 IE8같은 경우 attachEvent를 호출하도록 navigator객체의 속성으로 존재하는 브라우저버전을 확인해서 IE8과 아닐때를 구분해서 사용했습니다. 그러나 브라우저마다 웹 표준에서 지원해주는 기능이 제각각이어서 **위 예제와 같이 브라우저정보 보다는 사용할 함수나 기능 자체가 정의되어 있는지 검사에서 사용하는 것이 추천**됩니다. 

위 예제는 지원되는 함수나 기능을 예외를 통해 핸들링하여 브라우저에 맞게 호출되록 하고 있으나 getXHR을 사용하면 매번 try, catch를 타며 예외를 핸들링해야 합니다. 이 부분을 Init-time braching 패턴을 사용하여 변경한것이 아래의 예제입니다.


### Init-time branching 패턴을 적용한 XMLHttpRequest 사용
Init-time-branching 패턴을 적용하면 즉시호출함수를 통해 어떤객체를 사용해서 XMLHttpRequest를 사용할지 분기가 정해집니다.
```javascript
var getXHR = (function() {
  var xhr;
  if(window.XMLHttpRequest) {
    return function () {
      return new XMLHttpRequest();
    }
  }
  try{
    xhr = new ActiveXObject("MSXML2.XMLHTTP.6.0");
    return function () {
      return new ActiveXObject("MSXML2.XMLHTTP.6.0");
    }
  }catch (e) {
    try{
      xhr = new ActiveXObject("MSXML2.XMLHTTP.3.0");
      return function () {
        return new ActiveXObject("MSXML2.XMLHTTP.3.0");
      }
    }catch (e) {
      alert("This browser does not support XMLHttpRequest.");
    }
  }
}());
```
Init-time branching패턴은 위와 같이 초기화 단계에 현재 브라우저의 버전이나 환경에 따라 적합한 실행루틴을 정해놓는것입니다. 결국 초기화 시점에 위 함수들이 실행되어야 하므로 추가적인 컴퓨팅 자원이 실행되는것입니다. 즉 Init-tmie branching 패턴을 사용해서 초기화 시점에 분기루틴을 정할지 함수가 처음 호출될때 분기루틴을 정할(Self-defining function패턴)지 등의 실행시점에 대한것은 상황에 따라 적절한 판단이 필요합니다.


## Self-defining function 패턴
Self-defining function 패턴은 자바스크립트의 변수에 함수를 쉽게 재정의하거나 덮어쓸수 있는 장점에서 나온 디자인 패턴입니다. 이름 그대로 자기 자신을 다시 정의하는 것인데 위의 Init-time branching 패턴은 초기화 시점에 실행루틴이정해진다면 Self-defining function 패턴은 처음 호출될때 실행될 루틴에 맞는 함수로 다시 정의되는 것입니다. 다음은 getXHR에 대한 Self-defining function 패턴입니다.


```javascript
var getXHR = function () {
  var xhr;
  if(window.XMLHttpRequest) {
    getXHR = function () {
      return new XMLHttpRequest();
    };
    return new XMLHttpRequest();
  }
  try{
    xhr = new ActiveXObject("MSXML2.XMLHTTP.6.0");
    getXHR = function () {
      return new ActiveXObject("MSXML2.XMLHTTP.6.0");
    };
    return xhr;
  }catch (e) {
    try{
      xhr = new ActiveXObject("MSXML2.XMLHTTP.3.0");
      getXHR = function () {
        return new ActiveXObject("MSXML2.XMLHTTP.3.0");
      }
      return xhr;
    }catch (e) {
      alert("This browser does not support XMLHttpRequest.");
    }
  }
}
```
앞의 Init-time branching 패턴과 매우 유사하지만 다른점은 함수가 결정되는 시점입니다. Self-defining function 패턴은 초기에 컴퓨팅 리소스를 필요로 하지 않고 처음 실행되는 시점에 소모합니다. 


## References
* [속깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)
