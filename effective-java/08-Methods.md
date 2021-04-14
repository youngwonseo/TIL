# 메서드

메서드(생성자 포함)의 매개변수, 반환값 처리와 시그니처 설계, 문서화 방법을 사용성, 견고성, 유연성에 집중해 설명합니다.

## 아이템 49. 매개변수가 유효한지 검사하라
* 매서드의 매개변수는 메서드의 시작부분에서 유효성을 검사해야함
  * 메서드 실행 중간에 오류가 발견된다면 모호한 예외를 던지며 실패 할 수 있음
  * 메서드가 성공적으로 실행되나 잘못된 결과를 반환할 수 있음
  * 원자성을 어기는 결과를 생성함
* 유효성의 조건은 반드시 문서화해야함
* 매개변수가 잘못되면 보통의 경우 다음과 같은 예외를 던짐
  * IllegalArgumentException
  * IndexOutBoundsException
  * NullPointerException
* 문서화시 발생할 수 있는 예외도 함께 포함해야함  
* 자바 7 부터는 java.util.Objects.requireNonNull 메서드를 지원
  * null일 경우 예외 메시지를 작성가능

```java
this.strategy = Objects.requireNonNull(strategy, "전략");
```
<br/>

* 자바 9 부터는 Objects에 범위 검사 기능이 추가됨
  * checkFromIndexSize
  * checkFromToIndex
  * checkIndex
* 공개되지 않는 패키지의 메서드들은 외부에서 호출되지 않으므로 assert를 사용해 유효성을 검증
  * 실패시 AssertionError를 던짐
  * 런타임에서는 실행을 생략하므로 성능 저하가 없음

<br/>

## 아이템 50. 적시에 방어적 복사본을 만들라
* 악의적이든 실수든 클래스를 외부에서 오작동하게 만들 수 있음
* 방어적 복사본은 이를 예방하는 방법
* 다음은 외부에서 객체 내부에 접근하는 한 가지 상황임

```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
end.setYear(78);
```

* Date객체는 더이상 사용해서는 안될 낡은 API, 대신 LocalDateTime 또는 ZonedDateTime을 사용
* 이러한 상황을 방지하기 위해 생성자에서 받은 매개변수는 복사해서 맴버로 할당해야함
  * 이와 마찬가지로 내부객체를 반환할때에는 복사된 객체를 반환해야함
* 아이템49의 유효성은 복사본을 대상으로 검사해야함
* 매개변수가 다른사람에 의해 조작될 수 있는 타입이라면 복사본을 생성하기 위해 clone을 사용해서는 안됨
* 메서드든 생성자든 클라이언트가 제공한 객체의 참조를 내부의 자료구조에 보관해야할 때면 항상 그 객체가 잠재적으로 변경될 수 있는지 고려해야함
  * 예로들어 외부에서 받은 객체를 내부의 Set, Map과 같은 객체에 저장된 후, 외부에서 해당 객체가 조작되면 Set이나 Map의 불변식이 깨짐

<br/>

## 아이템 51. 메서드 시그니처를 신중히 설계하라
* 

<br/>

## 아이템 52. 다중정의는 신중히 사용하라
* 

<br/>

## 아이템 53. 가변인수는 신중히 사용하라
* 

<br/>

## 아이템 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라
* 컬렉션이나 배열같은 컨테이너가 비었을 때 컨테이너 대신 null을 반환하면 클라이언트에서는 null을 처리하는 코드를 추가해야함
  * 사용하기 어렵고 오류처리 코드도 늘어남
* null대신 빈 컬렉션이나 배열을 반환해야함
* null이 아닌 빈컨테이너를 반환하는 것은 비용상 문제가 있다고 생각할 수 있음
  * 그러나 신경 쓸 수준이 못됨
  * 빈 불변 컬렉션을 반환해서 처리 가능, 그러나 이것은 꼭 필요한 경우만 사용
    * Collections.emptyList()
    * Collections.emptySet()
* 배열도 null이아닌 길이가 0인 배열을 반환

```java
public Cheese[] getCheese() {
  return cheeseInStock.toArray(new Cheese[0]);
}
```

<br/>

## 아이템 55. 옵셔널 반환은 신중히 하라
* Java8 이전에는 값 반환을 할 수 없는 상황에 대해 2가지 선택사항이 있음
* 첫 번째, 예외 발생
  * 그러나 예외는 진짜 예외적인 상황에 사용해야함(아이템 69)
  * 예외 생성에 있어 스택 추적 전체를 캡처해야하므로 비용도 만만치 않음
* 두 번째 null 반환
  * null을 위한 별도의 코드를 작성해야함
* 이러한 상황에서 Java8 이후에는 Optional<T>라는 선택지가 생김
  * Optional<T>는 T타입을 참조하거나, 또는 아무것도 없거나를 처리

##### Optional 생성
* 다음은 컬렉션에서 최대값을 구해 Optional<E>로 반환하는 코드
* 적절한 정적 펙터리를 사용해 Optional을 반환하면 됨

```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
  if (c.isEmpty())
    return Optional.empty();

  E result = null;
  for (E e : c) {
    if (result == null || e.compareTo(result) > 0)
      result = Objects.requireNonNull(e);
  }
  return Optional.of(result);
}
```

<br/>

##### Optional 소비

```java
// null일 경우 기본값을 지정가능
String lastWordInLexicon = max(words).orElse("단어 없음...");

// 값이 없을 시 원하는 예외를 던질 수 있음
Toy myToy = max(toys).orElseThrow(TemperTantrumException::new);

// 항상 그값이 채워져 있다고 가정, 없으면 예외(NoSuchElementException)
Element lastNobleGas = max(Elements.NOBLE_GASES).get();
```

* 결과가 없을 수 있으며, 클라이언트가 이상황을 특별하게 처리해야한다면 Optional<T>를 반환
* Optional을 컬렉션의 키,값,원소나 배열의 원소로 사용하는게 적절한 상황은 거의 없음 
<br/>

## 아이템 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라
* 

<br/>

# References
* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=){: target="_blank"}