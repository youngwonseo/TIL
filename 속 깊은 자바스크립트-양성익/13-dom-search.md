#  DOM 탐색

자바스크립트에서 특정 DOM을 조작할려고 할 때, 우리는 먼저 DOM을 document.getElementById 등의 셀렉터로 찾습니다. 웹(프론트)을 개발하다보면 정말 수도 없이 사용하는 함수입니다. 그런데 이러한 DOM을 찾는 함수도 트리를 탐색하기 때문에 CPU소모가 발생합니다. 여기서는 트리탐색을 최소화하는 몇 가지 방법을 산단하게 살펴보겠습니다.


다음은 트리탐색이 많은 안티패턴입니다.
```javascript
document.getElementById('chaningDiv').style.backgroundColor = '#EEEEEE';
document.getElementById('chaningDiv').style.fontSize = '10em';
document.getElementById('chaningDiv').style.width = '100px';
document.getElementById('chaningDiv').style.marginLeft = '10px';
```

이는 다음과 같이 사용하게 좋습니다. 한 번의 DOM을 탐색을 유도하는 것이죠. 스타일요소를 사용하므로 스타일까지 변수에 할당하는것입니다.
```javascript
var changingDivStyle = document.getElementById('changingDiv').style;
changingDivStyle.backgroundColor = '#EEEEEE';
changingDivStyle.fontSize = '10em';
changingDivStyle.width = '100px';
changingDivStyle.marginLeft = '10px';
```


## 메모이제이션 활용
DOM을 탐색하는 것은 클로저 내의 변수를 탐색하는 것보다 더 많은 자원을 소모합니다. 따라서 같은 행동(함수)에 대한 DOM 탐색을 메모이제이션패턴을 사용하여 한 번만 탐색하도록 수정할 수 있습니다.
```html
<form id="myForm">
  <input type="text" name="firstName" id="firstName"/>
  <input type="text" name="middleName" id="middleName"/>
  <input type="text" name="lastName" id="lastName"/>
  <input type="submit" value="submit"/>
</form>
<script>
(function(){
  var memo = {};
  document.getElementById('myForm').onsubmit = function () {
    var inputFirstName = getMemo('firstName'),
        inputMiddleName = getMemo('firstName'),
        inputLastName = getMemo('firstName');
    if(inputFirstName.value === "") {
      alert('First name is manatory');
      inputFirstName.focus();
      return false;
    }

    inputLastName = getMemo('lastName');
    if(inputLastName.value === "") {
      alert('Last name id mandatory');
      inputLastName.focus();
      return false;
    }

    inputMiddelName = getMemo('middleName');
    alert('Hello, ' + inputFirstName.value + " " + 
                      inputMiddleName.value + " " + 
                      inputLastName.value);

  }

  function getMemo(id) {
    memo[id] = memo[id] || document.getElementById(id);
    return memo[id];
  }
}());
</script>
```
getMemo라는 함수를 정의하고 내부에서 memo객체를 사용하여 메모이제이션패턴을 구현한 예제입니다. getElementById를 id당 한번만 호출하여 DOM트리 탐색시간을 최적화 하였습니다.

# References
* [속깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)

