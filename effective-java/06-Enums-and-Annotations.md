# 열거 타입과 애너테이션

## 아이템 34. int 상수 대신 열거 타입을 사용하라

* 열거형은 정해진 상수값을 자료구조로 표현한것
* 이전에는 다음과 같이 정수 또는 문자열 기반의 상수를 선언후 사용

```java
public static final int APPLE_FUJI          = 0;
public static final int APPLE_PIPPIN        = 1;
public static final int APPLE_GRANNY_SMITH  = 2;
```

```java
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
```

* 열거타입은 싱글턴의 일반화한 형태라고 볼 수 있음
* 열거타입은 컴파일타임 타입 안정성을 제공
* 열거타입은 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현할 수도 있음

## 아이템 35. ordinal 메서드 대신 인스턴스 필드를 사용하라

* 열거 타입은 해당 상수가 열거 타입에서 몇 번째 위치하는지 반한하는 ordinal이라는 메서드를 제공
* 하지만 ordinal를 사용하는 로직을 구현시 열거타입내 상수의 순서를 바꾸거나 중간에 값을 삭제하는 등의 변경이 발생하면 dummy를 추가하는 등의 작업을 해야함
* ordinal 메서드 보다는 인스턴스 필드에 원하는 값을 저장

```java
public enum Ensemble {
  SOLO(1), DUET(2), TRIO(3), QUARTET(4);
  // SOLO(1), DUET(2), QUARTET(4); 와 같이 TRIO를 삭제해도 작동

  private final int numberOfMusicians;
  Ensemble(int size) { 
    this.numberOfMusicians = size;
  }
  public int numberOfMusicians() {
    return numberOfMusicians;
  }
}
```

## 아이템 36. 비트 필드 대신 EnumSet을 사용하라

* 예전에는 각 상수에 2의 거듭제곱값을 할당한 정수 열거 패턴을 사용했음

```java
public static final int STYLE_BOLD           = 1 << 0;
public static final int STYLE_ITALIC         = 1 << 1;
public static final int STYLE_UNDERLINE      = 1 << 2;
public static final int STYLE_STRIKETHROUGH  = 1 << 3;

...

// 다음과 같이 조합하여 사용
text.applyStyle(STYLE_BOLD | STYLE_ITELIC);
```

* 위에서와 같이 2의 거듭제곱으로 표현하면 OR 비트연산자로 조합을 표현가능
* 위와 같은 상황도 기존의 정수 열거 상수의 단점을 그대로 가지고 있음
  * 해당 값이 출력되면 해석하기 어려움
  * 비트필드 하나에 녹아 있는 모든 원소를 순회하기도 까다로움
  * 최대 몇 비트가 필요한지 API 작성 시 미리 예측해서 자료형을 선택해야함
* 열거 타입에서는 이와 같은 상황을 위해 EnumSet 클래스를 제공
* EnumSet은 열거 타입 상수에 대한 집합을 효과적으로 표현
* EnumSet은 Set인터페이스를 구현함
* EnumSet 내부는 피트 벡터로 구현되어 있음

```java
public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }
...

// applyStyles은 Set인터페이스 매겨형의 파라미터를 받음
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

* EnumSet의 유일한 단점은 불변 EnumSet을 만들수 없는 것(자바11까지 이수정은 이루어지지 않음)
* 불변 EnumSet을 위해(성능과 명확성은 조금 희생되지만) Collections.unmodifiableSet로 EnumSet을 감싸서 사용할 수 있음

## 아이템 37. ordinal 인덱싱 대신 EnumMap을 사용하라

* 열거 타입별로 저장하는 배열이 필요할 시 배열을 열거타입의 순서로 구분하고 ordinal을 호출해 해당 배열에 접근할 수 있음
* 하지만 배열의 각 인덱스의 의미를 모르고 열거 타입에 속한 값들의 순서가 변경되거나 잘못된 값으로 인해 ArrayInfexOutOfBoundsException이 발생할 가능서잉 존재
* 이를 해결하기 위해 EnumMap을 사용할 수 있음

```java
class Plain {
  enum LifeCyle { ANNUAL, PERENNIAL, BIENNIAL }
  ...
  final LifeCycle lifeCyle;
  ...
}
...

