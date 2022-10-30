# 스프링 데이터 JPA

* 데이터 접근 계층(Data Access Layer)에서는 CRUD로 부르는 유사한 등록, 수정, 삭제, 조회 코드 개발이 반복됨
* JPA에서도 동일함
* 제네릭과 상속을 적정히 사용해서 공통 부분을 처리하는 부모 클래스를 만들면 됨 (GenericDAO)
* 하지만 이 방법은 공통 기능을 구현한 부모 클래스에 너무 종속되고 구현 클래스 상속이 가지는 단점에 노출됨

## 스프링 데이터 JPA 소개

## 공통 인터페이스 기능

## 쿼리 메소드 기능

* 스프링 데이터 JPA가 제공하는 쿼리 메소드 기능은 크게 3가지가 존재
* 메소드 이름으로 쿼리 생성
  * `findByUsername`
* 메소드 이름으로 JPA NamedQuery 호출
  * 스프링 데이터 JPA는 선언한 "도메인 클래스 + . + 메소드 이름으로 Named 쿼리를 찾아서 실행
  * Named 쿼리가 없으면 메소드 이름으로 쿼리 생성 전략 사용

```java
@Entity
@NameQuery(name="Member.findByUsername", query="select m from Member m where m.username = :username") // 또는 XML로 정의가능
public class Member {
  ...
}
em.createNamedQuery("Member.findByUsername", Member.class).setParameter(..).getResultList()

// 스프링 데이터 JPA

public interface MemberRepository extends JpaRepository<Member, Long> {
  List<Member> findByUsername(@Param("username") String username);
}
```

* `@Query` 어노테이션을 사용해서 쿼리 직접 정의

```java
// JPQL
@Query("select m from Member m where m.username = :username")

// native
@Query(value = "select * from Member where username = :username", nativeQuery = true)
```

* 벌크 쿼리 실행후 영속성 컨텍스트를 초기화 하고 싶으면 다음과 같이 수행
```java
@Modifying(clearAutomatically = true)
```

* 힌트
* 락

## 명세

* 도메인 주도 설계는 명세(specification)라는 개념을 소개하는데, 스프링 데이터 JPA는 JPA Criteria로 이 개념을 사용할 수 있도록 지원
  * 명세를 이해하기 위한 핵심단어는 술어(predicate)인데 참, 거직으로 평가
  * 술어는 AND, OR로 조합가능
* Specification은 컴포지트 패턴으로 구성되어 있어 여러 Specification을 조합 가능
* `JpaSpecificationExcutor`를 상속해서 사용

## 사용자 정의 리포지토리 구현

* 스프링 데이터 JPA로 리포지토리를 개발하면 인터페이스만 정의하고 구현체는 직접 개발하지 않음
* 다양한 이유로 직접 구현해야 할 경우도 존재
* 이 경우 공통 인터페이스가 제공하는 기능까지 모두 구현해야함
* 스프링 데이터 JPA는 이런 문제를 우회해서 필요한 메소드만 구현할 수 있는 방법을 제공

```java
public interface MemberRepositoryCustom {
  ...
}

// 클래스명이 인터페이스이름 + Impl로 정의하면 사용자 지정 클래스로 인식
public class MemberRepositoryImpl implements MemberRepositoryCustom {
  ...
}

public interface MemberRepository extends JpaRepository<>, MemberRepositoryCustom {
  ...
}
```

## 스프링 데이터 JPA와 QueryDSL 통합

* 


## References

* [자바 ORM 표준 JPA 프로그래밍](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788960777330&orderClick=LAG&Kc=)