# JUnit in Action 2판

### 책을 읽은 후 간단한 소감

junit in action 2판은 10년 전에 나왔지만 번역본이 있어서 먼저 다 읽고 3판을 읽어보려고 했으나

읽다 보니 내용이 많이 달라서 8장까지만 읽었고 3판으로 넘어가고자 한다. 어노테이션을 많이 사용하지도 않고 무엇보다도 junit3~4로 넘어온 기준으로 작성된 책이기 때문이다.

아직 표준이 ant라고 한다.. 10년 전 책인데도 불구하고 좋은 오래된 책이라는 느낌은 들지 않았다. 

각종 디자인 패턴과 ci cd하는 것은 자동화가 되었을 뿐 현재랑 다르지 않기 때문인 것 같았다.

처음에 기대한 내용은 Junit과 mockito 같은 라이브러리 사용법과 TDD였는데 디자인 패턴과 스프링의 기본 원리에 대해 나와서 좋았다.

단위 test를 작성 하게 되면 작은 단위 기준으로 독립적인 테스트를 요구하기 때문에 

단위 테스트를 하기 어려운 코드를 리팩토링 해야해서 다루는 것 같았다.

간단한 내용 요약

### junit assert 메서드

| assert method | 사용 목적 |
| --- | --- |
| assertArrayEquals(”message”,A,B) | 배열 A B가 같음을 확인 |
| assertEquals(”message”,A,B) | 객체 A B가 일치함을 확인한다. B 파라미터로 A의 equals() method를 호출한다. A.equals(B) |
| assertSame(”message”,A,B) | A B와 같은 객체임을 확인한다. 하나의 객체를 가리키는지 검사한다. ==연산자를 사용해서 비교 |
| assertTrue(”message”,A) | 조건 A가 참임을 확인한다. |
| assertNotNull(”message”,A) | 객체 A가 Null이 아님을 확인한다. |

assertEquals를 사용하기 위해서는 클래스에서 equals함수를 오버라이딩 해야 한다.

### Junit 핵심 객체

| Assert | 테스트 하려는 조건을 명시한다, assert 메서드는 조건이 만족 되면 실행되지 않고 아니면 예외를 throw한다. |
| --- | --- |
| Test | @Test 가 부여된 메서드로 하나의 테스트를 의미한다. |
| Test 클래스 | @Test 메서드를 포함된 method를 포함한 클래스이다. |
| Suite | 여러 test class를 하나로 묶는 수단을 제공한다. Suite를 묶어 Suite를 만들 수도 있다. |
| Runner | 러너는 테스트를 실행한다. |

## Controller Pattern

### Controller Component

Core Java EE 패턴에 따르면 컴포넌트

- Controller Test 전체 코드