# 일반적인 프로그래밍 원칙

## 아이템 57. 지역변수의 범위를 최소화하라

* 이번 아이템은 [아이템15 클래스와 맴버의 접근권한을 최소화하라](04-Classes-and-Interfaces.md#아이템-15-클래스와-멤버의-접근-권한을-최소화하라)
* 지역변수의 유효범위를 최소한으로 줄이면 코드가독성과 유지보수성이 높아지고 오류 가능성이 낮아짐
* 지역변수 범위를 줄이는 가장 강력한 방법은 가장 처음 사용할때 선언하는 것
* 거의 모든 지역변수는 선언과 동시에 초기화 되어야함
  * try-catch문은 예외, 초기화 구문에서 예외 발생가능시
* 반복변수값을 반복문 종료후 사용할 것이 아니라면 while보다는 for를 사용

```java
// 컬렉션이나 배열을 순회하는 권장 관용구 (for-each)
for (Element e : c) {
  ... //e 로 무언가를 한다.
}

// 반복자가 필요한 경우 (for)
for (Iterator<Element> i = c.iterator(); i.hasNext(); ) {
  ... //e와 i로 무언가를 한다.
}

// 지역변수를 최소화하는 반목분 관용구
for(int i = 0, n = expensiveComputation(); i < n; i++) {
  ... // i로 무언가를 한다.
}
```

* 지역변수 범위를 최소화하는 마지막 방법은 메서드를 작게 유지하고 한 가지 기능에 집중하는 것

## 아이템 58. 전통적인 for 문보다는 for-each 문을 사용하라

* 스트림이 제격인 작업이 존재하고 반복이 제격인 작업이 존재

```java
for (Iterator<Element> i = c.iterator(); i.hasNext();) {
  ...
}

for(int i=0; i < a.length; i++){
  ...
}
```

* 이 관용구 들이 while보다는 낫지만 가장 좋은 방법은 아님
  * 반복자와 인덱스 변수가 코드를 지저분하게 하기 때문
  * 컬렉션이냐 배열이냐에 따라 코트형태가 달라짐
* 이상의 문제는 for-each문을 사용하면 해결
  * 인덱스 변수를 사용하지 않음
  * 컬렉션과 배열 모두 처리할 수 있으므로 어떤 컨테이너를 다루는지 신경 쓰지 않아도 됨

```java
for (Element e : elements) {
  ...
}
```

* for-each문을 사용할 수 없는 3가지 상황이 존재함
  * 파괴적인 필터링: 반복하면서 컬렉션의 원소를 삭제해야할 경우
  * 변형: 리스트나 배열을 순회하면서 원소 값을 변경해야할 경우
  * 병렬 반복: 여러 컬렉션을 병렬로 순회해야할 경우

## 아이템 59. 라이브러리를 익히고 사용하라

* 자바 표준 라이브러리들을 보다 상세히 익히고 사용해야함
  * 그 코드를 작성한 전문가의 지식과 여러분보다 앞서 사용한 다른 프로그래머들의 경험을 활용가능
  * 일과 크게 관련 없는 문제를 해결하느라 시간을 허비하지 않아도 됨
  * 노력하지 않아도 성능이 지속해서 계선됨
  * 기능이 점점 많아 짐
  * 내가 작성한 코드가 여러사함에게 낯익은 코드가 됨
* 표준 라이브러리르 사용하는 것이 가장 좋지만 기능존재여부를 몰라 직접 개발해서 사용하는 경우가 많음
* 자바 프로그래머라면 다음과 같은 패키지에 어떤 기능들이 존재하는지 익숙해저야함
  * java.lang
  * java.util
  * java.io
* 때로는 라이브러리가 필요한 기능을 충분히 제공하지 못할 수도 있지만 우선 라이브러리를 사용하려고 시도하고 원하는 기능이 아니라고 판단되면 대안을 사용하자
* 대안으로는 고품질의 서드파티 라이브러리가 될 것이다
  * 예로 구글의 구아바 라이브러리
* 서드파티 라이브러리가 존재하지 않으면 직접 구현

## 아이템 60. 정확한 답이 필요하다면 float와 double은 피하라

* float과 double은 이진 부동소수점 연산에 사용되며 넓은 범위의 수를 빠르게 정밀한 근사치로 계산하도록 설계되었음
* float과 double 특히 금융 관련 계산과는 맞지 않음
* 금융계산에는 BigDecimal, int, long을 사용해야함
* 소수점 추적은 시스템에 맡기고, 코딩 시의 불편함이나 성능저하를 신경쓰지 않겠다면 BigDecimal을 사용
* 성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크지 않다면 int, long을 사용

## 아이템 61. 박싱된 기본 타입보다는 기본 타입을 사용하라

* 자바는 int, double, boolean 과 같은 기본타입과 String, List와 같은 참조타입이 존재
* 각 기본타입에는 박싱된 기본타입인 Integer, Double, Boolean이 존재
* [아이템6](02-Creating-and-Destroying-Objects.md#아이템-6-불필요한-객체-생성을-피하라)에서과 같이 오토박싱과 오토언박싱을 통해 구분없이 기본타입과 박싱된 기본타입을 사용가능
* 하지만 두 타입의 차이점을 알고 있어야함
  * 첫 번째, 기본 타입은 값만 가지고 있으나 박싱된 기본 타입은 값과 식별성이란 속성을 가짐
    * 두 인스턴스의 값이 같아도 서도 다르다고 인식가능
  * 두 번째, 기본 타입의 값은 언제나 유효하나 박싱된 기본 타입은 null을 가질 수 있음
  * 세 번째, 기본 타입이 박싱된 기본타입보다 시간과 메모리 사용면에서 더 효율적
* 다음과 같은 비교 연산이 존재할때 int는 잘 동작하지만 Integer를 사용하면 잘동작하지 않음

```java
Comparator<Integer> naturalOrder = (i,j) -> (i<j) ? -1 : (i == j ? 0 : 1);

// 잘못동작함
naturalOrder.compose(new Integer(42), new Integer(42));
```

* (i < j) 구분에서는 기본타입으로 변환되어 비교연산이 수행됨
* 하지만 i == j 에서 객체 참조의 식별을 가지게 되고 값이 아닌 객체 비교가 이루어 지므로 같지 않다고 판단됨
* 즉 박싱된 타입에 == 연산자를 사용하면 원하지 않는 결과가 반환됨
* 실무에서 기본 타입을 다루는 비교자가 필요하면 Comparator.naturalOrder()을 사용해야 함
* 비교자를 직접 구현할려면 비교자 생성 메서드나 기본 타입을 받는 정적 compare메서드를 사용해야함([아이템14]())

```java
Comparator<Integer> naturalOrder = (iBoxed, jBoxed) -> {
  // 기본형으로 변환
  int i = iBoxed, j = jBoxed;
  return i < j  ? -1 : (i == j ? 0 : 1);
}
```

## 아이템 62. 다른 타입이 적절하다면 문자열 사용을 피하라

* 문자열은 다른 값 타입을 대신하기에 적합하지 않다
* 파일, 네트워크, 키보드 입력으로 부터 데이터를 받을때 데이터가 진짜 문자열일 경우에 문자열을 사용해야함
* 문자열이 아닌경우 int, float, boolean등 적당한 타입으로 변경해야함
* 문자열은 열거타임을 대신하기 적합하지 않음([아이템34]())
* 문자열은 혼합타입을 대신하기에 적합하지 않음

```java
String compoundKey = className + "#" + i.next();
```

* 위와 같이 문자열 기반의 혼합된 타입을 사용하면 다음과 같은 문제가 존재
  * 개별 요소에 접근하려면 파싱으로인해 느리고, 귀찮고, 오류 가능성이 커짐
  * equals, toString, compareTo와 같은 메서드를 제공할 수 없음
  * 따라서 전용 클래스를 만들어 사용하는것이 적절함
* 문자열은 권한을 표현하기에 적합하지 않음

## 아이템 63. 문자열 연결은 느리니 주의하라

* n개의 문자열 연결 연산은 n^2에 비례
* 문자열은 불변이라 두 문자열을 연결할 경우 양쪽을 모두 복사해야하기 때문
* 성능을 포기하고 싶지 않다면 String대신 StringBuilder를 사용

## 아이템 64. 객체는 인터페이스를 사용해 참조하라

* [아이템51]()에서 매개변수 타입으로 클래스보다 인터페이스를 사용하라고 권장했음
* 이 조언은 객체는 클래스가 아닌 인터페이스로 참조하라고 확장할 수 있음
* 적합한 인터페이스만 있다면 반환값, 변수, 필드를 전부 인터페이스 타입으로 선언하라

```java
// best case
Set<Son> sonSet = new LinkedHashMap<>();

// bad case
LinkedHashMap<Son> sonSet = = new LinkedHashMap<>();
```

* 인터페이스를 타입으로 사용하는 습관을 길러두면 프로그램이 훨씬 유연해짐
* 주의할 점이 있다면 원래의 클래스가 인터페이스의 일반 규약 이외의 기능을 제공하거나, 주변 코드가 이기능에 기대어 동작한다면 클래스도 반드시 동일한 기능을 제공해야함
  * 예로들면 Map을 구현하는 클래스중 LinkedHashMap의 경우 순서를 보장하지만 HashMap은 순서를 보장하지 않음
* 구현 타입을 변경하는 이유
  * HashMap을 EnumMap으로 바꾸면 성능이 개선되면서 순서도 같음을 보장함, 단 키가 열거형인 경우만 사용가능
* 적합한 인터페이스가 존재하지 않는다면 클래스로 참조
  * String, BigInteger
  * 작성된 프레임워크가 제공하는 객체들
  * 인터페이스에 없는 특별한 메서드를 제공하는 클래스
* 적합한 인터페이스가 없다면 클래스의 계층구조 중 필요한 기능을 만족하는 가장 덜 구체적인 상위 클래스를 타입으로 사용

## 아이템 65. 리플렉션보다는 인터페이스를 사용하라

* 리플렉션(java.lang.relect)을 사용하면 프로그램에서 임의의 클래스에 접근가능
  * Class 객체가 주어지면 클래스의 생성자, 메서드, 필드에 해당하는 Constructor, Method, Field 인스턴스를 가져올 수 있고 이어서 인스턴스들로는 그 클래스의 멤버이름, 필드 타입, 메서드 시그니퍼 등을 가져올 수 있음
  * 또한 이 인스턴스들을 통해 클래스의 인스턴스를 생성하거나, 메서드를 호출, 필드에 접근할 수 있음
  * 예로들면 Method.invoke는 어떤 클래스의 어떤 객체가 가진 어떤 메서드라도 호출할 수 있다
* 리플렉션의 단점
  * 컴파일타임 타입 검사가 주는 이점을 하나도 누릴 수 없음
  * 리플렉션을 이용하면 코드가 지저분하고 장황해짐
  * 성능이 떨어짐
* 리플렉션은 아주 제한적인 형태로만 사용해야함

## 아이템 66. 네이티브 메서드는 신중히 사용하라

* 자바 네이티브 인터페이스(Java Native Interface, JNI)는 자바 프로그램이 네이티브 메서드를 호출하는 기술
  * 여기서 네이티브란 C나 C++ 같은 네이티브 프로그래밍으로 작성된 메서드를 의미
* 성능을 개선할 목적으로 네이티브 메서드를 사용하는 것은 거의 권장하지 않음
* 네이티브 메서드에는 심각한 단점이 존재함
  * 안전하지 않으므로 메모리 훼손 오류로 부터 안전하지 않음
  * 이식성이 떨어짐
  * 디버깅도 어려움
  * 속도가 느림
  * 자바와 네이티브 코드의 경계를 넘나들 때마다 비용이 발생
  * 자바와 네이티브 코드 사이의 접착 코드(glue code)를 작성해야해서 가독성이 떨어짐

## 아이템 67. 최적화는 신중히 하라

* 빠른 프로그램보다는 좋은 프로그램을 작성해라
  * 좋은 프로그램은 정보 은닉원칙을 따르므로 개별 구성요소의 내부를 독립적으로 설계
  * 즉 시스템의 나머지에 영향을 주지 않고 각 요소를 변경가능
* 구현상의 문제는 나중에 최적화해 해결 할 수 있지만 아키텍처의 결함이 성능을 제한하는 상황이라면 시스템 전체를 다시 작성해야함
* 따라서 설계단게에서 성능을 반드시 염두해야함
* 성능을 제한하는 설계를 피하라
* API를 설계할 때 성능에 주는 영향을 고려하라
* 성능을 위해 API를 왜곡하는 건 매우 안 좋은 생각이다
* 꼭 최적화 해야하는 상황이라면 각각의 최적화 시도 전후로 성능을 추가해라

## 아이템 68. 일반적으로 통용되는 명명 규칙을 따르라

* 자바 플랫폼은 명명 규칙이 잘 정립되어 있음
* 크게 철자와 문법 두 범부로 구분

### 철자 규칙

* 철자 규칙은 패키지, 클래스, 인터페이스, 메서드, 필드, 타입변수의 이름을 다룸
* 이 규칙을 어긴 API는 사용하기 어렵고 유지보수하기 힘듬
* 패키지와 모듈은 각 요소를 점으로 구분하여 계층적으로 구분
* 클래스와 인터페이스는 하나 이상의 단어조합으로 이루어지며 단어의 첫 글자는 대문자로 시작
* 메서드와 필드 이름은 첫글자를 소문자로 한다는 것만 빼면 클래스와 같음
* 단 상수 필드는 대문자로 이루어지며 단어가 언더바(_)로 구분
* 지역변수는 다른 맴버변수와 같음

# References

* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=){: target="_blank"}