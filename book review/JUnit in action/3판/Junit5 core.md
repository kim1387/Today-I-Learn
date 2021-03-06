# Junit5 core

### 단위 프레임워크의 이해

- 각 단위 테스트는 다른 모든 단위 테스트와 독립적으로 실행되어야 한다.
- 프레임워크는 테스트 별로 오류를 감지하고 보고해야 한다.
- 실행할 단위 테스트를 쉽게 정의할 수 있어야 한다.

- 프레임워크는 유용한 테스트를 작성하는 데 도움이 되어야 한다.
- 프레임워크는 시간이 지나도 가치를 유지하는 테스트를 만드는 데 도움이 되어야 한다.
- 프레임워크는 코드를 재사용하여 테스트 작성 비용을 낮추는 데 도움이 되어야 한다

### JUnit 5로 마이그레이션해야하는 이유

Java 8부터 도입된 프로그래밍 기능을 사용한다.

모듈식으로 계층적으로 테스트를 빌드할 수 있다.

버전 5부터 아키텍처는 더 이상 monolithic하지 않다.

는 4버전과의 차이: `text`*XYZ* 패턴 과 일치하도록 모든 테스트 방법에 대한 명명 규칙을 사용

→어노테이션이 도입 되었다.  

## **Core annotations**

JUnit 4.x까지의 경우처럼 더 이상 테스트 클래스를 public으로 할 필요가 없다.

- *A test method는* 다음과 같은 어노테이션을 달고 있는 instance method이다. `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory`, or `@TestTemplate`.
- *A life cycle method* is a method that is annotated with `@BeforeAll`, `@AfterAll`, `@BeforeEach`, or `@AfterEach`.
- test method는 abstart일 수 없고 void형이어야 한다.

---

static 키워드를 가지지 않는 메소드는 인스턴스 메소드(instance method)라고 합니다.

## Junit 5 예제

### 사용된 버전

juit version 5.6

junit4과 전체적으로 변경된 사항이 많은 듯 하다..

### Junit LifeCycle method

- 예제코드는 제외

    
- ① `@BeforeAll` 모든 테스트 전에 한 번 실행된다.
- ② `@AfterAll`  모든 테스트 후에 한 번만 실행됩니다.
    
    ①, ② method는 전체 테스트 class에 `@TestInstance(Lifecycle.PER_CLASS)`.이 지정되지 않는 한 static이어야 합니다 
    
- ③ `@BeforeEach`이 붙은 method는 각 테스트 전에 실행된다.
- ④ `@AfterEach` 각 테스트 후에 실행된다.
- ⑤ `@Test`는 독립적으로 실행됩니다.
- 이 테스트 클래스를 cli에서 실행하는 방법 `mvn -Dtest=SUTTest.java clean install`

### **@DisplayName**

test 클래스와 method에 사용할 수 있으며 test에 대한 설명을 명시 할 수 있음

: 특수 문자와 이모티콘도 사용 가능

- 예제코드는 제외
    
- ① 전체 클래스에 적용된 표시명을 보여줍니다.
- ② 일반 텍스트 표시명 적용
- ③ 이모티콘이 포함된 표시 이름을 사용합니다
- cli 명령어: mvn "-Dtest=DisplayNameTest.java" clean install

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a7c3e1f2-cfff-42b2-98bc-b4376432de66/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2847c7bc-19e4-483e-adc3-91e77edabbd7/Untitled.png)

### @Disabled

- 예제 소스
    
- 예제코드는 제외

    
- 클래스 전체와 메서드에 둘 다 사용 가능
- 이 코드는 비활성화된 두 가지 테스트를 제공합니다.
- ① 에 주어진 이유 없이 테스트 중 하나가 비활성화 한다 .
- **②** 다른 프로그래머가 이해할 수 있는 이유 때문에 다른 테스트는 비활성화 한다. --**권장**

### @Nest Test

중첩 테스트

inner class 에 다른 클래스의 멤버인 클래스를 사용이기 때문에 outer class는 모든 private 변수에 접근이 가능하다. 일반적으로 사용하는 사례는 두 클래스가 밀접하게 연관이 있을 때 사용한다.


- 예제코드는 제외
    

① 메인 테스트는 `NestedTestsTest` 

`NestedTestsTest`모든 중첩 테스트에 사용할 고객의 이름과 성을 정의한다. 

② Nest Test는 Builder 패턴⑤의 도움으로 개체 ④`BuilderTest` 의 구성을 확인한다.

 필드의 값이 똑같은지 ⑥에서 확인된다.

### @Tag

- 예제코드는 제외

    

junit4에 카테고리와 비슷한 기능

나중에 tag를 활용하여 검색과 필터링 가능

사용 사례:  비즈니스 logic와 효과적으로 테스트하는 항목을 기반으로 테스트를 몇 가지 범주로 그룹화 해서 현재 요구 사항에 따라 일부 테스트만 실행하거나 범주 간에 대체하도록 결정한다.

| assert method | 기능 |
| --- | --- |
| assertAll | 오버로드된 method로 제공된 실행파일 중 어느 것도 예외를 throw하지 않는다고 주장한다. |
| assertArrayEquals | 예상 배열과 실제 배열이 같다고 주장합니다. |
| assertEquals | 예상 값과 실제 값이 동일하다고 주장합니다. |
| assertX(...,String,message) | 어설션이 실패할 경우 제공된 메시지를 테스트 프레임워크에 전달하는 어설션입니다. |
| assertAll(...,String,message) | 어설션이 실패할 경우 제공된 메시지를 테스트 프레임워크에 전달하는 어설션입니다. |
- junit5는 오버로드된 method를 많이 제공한다. 많은 junit4의 매서드들이 포함되며 java 8 lamda 기능을 추가할 수 있는 몇 가지가 추가되었다.
- 모든 Junit jupiter method(org.junit.jupiter.api.Assertions에 속하는)는 static method이다.
- Hamcrest와 함께 작동하는 assertThat() method는 제거 되었다.

→ 이러한 경우 대안 책 Hamcrest를 오버라이드한 `MatcherAssert.assertThat()`를 사용하는 것이다.

---

*Hamcrest* 는 JUnit에서 소프트웨어 테스트 작성을 지원하는 프레임워크니다. 사용자 정의된 어설션 매처( *Hamcrest* 는 matcher의 anagram) 생성을 지원하여 일치 규칙을 선언적으로 정의할 수 있다.

람다에서는 기능을 method의 인자로 취급하고 코드를 데이터로 취급한다.

람다식을 사용했을 때의 장점은 메세지 생성이 지연되어 속도가 향상되기 때문이다.

=? 람다는 런타임 시에 타입 결정되기 때문??


--이미지나 예제 코드는 노션 참고--
