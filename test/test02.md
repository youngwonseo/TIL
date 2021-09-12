# 스프링캠프 2019 [Track 2 Session 3] : 무엇을 테스트할 것인가? 어떻게 테스트할 것인가? (권용근)

[![]()](https://youtu.be/YdtknE_yPk4)

* 테스트로부터 얻을 수 있는 것
  * 안정감과 자신감
* 대상은?
  * 현재와 미래의 나
  * 현재와 미래의 동료

## 무엇을 테스트할 것인가?

* Method Call Tree

1. 구현과 설계
  * 구현은 언젠가 어떻게든 변할 수 있다
  * 구현 테스트와 함께 비즈니스 로직을 테스트해야함
  * 구현이 아닌 설계를 테스트해야함

2. 테스트 가능한 것. 불가능한 것

* 테스트 불가능한 메서드들이 테스트 가능한 메서드들을 오염시킴
* 다음과 같은 항목은 제어할 수 없음
  * Random, Shuffle, LocalDate.now()
  * 외부세계
    * HTTP
    * 외부 저장소


```java
IntStream.range(0, 3)
  .mapToObj(i -> collection.createNumbers()
  .flatMap(Collection::stream)
  .collect(Collectors.toList());
```

