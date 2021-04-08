# 디자인패턴#2 - 프락시 패턴, 데커레이터 패턴

## 프락시 패턴(proxy pattern)
### 프락시 기본
프락시 패턴은 말 그대로 하나의 객체가 프락시 역활을 수행하여 상황에 따라 수행해야할 함수를 선택하여 호출해주는 패턴을 의미합니다. 다음의 예제를 살펴봅시다.
```js
<html>
<body>
<video  style="width:480px" id="videoBunny" src="http://media.w3.org/2010/05/bunny/movie.ogv"></video>
<div id="controlPanel">
	<button id="play">Play</button>
	<button id="pause">Pause</button>
	<button id="volumeUp">Volume++</button>
	<button id="volumeDown">Volume--</button>
</div>
<script>
(function(){
var divControlPanel = document.getElementById("controlPanel"),
	videoBunny = document.getElementById("videoBunny"),
	proxyClickEventHandler = {
		"play": function() {
			videoBunny.play();
		},
		"pause": function() {
			videoBunny.pause();
		},
		"volumeUp": function() {
			if(videoBunny.volume <= 0.9) {
				videoBunny.volume += 0.1;
			}else{
				video.volume = 1
			}
		},
		"volumeDown": function() {
			if(videoBunny.volume >= 0.1) {
				videoBunny.volume -= 0.1;
			}else{
				videoBunny.volume = 0;
			}
		}
	};
	divControlPanel.addEventListener("click", function(e) {
		var  target = e.target || e.srcElement;
		if(proxyClickEventHandler.hasOwnProperty(target.id)) {
			proxyClickEventHandler[target.id].call();
		}
	}, true);
}());
</script>
</body>
</html>
```
controlPanel안에 속한 4가지 버튼에 대한 기능을 하나의 객체(proxyClickEventHandler)에 정의합니다. 4가지에 대한 각각의 버튼에 이벤트를 등록하지 않고 controlPanel에 이벤트를 등록하고 어떤기능이 실행될지를 id를 통해 선택합니다. 이때 id에 해당하는 함수가 proxyClickEventHandler에 존재하는지 hasOwnProperty로 검사 후 존재하면 call을 통해 해당 함수를 호출합니다.

각각의 버튼에 이벤트를 등록하는 것 보다 코드를 작성하거나 유지보수하는 면에서 깔끔해질 수 있음을 알 수 있습니다. **실제 기능이 추가되면 해당 프록시객체에 기능을 추가하고 버튼을 추가해주면 두 개의 추가된 요소에 대해 따로 처리해줄 일이 없음을 알 수 있습니다. 즉 프락시 객체만 확인하면서 기능들을 관리할 수 있습니다.**

### 프락시를 이용한 캐시
프락시 객체를 이용한 한가지 예제를 더 살펴봅시다. 다음은 프락시를 이용하여 DOM탐색을 캐시를 이용하여 최적화한 예제입니다.

```html
<html>
<body>
<video style="width:240px" id="videoBunnySmall" src="http://media.w3.org/2010/05/bunny/movie.ogv"></video>
<video style="width:320px" id="videoBunnyMedium" src="http://media.w3.org/2010/05/bunny/movie.ogv"></video>
<video style="width:480px" id="videoBunnyLarge" src="http://media.w3.org/2010/05/bunny/movie.ogv"></video>
<div id="controlPanel">
	<select id="controlVideo">
		<option>videoBunnySmall</option>
		<option>videoBunnyMedium</option>
		<option>videoBunnyLarge</option>
	</select>
	<button id="play">Play</button>
	<button id="pause">Pause</button>
	<button id="volumeUp">Volume++</button>
	<button id="volumeDown">Volume--</button>
</div>
<script>
(function() {
var divControlPanel = document.getElementById("controlPanel"),
	selectControlVideo = document.getElementById("controlVideo"),
	controlVideo = {
		"play":  function(video) {
			video.play();
		},
		"pause":  function(video) {
			video.pause();
		},
		"volumeUp":  function(video) {
			if(video.volume <= 0.9) {
				video.volume += 0.1;
			}else{
				video.volume = 1
			}
		},
		"volumeDown":  function(video) {
			if(video.volume >= 0.1) {
				video.volume -= 0.1;
			}else{
				video.volume = 0;
			}
		},
		"getVideoById":  function(id) {
			return  document.getElementById(id);
		}
	},
	proxyClickEventHandler = (function() {
		var  cache = {};
		return function(command) {			
			var  video;
			if(controlVideo.hasOwnProperty(command)) {
				if(cache.hasOwnProperty(selectControlVideo.value)){
					video = cache[selectControlVideo.value];
				}else{
					video = controlVideo.getVideoById(selectControlVideo.value);
					cache[selectControlVideo.value] = video;
				}
				controlVideo[command].call(this, video);
			}
		};
	}());
divControlPanel.addEventListener("click", function(e) {
	var  target = e.target || e.srcElement;
	proxyClickEventHandler(target.id);
}, true);
}());
</script>
</body>
</html>
```
처음예제와 비슷하지만 여러개의 비디오가 존재하고 selectbox로 비디오를 선택하는 구조로 변경된것을 알 수 있습니다.  또한 프록시 객체와 프록시에서 호출할 기능에 대한 객체가 분리되어있습니다.
기본적인 프록시 동작방식보다 어떻게 프록시가 캐시기능을 가지는지 살펴봅시다. 이전과 다르게 프록시 객체가 즉시호출함수로 리던된 함수를 참조하고 있고 cache가 클로저라는 것을 알 수 있습니다.(클로저에 대한 개념이 없다면 [클로저](/2018/11/28/02-closure/)를 바주세요). 즉 프록시에 기능 버튼에 대한 id가 입력되면 selectbox로 선택된 비디오에 해당 기능을 수행해야 하는데 참조한 적이 있는 비디오인지 아닌지를 cache객체로 확인하며 참조한 적이 있으면 cache에서 불러 기능을 수행합니다. 

