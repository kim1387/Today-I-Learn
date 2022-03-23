# Authentication

목차

## Autentication 객체란?

사용자가 누구인지 증명하는 객체로 spring security에서 토큰으로 사용된다.

- **인증 시**: id와 password 정보를 담고 인증 검증을 위해 사용된다.
- **인증 후**: 최종 인증 결과 (user 객체, 권한 정보)를 담고  SecurityContext에 저장이 되어 전역적으로 참조가 가능해진다.
- 인증 객체 꺼내는 방법
    - Authentication authentication = SecurityContexHolder.getContext().getAuthentication()
    - SecurityContextHolder안에 context안에 있다.

## Autentication 구조

1) principal : 사용자 아이디(인증 전) 혹은 User 객체를 저장(인증 후)
2) credentials : 사용자 비밀번호 → 인증 후에는 회사 정책에 따라 보안을 위해 비밀번호를 지움
3) authorities : 인증된 사용자의 권한 목록
4) details : 인증 부가 정보
5) Authenticated : 인증 여부

## Authentication 검증 흐름

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9115bd3-06cf-40fc-b304-b949931e2f97/Untitled.png)

### **검증이 이뤄지는 필터**

`**UsernamePasswordAuthenticationFilter**`

### 인증 전

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a9b0f95b-937b-40f8-a7a4-c08681bfa5c5/Untitled.png)

principal: 사용자 id가 저장되어 있다.

Credentials: 비밀번호

### AuthenticationManager을 통해 인증이 이뤄지면

### 인증 후

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d8d7e7c1-8e30-4ff2-a125-64e1b2841f9e/Untitled.png)

principal: UserDetail이라는 유저 정보가 저장

Credentials: 인증을 위한 비밀번호 삭제

Authorities: 사용자의 권한

Authenticated: 인증되었으니 True로 변경