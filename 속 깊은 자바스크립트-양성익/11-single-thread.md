# 단일 스레드

자바스크립트의 대표적인 특징가운데 한 가지는 단일 스레드에서 동작한다는 것입니다. 

## setTimeout과 setInterval
여기서는 setTimeout과 setInterval을 이용해 단일스레드의 동작방식을 이해해 봅시다(html파일위에 자바스크립트코드가 입력되어있지만 html은 사용하지 않습니다) . 참고로 setTimeout은 첫 번째 인자인 콜백을 두 번째 인자인 밀리세컨드후 호출하는 함수이고 setInterval은 첫 번째 인자인 콜백을 두 번째 인자인 밀리세컨트마다 호출하는 함수입니다. 즉 setTimeout과 setInterval함수의 차이는 한 번 호출, 매 주기 호출이라고 생각할 수 있습니다. 다음은 예제입니다.
```html
<!DOCTYPE html>
<html  lang="en">
	<head>
		<meta  charset="UTF-8">
		<meta  name="viewport"  content="width=device-width, initial-scale=1.0">
		<meta  http-equiv="X-UA-Compatible"  content="ie=edge">
		<title>Document</title>
	</head>
	<body>
		<script>
			(function(){
				console.log("start: \t\t\t\t\t\t\t" + Date.now());
				var  intervalCount = 1;
				var  intervalId = setInterval(function(){
					console.log("#"+intervalCount+" setInterval: \t\t\t\t" + Date.now());
					if(++intervalCount > 3){
						clearInterval(intervalId);
					}
				},2000);

				setTimeout(function(){
					console.log("setTimeout: \t\t\t\t\t" + Date.now());
					sleep(10000);
				},5000);

				sleep(10000);

				function  sleep(waitMilliseconds){
					var  waitUntil = Date.now() + waitMilliseconds;
					while(Date.now() < waitUntil){
						;
					}
				}
			}());
		</script>
	</body>
</html>
```
결과부터 살펴보며 코드를 이해해 봅시다. **여기서 중요한 부분은 밀리세컨드가 표기된 시각부분입니다.**
![](/public/js-single-thread-2.png)
즉시실행함수의 실행흐름을 살펴보면 먼저 시작시간의 밀리세컨드를 출력합니다(Data.now()). 이후 setInterval과 setTimeout을 등록하는데 각각 2000, 5000 밀리세컨드 후 실행되도록 등록합니다.  여기까지만 살펴보면 사용자의 의도는 setInterval이 2초마다 실행되고 setTimeout이 5초후 한번 실행되기를 의도합니다. 또한 여기서 **실행된다는 의미는 스레드 큐에 등록하는 것을 의미합니다. 스레드 큐에 등록되면 처음부터 하나씩 꺼내서 실행을 하게 되죠(FIFO).** 하지만 의도와는 다르게 그 다음 코드에 sleep함수로 인해 실행되는 시간을 보장받을 수 없게 됩니다. 이유는 sleep에서 10초동안 루프를 돌게 됩니다. 이 루프를 도는 동안에는 스레드큐에 작업이 들어와도 실행을 하지 않습니다. 즉 큐에서 대기하고 있는 것이죠. 
10초후에는 sleep함수가 종료되고 스레드 큐에 있던 setInterval과 setTimeout이 실행이 됩니다. 이미 큐에 같이 들어가 있었으므로(setInterval은 인자가 2초 이므로 먼저 큐에 들어가있기 때문에 먼저 실행됩니다) 거의 동시에 실행되죠. 하지만 setTimeout의 콜백함수를 보면 sleep으로 10초를 대기합니다. 즉! setInterval의 주기인 2초가 지나도 setTimout의 콜백에 존재하는 sleep이 실행중입니다. 그러므로 다시 10초를 대기후 setInterval이 실행됩니다. 2초라는 주기와 상관없이 말이죠. 이제 10초후 setInterval이 실행되고 실행이 완료되면 다시 2초후 setInterval이 실행됩니다. 이번에는 sleep이 없었기 때문에 2초라는 시간이 제대로 지켜졌습니다. 
도식화된 그림을 보면 다음과 같습니다. 그림을 보고 다시 위의 설명을 보면 이해하기 수월합니다.
![](/public/js-single-thread-1.png)
(파랑색은 실행 중인 작업, 빨간생은 생성된 작업)
여기서 우리는 setInterval의 특성을 한 가지 더 알수 있습니다. 그것은 setInterval의 콜백함수는 실행이 된 후, 즉 스레드 큐에서 호출되어 실행된 후 부터 다시 두 번째 인자만큼의 시간후 스레드 큐에 등록된다는 사실입니다. 
이 처럼 단일 스레드기반 자바스크립트에서는 스레드큐가 하나만 존재하여 스레드큐에 작업이 등록되도 다른작업으로 인해 실행시간이 지연될 수있는 특징을 가집니다. 이것은 setInterval과 setTimeout만이 아니라 비동기 또는 이벤트로 호출되는 모든 작업이 스레드큐에 등록된 후 자신의 차례가오면 실행된다는 특징을 보여줍니다.


## References
* [속 깊은 Javascript](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710098&orderClick=LEA&Kc=)
