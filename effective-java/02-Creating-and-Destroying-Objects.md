# 생성과 파괴

**생성과 파괴**에서는 객체를 만들때와 만들지 말아야할때, 올바른 객체 생성 방법, 불필요한 생성을 피하는 방법등을 숙지합니다.

## 아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라
* 생성자를 사용한 클래스 객체(인스턴스)화가 아닌 생성된 객체를 반환하는 정적 메서드를 정의하라

##### 정적 팩터리 메서드의 장점
* 이름을 가질 수 있슴, 가독성 있고 직관적인 코드를 작성가능
* 재사용 가능한 객체를 새로 생성하는 일을 방지, 다자인 패턴중 플라이웨이트 패턴(Flyweight pattern)이 이와 비슷
* 해당 객체의 하위타입을 생성 및 반환 가능
* 입력 매개변수에 따라 반환되는 객체를 결정 가능
* 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됨

##### 정적 팩터리 메서드의 단점
* public 생성자가 없으므로 하위 클래스를 만들수 없음
* 프로그래머 입장에서 public 생성자보다 찾기가 힘듬

##### 흔히 사용되는 정적 팩터리 메서드 이름
* from
* of
* valueOf
* instance, getInstance
* create, newInstance
* getType
* newType
* type

## 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라
* 생성시 매개변수가 많아지면 가독성이 떨어짐
* 이때 빌더를 사용해 객체를 생성해 가독성 및 명확한 코드를 작성가능

```java
// 생성자, 각 매개변수의 의미를 알 수 없다
NutritionFacts nuutritionFacts = new NutritionFacts(240, 8, 100, 0, 35, 26);

// 정적 펙터리 매서드, 각 매개변수의 의미를 알 수 없다
NutritionFacts nuutritionFacts = NutritionFacts.newInstance(240, 8, 100, 0, 35, 26);

// 빌더, 객체 생성시 보다 명확한 코드를 제공한다
NutritionFacts nuutritionFacts = NutritionFacts
                                  .builder()
                                  .servingSize(240)
                                  .servings(8)
                                  .calories(100)
                                  .fat(0)
                                  .sodium(35)
                                  .carbohydrate(25)
                                  .build();
```
* 자바에서는 대표적으로 lombok을 사용할 수 있음
* 직접 구현에 대한 코드는 구글링을 통해 쉽게 얻을 수 있음 

## 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라
* 싱글턴(singleton)이란 오직하나의 인스턴스만 생성할 수 있는 클래스를 의미
* 싱글턴은 싱글턴을 사용하는 클라이언트를 테스트하기 어렵게 만듬(mock 인스턴스를 생성할 수 없기 때문에)

##### 싱글턴을 제공하는 3가지 방법
* 싱글턴은 생성자를 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 public static을 제공
* 정적 팩터리 메서드를 제공
* 원소가 하나인 열거형 타입을 선언 - 직렬화 상황이나 리플렉션 공격에도 제2의 인스턴스가 생성되는 것을 완벽히 막아줌, 하지만 Enum외의 클래스를 상속해야할 경우 사용할 수 없음 

## 아이템 4. 인스턴스화를 막으려거든 private생성자를 사용하라
* 정적 메서드나 정적 맴버만 가진 클래스들이 존재함, 예로들어 util에 대한 메서드의 클래스
* 자바에는 java.lang.Math, java.util.Arrays 클래스, java.util.Collections 인터페이스 등이 이에 해당
* 이처럼 클래스를 인스턴스화 하지 않을 경우 private 생성자를 만들어 생성되는 것을 방지해야함
* 추상 클래스는 인스턴스화 가능 - 상속해서 인스턴스화 가능
* private 생성자는 상속을 불가능하게 하는 기능도 포함

