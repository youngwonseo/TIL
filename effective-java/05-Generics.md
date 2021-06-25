# 제네릭

제네릭의 이점을 최대한 살리고 단점을 최소화하는 방법을 이야기함

## 아이템 26. 로 타입은 사용하지 말라

* 클래스와 인터페이스 선언에 **타입 매개변수**가 사용되면, 이를 제네릭 클래스 또는 제네릭 인터페이스라고 함
  * 예로들면 List<E>
* List<String>과 같이 매개변수화 타입을 정의
  * 원소 타입이 String인 리스트를 뜻하는 매개변수화 타입
* 제네릭 타입을 하나 정의하면 그에 딸린 로 타입(raw type)이 정의됨
  * List<E>의 로 타입은 List
* 아래와 같은 코드는 컴파일 타입에 stamps 컬렉션에 Coin이 추가되는것을 탐지할 수 없음
  * 런타임때 ClassCastException을 던짐

```java
private final Collection stamps = ...;
stamps.add(new Coin(...));

for (Iterator i = stamps.iterator(); i.hasNext(); ) {
  Stamp stamp = (Stamp) i.next(); // ClassCastException을 던짐
}
```

* 로 타입이 아닌 매개변수화된 컬렉션 타입을 사용하면 타입 안정성을 확보할 수 있음
* 이는 컴파일 타임에 에러를 검출가능

```java
private final Collection<Stamp> stamps = ...;
```

* 로 타입을 사용하면 제네릭이 제공하는 안정성과 표현력을 모두 잃게됨
  * 로 타입은 기존 버전에 대한 호환성 때문에 남겨진 것
* List<Object>와 같이 임의 객체를 허용하는 매개변수화 타입은 괜찮음
  * List는 제네릭에서 완전히 발을 뺀것이고 List<Object>는 모든 타입을 허용하겠다는 것
* 매개변수로 List를 받는 메서드에 List<String>은 넘길 수 있음
  * List<Object>를 받는 메서드에는 List<String>을 넘길 수 없음
  * 이는 제네릭의 하위타입 규칙 때문

```java
static int numElementsInCommon(Set s1, Set s2) {
  int result = 0;
  for (Object o1: s1)
    if (s2.contains(o1))
      result++;
  return result;
}
```

* 위 메서드는 동작은 하지만 로 타입을 사용해 안전하지 않음
* 아래 코드는 비한정적 와일드카드 타입을 사용, 타입에 안전하며 유연함

```java
static int numElementsInCommon(Set s1, Set s2)
```

* 로 타입을 사용하지 말라는 규칙에서 소소한 예외가 몇 가지 존재
  * class리터럴에는 로타입을 사용해야함
  * 런타임에는 제네릭 타입정보가 지워지므로 instanceOf 연산자는 비한정적 와일트카드 타입 이외의 매개변수화 타입에는 적용할 수 없음
    * 제네릭 타입에는 다음과 같이 instanceof를 사용

```java
if (o instanceof Set) {
  Set<?> s = (Set<?>)o;
}
```

## 아이템 27. 비검사 경고를 제거하라

* 제네릭 사용시 다양한 종류의 경고를 출력하는데 가능한 경고를 줄일 수 있도록 코드를 작성해야함
* 모든 경고를 제거하면 타입 안정성이 보장됨, 런타임에 ClassCastException이 발생할 일이 없어짐
* 경고를 제거할 수는 없지만 타입이 안전하다고 확신할 있으면 @SuppressWarnings("unchecked") 에너테이션을 추가
* 타입이 안전하다고 확신이 없는상태에서 @SuppressWarnings("unchecked")를 사용하는 것은 전혀 도움이 안 됨
* 이때 @SuppressWarnings("unchecked")는 가능한 좁은 범위에 지정해야 함
* @SuppressWarnings("unchecked") 사용시 경고를 무시한 이유를 주석으로 남겨야 함

## 아이템 28. 배열보다는 리스트를 사용하라

* 배열과 제네릭 타입은 다음과 같은 두 가지 차이점이 존재
  * 배열은 공변, 제네릭은 불공변
    * 이것은 배열은 상 하위 타입이 존재하지만 제네릭은 상위,하위 구분이 없음
  * 배열은 실체화됨
    * 배열은 런타임에서 자신의 타입을 인식하고 검사함
    * 제네릭은 런타임에서는 타입정보가 소거됨

* 이러한 차이로 배열과 제네릭은 잘 어우러지지 못함
* 제네릭 배열을 만들지 못하는 이유는 타입이 안전하지 않기 때문

## 아이템 29. 이왕이면 제네릭 타입으로 만들라

* 스택을 제네릭으로 구현

