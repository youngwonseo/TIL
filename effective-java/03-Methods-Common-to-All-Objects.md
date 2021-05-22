# 모든 객체의 공통 메서드

**모든 객체의 공통 메서드**에서는 Object의 final이 아닌 메서드(equals, hashCode, toString, clone, finalize)의 재정의(overriding)시 지켜야 하는 규약을 설명합니다.

## 아이템 10. equals는 일반 규약을 지켜 재정의하라

* equals 객체를 비교하는 함수로 재정의하기 쉬워보이지만 곳곳에 위험요소가 존재하므로 재정의 하지 않는것이 최전임
* 재정의 하지 않으면 오직 자신과만 동일하다고 판단

### 재정의 하지 않는것이 최선인 상황

* 각 인스턴스가 본질적으로 고유, 여기에는 값을 표현하는 것이아닌 동작하는 객체를 표현하는 인스턴스가 해당됨
* 인스턴스의 논리적 동치성(logical equality)를 검사할일이 없음
* 상위 클래스에서 재정의한 equals로 하위클래스가 커버됨
* 클래스가 private이나 package-private 인 경우, equals이 호출될 일이 없음

### 재정의 해야할 상황

* 객체의 식별성이 논리적 동치성으로 판단될 경우

### equals메서드의 동치관계는 다음을 만족함

* 반사성 - x.equals(x)는 true
* 대칭성 - x.equals(y)가 true면 y.equals(x)도 true
* 추이성 - x.equals(y)가 true, y.equals(z)가 true 면 x.euqlas(z)도 true
* 일관성 - x.equals(y)가 true면 항상 true만 반환
* null-아님 - x.equals(null)은 false

## 아이템 11. equals를 재정의하려거든 hashCode도 재정의하라

## 아이템 12. toString을 항상 재정의하라

## 아이템 13. clone 재정의는 주의해서 진행하라

## 아이템 14. Comparable을 구현할지 고려하라

* compareTo는 Comparable 인터페이스의 유일한 메서드
* 즉 다른 아이템들과 달리 Object의 메서드가 아님

### equals와의 차이점

* 동치성 비교와 함께 순서까지 비교가능
* 예로들어 Arrays.sort를 통해 인스턴스의 정렬이 가능

```java
Arrays.sort(a);
```

* 제네릭함
* 즉 Comparable을 활용하는 **수많은 제네릭 알고리즘과 컬렉션 관련 기능을 사용가능** (위 sort도 포함)

## References

* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=)