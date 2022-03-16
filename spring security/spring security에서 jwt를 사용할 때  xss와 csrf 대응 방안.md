# spring security에서 jwt에서 xss와 csrf

## session을 활용할 때

spring security에서 session을 활용한 인증을 할 때는csrf라는 기능이 자동으로 활성화 되어서 csrf의 공격이 방어가 된다.

csrf 토큰을 발급해서 사용하기 때문

## JWT를 활용할 때

그렇다면 crsf 공격은 어떻게 막을 것이냐

사용자가 요청을 보낼 때 cookie를 통해서가 아닌 header를 통해 jwt 토큰을 전송한다.

→ 단점 header에 cookie 값을 저장하기 위해서는 http only 옵션이 해제 되야한다.

jwt 토큰을 활용할 때 csrf 토큰은 어떻게 할까??

csrf token을 활용한다.

## 클라이언트에서 주의할 점

### 토큰을 어디에 저장할 것인가? local storage vs cookie

local storage는 xss 공격에 취약

→cookie에 저장한다.

→cookie도 js로 접근 가능하지 않냐? → http only로 설정 하면 접근 불가

그렇다면 crsf 공격은 어떻게 막을 것이냐

사용자가 요청을 보낼 때 cookie를 통해서가 아닌 header를 통해 jwt 토큰을 전송한다.

→ 단점 header에 cookie 값을 저장하기 위해서는 http only 옵션이 해제 되야한다.

## 번외 http-only 옵션을 주더라도 cookie에 접근이 가능하다??

가정) 사용자와 같은 인터넷 망에 접근 할 수 있다!

페킷 스니핑을 통해 사용자의 요청을 훔쳐본다.

Jsession이나 jwt token값을 볼 수 있다.

### 대안

https로 만들고 spring security secure 옵션을 활성화 해서 

패킷 스니핑을 당하더라도 내부 값을 확인하지 못하도록 한다.

# [참고 자료]

[https://medium.com/@pranne1224/http-인증-authentication-을-위한-spring-security-jwt-jason-web-token-전략-5fb4a2ee2bbe](https://medium.com/@pranne1224/http-%EC%9D%B8%EC%A6%9D-authentication-%EC%9D%84-%EC%9C%84%ED%95%9C-spring-security-jwt-jason-web-token-%EC%A0%84%EB%9E%B5-5fb4a2ee2bbe)

[https://cjw-awdsd.tistory.com/48](https://cjw-awdsd.tistory.com/48)

[https://www.skyer9.pe.kr/wordpress/?p=2444](https://www.skyer9.pe.kr/wordpress/?p=2444)