// lifeCycle별 Plant를 구분하는 EnumMap
Map<Plant.LifeCyle, Set<Plant>> plaintsByLifeCyle = new EnumMap<>(Plant.LifeCycle.class);
```

* EnumMap은 내부적으로 배열을 사용하기 때문에 배열을 사용때화 성능이 비견될 수 있음
* 스트림 사용해서 맵을 관리하면 코드를 더욱 줄일 수 있음

```java
Arrays.stream(garden).collect(groupingBy(p->p.lifeCycle));
```

## 아이템 38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

* 열거 타입은 거의 모든 상황에서 이 책 초판에서 소개한 타입 안전 열거 패턴보다 우수함
* 하지만 한 가지 예외 상황이 존재하는데, 타입 안전 열거 패턴은 확장할 수 있으나 열거 타입은 그럴 수 없다는 점
* 즉 추가 상수가 필요하면 그대로 가져와 확장할 수 있으나 열거 타입은 그럴 수 없음
  * 사실 열거 타입을 확장하는 건 좋은 생각이 아님
* 열거 타입이 확장되어 사용되어야 할 상황이 존재하는데 그것은 연산 코드
  * API가 제공하는 기본 연산 외에 사용자가 확장 연산을 추가해야할 경우
* 열거 타입에서 이러한 효과를 주기 위해서는 열거타입이 인터페이스 구현할 수 있다는 것을 이용

## 아이템 39. 명명 패턴보다 애너테이션을 사용하라

* 전통적으로 도구나 프레임워크가 특별히 다뤄야 할 프로그램 요소는 구분되는 명명 패턴이 적용되어왔음
  * 예로 들면 JUnit3까지 테스트 메서드 이름이 test로 시작되어야 했음
* 명명패턴은 여러가지 단점을 가짐
  * 오타가 나면안됨
  * 올바른 프로그램 요소에서만 사용된다고 보증할 수 없음
  * 프로그램 요소를 매개변수로 전달할 마땅한 방법이 없음
* 애너테이션은 이 모든 단점을 해결해줌

## 아이템 40. @Override 애너테이션을 일관되게 사용하라

* 상위클래스의 메서드를 재정의할때는 항상 @Override애너테이션을 사용해야함
  * 재정의 실수를 컴파일러가 알려줌

## 아이템 41. 정의하려는 것이 타입이라면 마커 인터페이스를 사용하라

* 마커 인터페이스란 아무런 메서드도 없고 자신을 구현하는 클래스가 특정 속성을 가짐을 표시해주는 인터페이스를 의미함
  * Serializable 인터페이스가 여기에 해당
  * Serializable을 구현한 클래스의 인스턴스는 ObjectOutputStream을 통해 쓸 수 있다고, 즉 직렬화(serialization) 할 수 있다고 알려줌
* 마커 애너테이션이 등장하면서 마커 인터페이스는 구식이 되었다는 이야기가 있지만 이는 사실이 아님
* 다음과 같은 2가지 측면에서 마커 인터페이스가 마커 애너테이션보다 나음
  1. 마커 인터페이스는 이를 구현한 클래스의 인스턴스들을 구분하는 타입으로 사용가능
    * 직렬화를 예로 들면 인터페이스를 통해 컴파일 타임에 직렬화 가능성을 체크라고 에러 검출가능
  2. 적용 대상을 더 정밀하게 지정가능
    * 적용 대상을 ElementType.TYPE으로 선언한 애너테이션은 모든 타입에 적용가능
    * 즉 부착할 수 잇는 타입을 더 세밀하게 제한하지 못함

## References

* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=)