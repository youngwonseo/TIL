# 제네릭

## 아이템 26. 로 타입은 사용하지 말라

## 아이템 27. 비검사 경고를 제거하라
* 제내릭 사용시 다양한 종류의 경고를 출력하는데 가능한 경고를 줄일 수 있도록 코드를 작성해야함
* 모든 경고를 제거하면 타입 안정성이 보장됨, 런타임에 ClassCastException이 발생할 일이 없어짐
* 경고를 제거할 수는 없지만 타입이 안전하다고 확신할 있으면 @SuppressWarnings("unchecked") 에너테이션을 추가
* 타입이 안전하다고 확신이 없는상태에서 @SuppressWarnings("unchecked")를 사용하는 것은 전혀 도움이 안 됨
* 이때 @SuppressWarnings("unchecked")는 가능한 좁은 범위에 지정해야 함
* @SuppressWarnings("unchecked") 사용시 경고를 무시한 이유를 주석으로 남겨야 함

## 아이템 28. 배열보다는 리스트를 사용하라

## 아이템 29. 이왕이면 제네릭 타입으로 만들라

## 아이템 30. 이왕이면 제네릭 메서드로 만들라

## 아이템 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

## 아이템 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라

## 아이템 33. 타입 안전 이종 컨테이너를 고려하라

# References
* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=){: target="_blank"}