## 아이템 30. 이왕이면 제네릭 메서드로 만들라

```java
public static Set union(Set s1, Set s2) {
  Set result = new HashSet(s1);
  result.addAll(s2);
  return result;
}

public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
  Set<E> result = new HashSet<>(s1);
  result.addAll(s2);
  return result;
}
```

## 아이템 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

* 매개변수화 타입은 불공변
  * Type1, Type2가 존재할때 List<Type1>, List<Type2>는 상위타입도 하위타입도 아님
* 아이템29의 스택에 일련의 원소를 입력하는 pushAll를 정의해보자

```java
public void pushAll(Iterable<E> src) {
  for(E e: src) {
    push(e);
  }
}
```

* 이 매서드는 컴파일 되지만 완벽하지는 않음
  * 스택의 원소 타입과 일치하면 잘 동작함
  * 만약 Stack<Number>로 선언한후 pushAll(intVal)을 호출하면?
  * intVal은 Integer이므로 Number의 하위 타입으로서 논리적으로 잘 동작할것같음
  * 하지만 매개변수화 타입이 불공변이기 때문에 오류 발생
* 이런 상황에 대처할 수 있는 한정적 와일드카드 타입이라는 특별한 매개변수화 타입을 지원

```java
public void pushAll(Iterable<? extends E> src) {
  for (E e: src) {
    push(e);
  }
}
```

* E의 하위 타입의 Iterable (E자기 자신도 포함)
* 이번에는 모든 원소를 추출하는 popAll을 정의

```java
public void popAll(Collection<E> dst) {
  while(!isEmpty()) {
    dst.add(pop());
  }
}

Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = ...;
numberStack.popAll(objects);
```

* Stack<Number>의 원소를 Object용 컬렉션으로 옮기려고하면 Collection<Number>의 하위타입이 아니라는 오류 발생
* E의 상위 타입 Collection를 명시해야함

```java
public void popAll(Collection<? super E> dst) {
  while(!isEmpty()) {
    dst.add(pop());
  }
}
```

* 유연성을 극대화 하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입 사용이 필요
* 그러나 입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을게 없음
  * 타입을 확실히 지정해야할 상황에서는 와일드카드 타입을 사용해서는 안됨

## 아이템 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라

* 가변인수 메서드(아이템53)와 제네릭은 자바5 때 함께 추가되어 서로 잘 어우러질것같지만 그렇지 않음
* 가변인수 메서드를 호출하면 가변인수를 위한 배열을 생성함
* 그런데 내부로 감춰야 했을 이 배열을 클라이언트에 노출하는 문제가 발생
* 그 결과 가변인수를 위한 매개변수에 제네릭이나 매개변수화 타입이 포함되면 알기 어려운 컴파일 경고가 발생
* 


```java
static void dangerous(List<String>... stringLists) {
  List<Integer> intList = List.of(42);
  Object[] objects = stringLists;
  objects[0] = intList; // 힙오염
  String s = stringLists[0].get(0); //ClassCastException을
}
```

* 위 메서드는 형변환을 시도하는 곳이 보이지 않는데 ClassCastException을 던짐
* 마지막 줄에 컴파일러가 생성한 보이지 않는 형변환이 숨겨져 있기 때문
* 이처럼 제네릭 varargs 배열 매개변수에 값을 저장하는 것은 안전하지 않음
* 

## 아이템 33. 타입 안전 이종 컨테이너를 고려하라

* 매개변수화 되는 대상은 (원소가 아닌) 컨테이너 자신 
* 따라서 하나의 컨테이너에서 매개변수화 할 수 있는 타입의 수가 제한됨
* 보다 유연한, 모든 컨테이너 요소에 대해 타입안전 보장하기 위한 방법으로 컨테이너 대신 키를 매개변수화 하는 방법이 존재
  * 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공함으로써 제네릭 타입 시스템이 키와 같음을 보장해줌
  * 이러한 설계 방식을 타입 안전 이종 컨테이너 패턴(type safe heterogeneous container pattern)이라 함

```java
public class Favorites {
  public <T> void putFavorite(Class<T> type, T instance);
  public <T> T getFavorite(Class<T> type);
}


public static void main(String[] args) {
  Favorites f = new Favorites();

  f.putFavorite(String.class, "Java");
  f.putFavorite(Integer.class, 0xcafebabe);
  f.putFavorite(Class.class, Favorites.class);

  String favoritesString = f.getFavorite(String.class);
  int favoriteInteger = f.getFavorite(Integer.class);
  Class<?> favoriteClass = f.getFavorite(Class.class);

  System.out.printf("%s %x %s%n", favoriteString, favoriteInteger, favoriteClass.getName());
}
```

## References

* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=)