# 람다와 스트림

자바8에서 함수형 인터페이스, 람다, 메더스 참조 개념과 함께 스트림API가 추가됨. 이기능들을 효과적으로 사용하는 방법을 알아봄

## 아이템 42. 익명 클래스보다는 람다를 사용하라

* 함수 객체를 만드는 주요수단으로 익명 클래스가 사용됨
	* 전략 패턴처럼, 함수 객체를 사용하는 과거 객체 지향 디자인 패턴에서는 익명 클래스면 충분했음
	* 자바 특성상 익명 클래스 방식은 코드가 너무 길기 때문에 자바는 함수형 프로그로그래밍에 적합하지 않았음
* 자바8에 와서 추상 메서드 하나짜리 인터페이스가 대우를 받게됨
	* 함수형 인터페이스라 부르는 이 인터페이스의 인스턴스를 람다식을 사용해 생성 가능해짐

```java
// 익명 클래스
Collection.sort(words, new Comparator<String>() {
	public int compare(String s1, String s2) {
		return Integer.compare(s1.length(), s2.length());
	}
});

// 람다식
Collection.sort(words, (s1, s2) -> Integer.compare(s1.length(), s2.length()));

// 비교자 생성 메서드
Collection.sort(words, comparingInt(String::length));

// List 인터페이스에 추가된 sort
words.sort(comparingInt(String::length));
```

* 타입을 명시해야하는 코드가 더 명확할 때만 제외하고는, 람다의 모든 매개변수 타입은 생략하자
* 람다는 이름이 없고 문서화도 할 수 없음
	* 따라서 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 사용하면 안됨

## 아이템 43. 람다보다는 메서드 참조를 사용하라

* 람다가 익명 클래스보다 나은점 중 가장 큰 특징은 간결함
* 그러나 자바에는 함수 객체를 람다보다 더 간결하게 만드는 방법이 존재, 이 방법이 메서드 참조(method reference)

```java
// 람다
map.merge(key, 1, (count, incr) -> count + incr);

// 메서드 참조
map.merge(key, 1, Integer::sum);
```

* **메서드 참조쪽이 더 짧고 명확하면 메서드 참조, 그렇지 않으면 람다를 사용**

## 아이템 44. 표준 함수형 인터페이스를 사용하라

* util.function 패키지에 다양한 용도의 표준 함수형 인터페이스가 존재
* 필요한 용도에 맞느게 존재한다면, 직접 구현하지 말고 표준 함수형 인터페이스 활용을 권장
* 직접 만든 함수형 인터페이스에는 항상 @FunctionInterface 애너테이션을 사용해야함

## 아이템 45. 스트림은 주의해서 사용하라

* 스트림 API는 다량의 데이터 처리 작업(순차적이든 병렬적이든)을 돕고자 자바8에 추가됨
* 이 API가 제공하는 추상 개념 중 핵심은 두가지
	* 스트림은 데이터 원소의 유한 또는 무한 시퀀스를 의미
	* 스트림 파이트라인은 이 원소들로 수행하는 연산의 단계를 표현하는 개념
* 스트림을 과용하면 프로그램이 읽거나 유지보수하기 어려워짐
* 기존 코드는 스트림을 사용하도록 리팩터링하되, 새 코드가 더 나아 보일 때만 반영

```java
// 반복버전의 데카르트 곱
private static List<Card> newDeck() {
	List<Card> result = new ArrayList<>();
	for (Suit suit : Suit.values())
		for (Rank rank : Rank.values())
		  result.add(new Card(suit, rank));
	return result;
}

// 스트림버전의 데카르트 곱
private static List<Card> newDeck() {
	return Stream.of(Suit.values())
	  .flatMap(suit -> 
			Stream.of(Rank.values())
				.map(rank -> new Card(suit, rank)))
}
```

## 아이템 46. 스트림에서는 부작용 없는 함수를 사용하라

* 스트림 패러다임의 핵심은 계산을 일련의 변환으로 재구성하는 부분
* 이때 각 단계는 가능한 이전 단계의 결과를 받아 처리하는 순수함수이어야함
* 다음은 주어진 파일에 존재하는 단어를 카운팅하는 예제

```java
// 스트림 패러다임을 이해하지 못한채 사용된 API
// 스트림, 람다, 메서드 참조를 사용했고 결과 또한 올바름
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
	words.forEach(word -> 
		freq.merge(word.toLowerCase(), 1L, Long::sum);
	)
}


// 스트림을 제대로 활용한
Map<String, Long> freq;
try (Stream<String> words = new Scanner(file).tokens()) {
	freq = words.collect(groupingBy(String::toLowerCase, counting()));
}
```

## 아이템 47. 반환 타입으로는 스트림보다 컬렉션이 낫다

* 자바7 까지는 시퀀스, 일련의 원소반환을 위해 Collection, Set, List같은 컬렉션 인터페이스 또는 Interable이나 배열을 사용
* 기본은 컬렉션, for-each나 컬렉션을 사용할 수 없으면 Iterable, 성능이 민감한 상황이면 배열을 사용함
* 자바8에서 스트림이라는 개념이 존재하면서 이 선택이 아주 복잡한 일이 되어버림
* 원소 시퀀스를 반환하는 공개 API의 반환 타입에는 Collection이나 그 하위 타입을 쓰는 게 일반적으로 최선
	* Collection 인터페이스는 Iterable의 하위타입
	* stream 메서드 지원, 즉 반복과 스트림을 동시에 지원
	* 원소개수가 적다면 ArrayList와 같은 표준 컬렉션이 적합
* 단지 컬렉션을 반환한다는 이유로 덩치 큰 시퀀스를 메모리에 올려서는 안됨

## 아이템 48. 스트림 병렬화는 주의해서 적용하라

* 자바는 동시성 처리에 대해 항상 앞서감
* 자바8 부터 parallel 메서드만 한번 호출하면 파이프라인을 병렬 실행할 수 있는 스트림을 지원
* 자바로 동시성 프로그램을 작성하기가 점점 쉬워지고있지만 이를 올바르고 빠르게 작성하는 일은 여전히 어려움
* 데이터 소스가 Stream.iterate거나 중간 연산으로 limit를 쓰면 파이프라인 병렬화로는 성능 개선을 기대할 수 없음
* 스트림 소스가 ArrayList, HashMap, HashSet, ConcurrentHashMap의 인스턴스이거나 배열, int 범위, long 범위 일때 병렬화의 효과가 가장 좋음
* 스트림을 잘못 병렬화하면 성능이 나빠질 뿐만 아니라 결과가 잘못되거나 예상 못한 동작이 발생할 수 있음

## Reference

* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=)