### 프락시를 이용한 래퍼
자바스크립트 특성상 기존 함수 또는 라이브러리를 한 번더 감싸서 원하는 기능을 구형하는 래퍼(wrapper) 형식의 기능을 쉽게 구현하고 제공할 수 있습니다. 이는 기존의 특정 기능에 대한 전처리를 쉽게 적용할 수 있음을 의미합니다. 프락시 패턴을 이용한 래퍼예제를 살펴봅시다.

```html
<html>
<body>
<script>
(function() {
	function wrap(func, wrapper) {
		return function() {
			var args = [func].concat(Array.prototype.slice.call(arguments));
			return wrapper.apply(this, args)
		}
	}
	function existingFunction() {
		console.log("Existing function is called with arguments");
		console.log(arguments);
	}
	var wrapperFunction = wrap(existingFunction, function(func) {
		console.log("Wrapper function is called with arguments");
		console.log(arguments);
		func.apply(this, Array.prototype.slice.call(arguments, 1));
	});

	console.log("1. Calling existing existing function");
	existingFunction("First argument", "Second argument", "Third argument");

	console.log("\n2. Calling wrapped existing function");
	wrapperFunction("First argument", "Second argument", "Third argument");
}());

</script>
</body>
</html>
```
![](/public/js-proxy)
처음 정의된 wrap함수가 wrapper기능을 구현하는 함수입니다. 첫 번째 인자가 기존함수, 두 번째 인자가 전처리할 함수입니다. 수행순서를 보면 1. Calling.. 로그가 출력되고 existingFunction함수가 호출되어 해당 로그와 arguments가 콘솔에 출력됩니다.
그 다음 2. Calling.. 로그가 출력되며 wrapperFunction이 호출됩니다. 여기서부터 실제 전처리가 적용된 함수가 호출됩니다. wrapperFunction은 wrap함수로 전처리가 추가된 함수입니다. 처음 설명과 같이 첫 번째가 기존함수(existingFunction) 두 번째가 전처리함수(function(func){..})로 쉽게 전처리 함수가 추가된것을 확인 할 수 있습니다.
wrap함수의 실행에 대해 살펴보면 실행될 함수와 모든 arguments를 함쳐 두 번째 인자의 wrapper함수를 호출하는데 wrapper함수해서는 합쳐진 arguments의 첫번째 함수를 실행하는 형태입니다.

## 데커레이터 패턴(decorator pattern)
프락시 패턴이 프락시 객체를 두어 어떤 함수를 호출할 시 결정하는 패턴이라면 데커레이터 패턴은 호출 대상이 되는 객체에 추가 기능들을 자유롭게 추가하는 패턴입니다.

```html

<html>
<body>

<script>
function Validator(form){
	this.validatingForm = form;
	form.addEventListener("submit", function(){
		if(!validator.validate(this)){
			event.preventDefault();
			event.returnValue = false;
			return true;
		}
		alert("Success to validate");
		return true;
	});
}


Validator.prototype.ruleSet = {};
Validator.prototype.decorate = function(ruleName, ruleFunction) {
	this.ruleSet[ruleName] = ruleFunction;
}

Validator.prototype.validate = function(form) {
	var validatingForm = form || this.validatingForm,
		inputs = validatingForm.getElementByClassName("validate"),
		length = inputs.length,
		i, j,
		input,
		checkRules,
		rule,
		ruleLength;
	for(i = 0 ; i < length ; i++) {
		input = inputs[i];
		if(input.dataset.validateRuls) {
			checkRules = input.dataset.validateUrls.split(" ");
			ruleLength = checkRules.length;
			for(j = 0; j < ruleLength; j++){
				rule = checkRules[j];
				if(this.ruleSet.hasOwnProperty(rule)) {
					if(!this.ruleSet[rule].call(null, input)){
						return false;
					}
				}
			}
		}
	}
};

validator.decorate("require", function(input) {
	if(!input.value){
		alert(input.name + " is required");
		return false;
	}
	return true;
})

validator.decorate("alphabet", function(input) {
	var regex = /^[a-zA-Z\s]*$/;
	if(!regex.test(input.value)) {
		alert(input.name + " has to be only alphabets");
		return false;
	}
	return true;
});

validator.decorate("number", function(input) {
	var regex = /^[0-9]*$/;
	if(!regex.test(input.value)) {
		alert(input.name + " has to be only numbers");
		return false;
	}
	return true;
});
</script>
</body>
</html>
```


## References
* [속깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)
