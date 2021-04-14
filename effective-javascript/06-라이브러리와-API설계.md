# 라이브러리와 API설계

## Item53. 일관된 컨벤션을 유지하라

API사용자에게 **일괄되고 일반화된** 컨벤션을 제공하여 혼란을 없애고 학습시간을 최소화 하도록 하여야 한다. 예로들면 넓이와 높이를 지정하는 생성자에서는 아래처럼 넓이가 먼저나오는게 일반적이다.

```javascript
new Widget(320, 240)   //width : 320, height : 240
```

좋은 라이브러리는 문서도 빈틈없이 제공하지만, 최고의 라이브러리는 문서를 자전거의 보조 바퀴처럼 대한다는 사실을 기억하자. 즉, 함수의 이름만 보고 기능이 유추가능해야하며 위처럼 일반화된 컨벤션을 따르도록 라이브러리를 설계해야한다.



## Item54. undefined를 '값이 없는'것처럼 처리하라

자바스크립트에서는 값이 없음을 의미하는 undefined가 존재한다. 다음과 같은 상황들을 살펴보자.

```javascript
var x;
x; //undefined

var obj ={};
obj.x; //undefined

function f(){
    return;
}
function g(){}
f(); //undefined
g(); //undefined
```

변수가 초기화가 되지 않거나 정의되지 않은 프로퍼티접근, 파라미터없이 return 등에서 나타내야하는 '값이 없는' 상황을 자바스크립트에서는 undefined로 처리한다.

undefined는 값이 없는것을 나타내기 위한 자바스크립트 자체 컨벤션이다. 





## Item55. 키워드 인자를 위해 옵션 객체를 받아들여라

Item53에서 제안한 것처럼 인자순서에 대한 일관된 컨벤션을 제공해야한다. 







## Item56. 불필요한 상태 유지를 피하라

API는 상태유지(stateful) 또는 무상태(stateless)로 나뉜다. 무상태 API의 대표적인 예는 RESTful API로써 REST는 서버의 세션 및 쿠키등의 상태유지를 지향하지 않고 각각의 API호출이 독립적으로 이루어진다. 

자바스크립트에서 대표적인 상태유지와 무상태 API는 String과 Date객체이다. String의 경우 "foo".toUpperCase()와 같이 내부 상태에 관계없이 항상 "FOO"를 리턴한다(문자열 값 자체가 상태라고 할 수 있으나 스트링은 Constant객체임을 명시하자). 반면 Date객체는 상태에 따라 toString의 결과값이 다르다. 상태가 꼭필요할 때도 있지만 무상태 API가 더 익히기 쉽고, 사용하기 쉬우며 오류를 만들 가능성이 더 낮다.

여기서는 무상태 라이브러리 중 하나인 Canvas라이브러리의 코드를 살펴보며 무상태의 장점을 살펴보자

```javascript
//상태유지
c.fillStyle = "bule";
c.font = "24pt serif";
c.textAlign = "center";
c.fillText("hello, world!", 75, 25);

//무상태
c.fillText("hello, world!",75,25,{
   	fillStyle = "bule",
	font = "24pt serif",
	textAlign = "center"
});
```

위는 상태유지버전와 무상태버전이다. **당연히 무상태버전이 더 선호되는데 그 이유는 다음과 같다.**

첫 번째, **깨지리 염려가 훨씬 적다.**

생각해 보자. 그리기 동작을 위해 내부 상태를 변경하는 것을 사용자가 파악하고 있어야하고, 디폴트값을 사용자 지정값으로 변경할 시 디폴트값을 사용하기 위해서는 다시 디폴트값으로 지정해야한다. 즉 디폴트값에 대한 정확한 명세를 사용자가 알아야한고 이러한 부분은 사용자의 실수가 더 많이 노출될 환경을 만든다. 이것은 가독성에도 영향을 미친다(위 코드를 살펴보면 알수 있다).



두 번째, **배우기 쉽다.**

첫 번째에서 명시한것과 같이 디폴트 값을 모두 알고있어야 정확한 사용이 가능하다.  이를 위해 불필요한 문서내용이 들어가고 사용자는 그것들을 익혀야 한다. 무상태에서는 전혀 고민하지 않아야 될 많은 부분들을 상태유지에서는 신경쓰게 만든다.



세 번째, **간결하다.**

위 예제 코드와 같이 상태유지 API는 상태변경에 대한 코드를 급격하게 늘려야 하는 경우가 발생할 수 있다. 



## Item57. 유연한 인터페이스를 위해 구조화된 형식을 사용하라

위키를 구성하는 자바스크립트 라이브러리를 상상해보자.  

유연한 사용성을 제공하는 위키는 다양한 포맷으로 문서를 작성할 수 있도록 지원한다. 이를 위해 API는 사용자가 생성한 마크업 텍스트를 잘 관리하고 사용자가 작성한 텍스트와 위키의 기능, 예로들면 HTML로의 변환등에서 필요한 프로퍼티가 분리되어 유연하게 동작해야한다. 위키에서 컨텐트 추출을 위해 어떤 종류의 인터페이스가 존재하는지 조금 더 자세히 살펴보자.

필요한 기능을 몇가지 나열해보자.

메타데이터를 추출할 수 있어야하고 HTML로 포맷팅해서 보여주는 기능 필요한다. getTitle, getAuthor등의 메서드와 toHTML와 같은 메서드를 제공해야 한다는 말이다. 

또한, 마크업 포맷에 대해 설정이 가능해야 한다. 마크업언어는 여러가지 포맷이 존재하는데 예로들어 미디어위키의 포맷을 지원하는 객체를 생성할 때는 new Wiki(Wiki.formats.MEDIAWIKI)와 같이 포맷을 입력받아 지원하도록 한다. 







```javascript
Wiki.prototype.displayPage = function(source){
    var page = this.format(source);
    var title = page.getTitle();
    var author = page.getAuthor();
    var output = page.toHTML();
    //...
}
    
function MWPage(source){
	Page.call(this, source);
}

MWPage.prototype = Object.create(Page.prototype);
MWPage.prototype.getTitle = ...
MWPage.prototype.getAuth = ...
...
Wiki.formats.MEDIAWIKI = function(source){
    return new MWPage(source);
}
```







## Item58. 배열과 유사 배열 객체를 구별하라







## Item59. 과도한 강제 형변환을 피하라







## Item60. 메서드 체이닝을 지원하라

Item56에서 설명한 무상태 API의 강력함 중 하나는 체이닝과 같은 다음과 같은 유연성이다. 

다음은 메서드 체이닝사용된 문자열의 replace함수이다. 

```javascript
//체이닝 스타일
function escapeBasicHTML(str){
    return str.replace(/&/g, "&amp;")
    		.replace(/</g,"&lt;")
    		.replace(/>/g,"&gt;")
    		.replace(/"/g,"&quot;")
    		.replace(/'/g,"&apos;");
}

//반복된 메서드 스타일
function escapeBasicHTML(str1){
    var str2 = str1.replace(/&/g, "&amp;");
    var str3 = str2.replace(/</g,"&lt;");
    var str4 = str3.replace(/>/g,"&gt;");
    var str5 = str4.replace(/"/g,"&quot;");
    var str6 = str5.replace(/'/g,"&apos;");
    return str6;
}
```

두 방법을 살펴보면 체이닝방법이 훨신 간결하다(가독성이 좋다). 


