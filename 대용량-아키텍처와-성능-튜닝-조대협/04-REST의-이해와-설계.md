

## REST 란?

REST(Representational State Transfer)는 웹 창시자 중의 한 명인 로이필딩(Roy Fielding)에 의해 웹의 장점을 최대한 활용할 수 있는 아키텍처로 처음 소개됬습니다. 웹기반으로 리소스(데이터에 대한 입력, 수정, 삭제 등)를 관리하기 위한 아키텍처 구조이며 현재는 많은 API들이 REST기반에서 JSON을 이용한 형태로 제공되고 있습니다.
최근에는 효율적인 API를 제공하기 위한 아키텍처로 GraphQL이 소개되고 있지만 여전히 많은 기업 또는 서비스에서 API제공 시 REST기반으로 제공되고 있을 만큼 API개발에 대한 표준으로 자리잡고있습니다. 


## 1. HTTP메서드

REST에서는 수행할 작업의 종류를 HTTP메서드를 통해 구분됩니다. 즉 HTTP에서 특정 URL에 어떤 메소드로 접근하냐에 따라 작업의 종류가 결정됩니다.(URL이 동일하더라도). 아래는 CRUD에 대한 작업과 HTTP메서드의 관계입니다.
 
|Method|Meaning|Idempotent|
|--|--|--|
| POST | Create | No |
| GET | Select | Yes |
| PUT | Update | Yes |
| DELETE| Delete | Yes |

 가장 우측 Idempotent는 여러번 수행 시 데이터의 동일성 여부를 나타냅니다. 즉 POST연산은 데이터를 추가하는 작업을 수행하므로 여러번 수행시 계속해서 데이터가 추가(idx를 증가시키면서)가 되지만, PUT의 수정일 경우 계속 수행해도 같은 idx를 가진 데이터를 동일한 값으로 수정하므로 여러번을 실행하더라도 데이터가 동일합니다. **Idempotent 같은 사항들은 REST의 표준을 지키는데 매우 중요한 사항이므로 REST 구현 시 100% 만족시키도록 구현되어야 해야합니다.**

 
## 2. REST의 리소스
다음은 REST가 **리소스 지향적 아키텍처**이다. 여기서 대해 리소스를 표현하는 규칙이 존재하는데 다음과 같습니다.
* 모든 리소스는 명사로 표현
* 즉, 사용자라는 리소스를 http://myweb/users 로 표현하였다면 gildong이라는 아이디의 리소스는 http://myweb/users/gildong 이라는 형태로 정의

  

## 3. REST 예제
딱히 설명한건 없지만 이것이 REST의 전부입니다. 이제무터 REST에 대한 실제 예제를 살펴보겠습니다. 내용은 HTTP BODY에 추가해서 해당 URL에 해당 METHOD로 요청(request)하는것이 핵심입니다.

### 3.1 사용자 생성
```json
HTTP POST, http://myweb/users/
{
	"name":"gildong",
	"address":"busan",
	"gender":"male"
}
```
POST와 사용자를 나타내는 리소스에 생성할 데이터를 JSON형식으로 Body에 포함시켜 요청합니다. 성공여부를 true, false 또는 error message 등을 이용하여 받을 수 있습니다.

  
### 3.2 사용자 조회
```json
HTTP GET, http://myweb/users/
```
```json
HTTP GET, http://myweb/users/gildong
```
처음 예제는 전체목록에 해당하고 두번째 예제는 gildong의 정보를 요청합니다. GET으로 요청하고 있고 Body에는 아무 내용을 포함시키지 않았습니다.

  
### 3.3 사용자 업데이트

```json
HTTP PUT, http://myweb/users/gildong
{
	"address":"daegu",
	"gender":"female"
}
```
gildong의 리소스에 대하 성별을 Body에 포함시켜 PUT으로 요청합니다.


### 3.4 사용자 삭제
```
HTTP DELETE, http://myweb/users/gildong
```
삭제를 위해 DELETE에 요청합니다. 물론 아래와 같이 요청하면 users에 대한 전체 목록이 삭제됩니다.

```
HTTP DELETE, http://myweb/users/
```

  
  
## 4. REST 특성
REST에 대한 조금 더 상세한 내용을 알아봅시다!  

### 4.1 유니폼 인터페이스(Uniform Interface)
* 플랫폼에 상관없이 동일한 인터페이스를 제공함으로써 클라이언트가 쉽게 사용 가능
* 프론트앤드의 기술에 상관없이 백앤드를 REST로 구현하여 비즈니스 로직을 통일할 수 있음

 
### 4.2 무상태/스테이트리스 (Stateless)
* 세션같은 클라이언트의 상태를 기억하지 않음
* 인증 같은 부분은 토큰등의 API KEY를 발급하여 관리

  

### 4.3 캐시 가능(Cacheable)
* HTTP에서 제공하는 장점인 캐시, 로드벨런스 등을 그래로 사용할 수 있음

  
### 4.4 자체 표현 구조(Self-descriptiveness)
* 리소스의 경로와 정해진 Method를 보고 API해석이 가능
* 최소한의 문서로 쉽게 사용 가능

  

### 4.5 클라이언트 서버 구조(Client-Server)

* 클라이언트-서버라는 구조로 각 부분의 개발 의존성을 최소화가 가능

  

### 4.6 계층형 구조(Layered System)

* 순수 비즈니스로직, 인증, 암호화, 로드 밸런싱 등의 계층을 나누어 개발이 가능

  

## 5. REST 안티패턴
다음은 REST에 대한 안티패턴의 구조입니다. REST스러운 또는 유사REST라는 용어가 존재할 만큼 REST에 대한 사항들을 완벽하게 구현하지 않아도 그럭저럭 쓸만한 API를, 즉 REST스럽게 쓸만한 API를 개발할 수 있습니다. 하지만 그것은 REST라고 부르기 힘듭니다. REST는 무조건 표준을 100%만족할 경우에 대해서만 RESTful을 호칭할 수 있습니다.  

### 5.1 GET/POST를 이용한 터널링
* http://myweb/users?method=update&id=gildong 이 전형적인 GET을 이요한 터널링
* 즉 Method의 규약을 어기고 쿼리 파라미터로 데이터를 주고받는 방식은 REST가 아님

  

### 5.2 Self-descriptiveness 속성을 사용하지 않음
* 리소스의 URI와 Method, 그리고 정해진 포맷의 메세지 등으로 API가 구현되어야 하는데 위의 터널링처럼 규약을 지키지 않은 것을 의미

  

### HTTP응답 코드를 사용하지 않음
* 200,403,404,500 등의 응답 코드를 사용해서 구현되어야 한다. 그렇지 않으면 REST라 할 수 없음


## References
* 대용량 아키텍처와 성능 튜닝 - 조대협 | 프리렉 (<http://www.yes24.com/24/goods/17018954>)