## 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
* 명시적으로 할당되어 있는 클래스의 맴버가 필요에 따라 변경이 필요할 경우 외부에서 주입하라
* 즉 final 맴버가 존재할 경우 명시적 초기화가 아닌 생성자를 통해 외부에서 주입하라, 보다 유연한 동작을 보장한다(스프링의 의존성 주입을 생각해 보라)

## 아이템 6. 불필요한 객체 생성을 피하라
* 같은 기능, 내용을 가진 객체는 매번 생성하는 것이 아니라 재사용하는 것이 좋음
* 대표적인 예로 자바에서 문자열은 리터럴을 가지므로 같은 문자열을 재생성해서는 안됨 (메모리낭비)
```java
// 같은 문자열 리터럴인 effective-java를 매번 불필요하게 생성
String s = new String("effective-java");
// 개선된 버전 - JVM은 같은 문자열 리터럴을 사용하는 코드는 같은 객체를 재상요함이 보장됨
String s = "effective-java";
String s = String.valueOf("effective-java");
```
* 정적 팩터리 메서드(아이템1)나 valueOf 등을 사용해 불필요한 객체 생성을 피할 수 있음
* 기본 자료형과 클래스형에 대한 상호 변환 기능인 오토박싱(auto boxing)은 무분별한 객체 생성의 원인이 될수 있음 (무조건적인 사용 금지가 아닌 의도치 않은 오토박싱을 인지해야함)
```java
// long이 아닌 Long
Long sum = 0L;
for (long i = 0; i<= Integer.MAX_VALUE; i++){
  // Integer.MAX_VALUE개의 Long 객체가 생성됨
  sum += i;
}
```

## 아이템 7. 다 쓴 객체 참조를 해제하라
* C,C++와 다르게 자바는 가비지 컬렉터에 의해 메모리가 관리됨, 즉 사용후 따로 해제(free, delete)등이 필요 없음
* 대신 사용을 완료한 메모리에 대해서는 GC에 의해 메모리가 해제될 수 있도록 참조하고 있는 변수가 없어야함
* 사용을 완료한 변수에 null을 대입해 해당 메모리가 GC에 의해 정리되게 만들 필요가 있음
* 그렇다고 해서 무조건 다사용할때마다 null을 대입하는 것이 아닌 예외적인 경우일때만 함
* 해당 예에는 자기 메모리를 직접관리하는 collection형 클래스가 해당
* 캐시를 사용할 때에도 유효기간이 없는경우 null 대입을 통해 할당을 해제해야함

## 아이템 8. finalizer와 cleaner 사용을 피하라
* 자바는 객체에 대한 두 가지 소멸자인 finalizer와 cleaner를 제공
* finalizer는 예측할 수 없고 상황에 따라 위험할 수 있어 일반적으로 불필요함
* cleaner은 finalizerㅂ다 덜 위험하지만, 여전히 예측할 수 없고 느리고 불필요함

## 아이템 9. try-finally보다는 try-with-resources를 사용하라
* close 메서드를 호출해 직접 닫아(메모리에서 해제)하는 자원 사용시 try-with-resources를 사용하라
* try절 안에서 1차 예외, finally안에서 2차 예외 발생가능
* try-finally에서는 마지막에 발생한 예외를 보여줌, 하지만 우리의 관심사는 첫 번째 발생한 예외!
* try-with-resources에서 여러개의 예외 발생시 1차 예외를 보여주고 나머지 예외들도 숨김(suppressed)으로 보여지게 됨
* try-with-resources를 사용하기 위한 자원은 **AutoCloseable** 인터페이스를 구현해야 함

```java
// try-finally 예
// close에서 예외가 발생할 수 있음
static String firstLineOfFile(String path) throws IOException {
  BufferedReader br = new BufferedReader(new FileReader(path));
  try {
    return br.readLine();
  } finally {
    br.close();
  }
}

// try-with-resources 예
static String firstLineOfFile(String path) throws IOException {
  try(BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readLine();
  }
}
```

# References
* [이펙티브 자바 3/e](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEA&Kc=){: target="_blank"}