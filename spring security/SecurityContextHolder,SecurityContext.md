# SecurityContextHolder,SecurityContext

## SecurityContext 역할

- Authentication 객체가 저장되는 보관소이다. 언제든지 Authentication 객체를 꺼내 쓸 수 있다.
- TreadLocal에 저장되어 있어 어디에서나 참조 가능함
- 인증이 완료되면 HttpSession에 저장되어 어플리케이션 전반에서 사용 가능하다.

---

*`SecurityContext`> `Authentication` > `User 정보` 저장

**`ThreadLocal`이란 Theard에 localStorage에 정보를 저장할 수 있는 저장소 get, set, remove를 통해 제어 가능함 → 잘못 사용하면 이전 사용자의 정보가 그대로 남아 있어서 `**심각한 장애**` 발생

## SecurityContextHolder

### SecurityContext 객체 저장 방식 옵션

`MODE_THREADLOCAL` : 스레드당 `SecurityContext` 객체를 할당, 기본값
`MODE_INHERITABLETHREADLOCAL` : 메인 thread와 자식 thread에 대해 동일한 `SecurityContext`를 유지
`MODE_GLOBAL` :  응용 프로그램에서 단 하나의 `SecurityContext`를 저장

### `SecurityContext` 기존 정보 초기화 방법

SecurityContextHolder.clearContext() : 

### Authentication 객체 가져오는 방법

`Authentication authentication = SecurityContextHolder.getContext().getAuthentication()`

# 큰 흐름

![image](https://user-images.githubusercontent.com/58874807/159684077-089e0d3a-a241-4191-82de-dd285a2f7820.png)


1. 사용자가 `로그인` 요청을 함
2. 서버가 요청을 받는 `thread`를 생성함  → thread는 `threadlocal`이라는 저장소가 할당됨
3. thread가 `인증` 처리를 함
    1. 인증이 실패한다면 `SecurityContextHolder.clearContext`로 `Authentication` 객체를 초기화
    2. 인증이 성공한다면
    - `securityContextHolder` 안에 `SecurityContext`안에  `Authentication` 객체를  저장한다.
    - `SecurityContextHolder`가 `ThreadLocal` 객체를 가지고 있고 `ThreadLocal`이 `SecurityContext`를 담고 있다.
        - `SecurityContextHolder` → `ThreadLocal [SecurityContext]`
        - `SecurityContext` → `Authentication` → `UserInfo(UserDetail)`
4. 전 단계를 성공했다면, `SecurityContext`를 `HttpSession`에 저장한다.
    - SessionId = .(`SPRING_SECURITY_CONTEXT`)