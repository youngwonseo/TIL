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

* equals를 재정의한 클래스 모두에서 hashCode도 재정의해야함
	* 그렇지 않으면 hashCode의 규약을 어기게되고 HashMap이나 HashSet과 같은 컬렉션의 원소로 사용할 때 문제를 일으킴
* 논리적으로 같은 객체는 같은 hashCode를 반환해야함

### 좋은 hashCode 작성 요령

1. int로 result를 선언한후 객체의 첫 번째 핵심 필드인 c로 초기화
2. 나머지 핵심 필드 f에 대해 다음과정 수행
	1. 해당 필드의 해시코드 c를 계산
		* 기본 타입이라면 Type.hashCode(f)
		* 참조 타입이라면 hashCode를 재귀적으로 호출, 계산이 복잡해 질것 같으면 표준형(canonical representation)을 만들어 hashCode를 호출
		* 배열이라면 핵심 원소 각각을 별도의 필드 처럼 처리
	2. 계산된 c로 result를 갱신 (result = 31 * result + c)

* hashCode를 구현했다면 동치인 인스턴스에 대해 같은 해시코드를 반환하는지 테스트 필요
* 파생 필드는 해시코드 계산에서 제외
* 해시 충돌이 더욱 적은 방법을 사용해야 한다면 구아바의 com.google.common.hash.Hashing을 참조
* 성능을 높인답시고 해시코드 계산시 핵심 필드를 생략하거나 하는 행위는 안됨
* hashCode가 반환하는 값의 생성 규칙을 API사용자에세 자세히 공표하지 말자
	* 그래야 클라이언트가 이 값에 크게 의존하지 않고 필요시 구현해서 사용함

## 아이템 12. toString을 항상 재정의하라

* Object의 기본 toString 메서드는 클래스_이름@16진수_해시코드 를 반환함
* toString의 일반 규약에 따르면 **간결하면서 사람이 읽기 쉬운 형태의 유익한 정보**를 반환해야함
* 모든 하위 클래스는 toString을 재정의해야함
* toString을 잘 구현한 클래스는 사용하기에 훨씬 즐겁고, 그 클래스를 사용한 시스템 디버깅이 쉬움
* toString은 그 객체가 가진 중요정보를 모두 반환하는게 좋음
* toString이 반환한 값에 포함된 정보를 API에서 제공해야함

## 아이템 13. clone 재정의는 주의해서 진행하라

* Cloneable은 복제해도 되는 클래스임을 명시하는 믹스인 인터페이스
* 그러나 의도한 목적을 제대로 이루지 못함
	* clone이 Object에 정의됨
	* 그마저도 protected
	* 그래서 Cloneable을 구현하는 것만으로 외부 객체에서 복사 불가능
* Cloneable은 메서드 하나 없는 인터페이스
	* 이 인터페이스는 Object의 protected 메서드인 clone의 동작 방식을 결정
	* 객체의 필드들을 하나하나 복사한 객체를 반환
	* 그렇지 않은 클래스에서 호출하면 CloneNotSupportedException을 던짐
* 실무에서 Cloneable을 구현한 클래스는 clone메서드를 public으로 제공하며, 사용자는 당연히 복제가 제대로 이뤄지리라 기대
	* 이 기대를 만족시키려면 그 클래스와 상위 모든 클래스는 복잡하고, 강제할 수 없고, 허술하게 기술된 프로토콜을 지켜야함
* clone 메서드는 생성자와 같은 효과를 냄
	* 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야함
* Cloneable 아키텍처는 가변 객체를 참조하는 필드는 final로 선언하라는 일반 용법과 충돌함
* Cloneable이 가지고 있는 문제사항을 볼때, 새로운 인터페이스를 만들때는 절대 Cloneable을 확장해서는 안됨, 클래스는 Cloneable을 구현하면 안됨
	* final클래스의 경우 Cloneable을 구현해도 크게 상관없지만 성능이슈를 고려해야함
	* 기본원칙은 **복제 기능은 생성자와 팩터리를 이용하는게 최고**라는 것
	* 단 배열은 이 규칙의 합당한 예외

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