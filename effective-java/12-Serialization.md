# 직렬화

* 객체 직렬화란 자바가 객체를 바이트 스트림으로 인코딩하고 그 바이트 스트림으로부터 객체를 재구성하는 메커니즘을 의미
* 이번장은 직렬화가 품고 있는 위험과 그 위험을 최소화하는 방법에 집중

## 아이템 85. 자바 직렬화의 대안을 찾으라

* 
* 

## 아이템 86. Serializable을 구현할지는 신중히 결정하라

* 
* 

## 아이템 87. 커스텀 직렬화 형태를 고려해보라

* 
* 

## 아이템 88. readObject 메서드는 방어적으로 작성하라

* 
* 

## 아이템 89. 인스턴스 수를 통제해야 한다면 readResolve보다는 열거 타입을 사용하라

* [아이템3]()에서 싱글턴 패턴이 바깥에서 생성자를 호출하지 못하게 막는 방식으로 인스턴스가 오직 하나만 생성된다는것을 보여줌
* 하지만 implements Serializable을 추가하는 순간 싱글턴이 아님
* 기본 직렬화를 사용하지 않거나(아이템 87) 명시적인 readObject를 제공하더라도(아이템88) 소용없음
* 어떤 readObject를 사용하던 이 클래스가 초기화 될 때 만들어진 인스턴스와는 별개인 인스턴스를 반환함
* readResolve를 사용하면 readObject가 만들어낸 인스턴스대신 다른것으로 대체가능, 즉 싱글턴을 유지가능
* 역직열화한 객체의 클래스가 readResolve를 적절히 정의해뒀다면, 역직렬화후 생성된 객체가 아닌 원하는 객체의 참조를 반환가능

```java
private Object readResolve() {
  return INSTANCE;
}
```

* 좀더 깊게 생각해보면 오직 하나의 인스턴스만 유지하므로 직렬화할때 객체의 맴버를 포함할 필요가 없음
  * 그러므로 모든 필드를 transient로 선언해야함
  * 그렇지 않으면 readResolve 메서드가 수행되기전에 역직렬화된 객체의 참조를 공격할 여지가 존재함
* 맴버 보호하기 위해 맴버를 transient로 선언하는 방법 보다는 원소 하나짜리 열거 타입으로 바꾸는 방법이 더 나은 선택임
* readResolve 메서드의 접근성은 매우 중요함
  * final 클래스에서라면 readResolve 메서드는 private

## 아이템 90. 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라

* 이전에서 언급한것과 같이 Serializable을 구현하면 언어의 정상 메커니즘인 생성자 이외의 방법으로 인스턴스를 생성할 수 있게 됨
  * 버그와 보안 문제가 발생할 가능성이 커진다는 것
* 이 위험을 크게 줄여주는 기법으로 직렬화 프록시 패턴(serialization proxy patter)이 존재
* 먼저 바깥 클래스의 논리적 상태를 정밀하게 표현하는 중첩 클래스를 설계해 private static으로 선언

```java
private static class SerializationProxy implements Serializable {
  private final Date start;
  private final Date end;

  SerializationProxy(Period p) {
    this.start = p.start;
    this.end = p.end;
  }

  private static final long serialVersionUID = 324234234234242L;
}
```

* 다음으로 바깥 클래스에 다음의 writeReplace 메서드를 추가
* 이 메서드가 자바의 직렬화 시스템이 바깥 클래스의 인스턴스 대신 SerializationProxy의 인스턴스를 반환하는 역할을 함
* 즉 직렬화가 이루어지기전 바깥 클래스의 인스턴스를 직렬화 프록시로 변환해줌

```java
private Object writeReplace() {
  return new SerializationProxy(this);
}
```

* 다음의 readObject 메서드를 바깥 클래스에 추가하면 불변식을 훼손하는 공격을 가볍게 막아냄

```java
private void readObject(ObjectInputStream stream) throws InvalidObjectException {
  throw new InvalidObjectException("프록시가 필요합니다.");
}
```

* 마지막으로 마깥 클래스와 논리적으로 동일한 인스턴스를 반환하는 readResolve 메서드를 SerializationProxy에 추가

## References

* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=)