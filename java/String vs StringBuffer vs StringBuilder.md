# String vs StringBuilder vs StringBuffer

목차

## 자바에서 문자열 다루는 클래스

1. String
2. StringBuffer
3. StringBuilder

### 차이점에 대해 왜 알아야 할까?

연산이 적을 때는 어떤 것을 선택하더라도 큰 영향이 없다.

하지만 multi-thread, race condition의 상황이 자주 발생하거나 연산 회수가 많을 때는 각각의 특징을 고려하는 것이 성능과 정확도에  큰 영향을 준다.

## ****String  vs  StringBuffer/StringBuilder****

String: 불변(immutable)함

StringBuffer/StringBuilder: mutable 함(수정이 가능 함)

### String은 불변하다

```java
String str = "hello";   // String str = new String("hello");
str = str + " world";  // [ hello world ]
```

- **해당 연산은 문자열이 합쳐진 것이 아니라 GC에 의해서 새로 만들어진 인스턴스다!**
    
    "hello" 값을 가지고 있던 String 클래스의 참조변수 str이 가리키는 곳에 저장된 "hello"에 "world" 문자열을 더해 "hello world"로 변경한 것으로 착각할 수 있습니다.
    
    하지만 기존에 "hello" 값이 들어가있던 String 클래스의 참조변수 str이 "hello world"라는 값을 가지고 있는 **새로운 메모리영역을 가리키게 변경**되고 **처음 선언했던 "hello"로 값이 할당되어 있던 메모리 영역은 Garbage로 남아있다가 GC(garbage collection)에 의해 사라지게 되는 것** 입니다. String 클래스는 불변하기 때문에 문자열을 수정하는 시점에 새로운 **String 인스턴스가 생성된 것**이지요.
    
    [https://t1.daumcdn.net/cfile/tistory/99948B355E2F13350F](https://t1.daumcdn.net/cfile/tistory/99948B355E2F13350F)
    

### String을 쓰면 좋은 곳 → 값이 자주 바뀌지 않는 문자열에 사용!

위와 같이 String은 불변성을 가지기 때문에 **변하지 않는 문자열을 자주 읽어들이는 경우 String을 사용**해 주시면 좋은 성능을 기대할 수 있습니다. 

> 그러나 **문자열 추가,수정,삭제 등의 연산이 빈번하게 발생**하는 알고리즘에 String 클래스를 사용하면 **힙 메모리(Heap)에 많은 임시 가비지(Garbage)가 생성**되어 힙메모리가 부족으로 어플리케이션 성능에 치명적인 영향을 끼치게 됩니다.
> 

 

### **StringBuffer** / **StringBuilder** 쓰면 좋은 곳 → 값이 자주 create, update, delete 되는 문자열

이를 해결하기 위해 Java에서는 **가변(mutable)성**을 가지는 **StringBuffer** / **StringBuilder** 클래스를 도입했습니다.

String 과는 반대로 StringBuffer/StringBuilder 는 가변성 가지기 때문에 .append() .delete() 등의 API를 이용하여 **동일 객체내에서 문자열을 변경**하는 것이 가능합니다. 따라서 **문자열의 추가,수정,삭제가 빈번하게 발생할 경우**라면 String 클래스가 아닌 **StringBuffer/StringBuilder를 사용**하셔야 합니다.

```java
StringBuffer sb= new StringBuffer("hello");
sb.append(" world");
```

[https://t1.daumcdn.net/cfile/tistory/9923A9505E2F133608](https://t1.daumcdn.net/cfile/tistory/9923A9505E2F133608)

## ****StringBuffer  vs  StringBuilder****

공통점: 똑같은 추가 생성 삭제 api를 가지고 있음

차이점: **동기화의 유무**

### **StringBuffer**는 **thread-safe**!  - string 또한 그러함(imutable 하기 때문)

**멀티쓰레드 환경에서 안전하다는 점(thread-safe)** 입니다.  참고로 **String**도 ****불변성을 가지기때문에 마찬가지로  **멀티쓰레드 환경에서의 안정성(thread-safe)**을 가지고 있습니다.

### **StringBuilder**는 **thread-unsafe!**

멀티쓰레드 환경에서 사용하는 것은 적합하지 않지만 동기화를 고려하지 않아서

 **단일쓰레드에서의 성능은 StringBuffer 보다 뛰어남**

# 결론

**String**                :  문자열 연산이 적고 멀티쓰레드 환경일 경우

**StringBuffer**    **** :  문자열 연산이 많고 멀티쓰레드 환경일 경우

**StringBuilder**   :  문자열 연산이 많고 단일쓰레드이거나 동기화를 고려하지 않아도 되는 경우

[https://t1.daumcdn.net/cfile/tistory/99BE23375E2F133722](https://t1.daumcdn.net/cfile/tistory/99BE23375E2F133722)

출처:

[https://ifuwanna.tistory.com/221](https://ifuwanna.tistory.com/221)

[IfUwanna IT]