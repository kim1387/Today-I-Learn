# 세션 관리 (동시 세션 제어, 세션 고정 보호, 세션 정책)

목차

# 세션 관리

## 동시 세션 제어 - 동시 접속 관리

최대 세션 허용 개수 초과 시  정책을 설정하는 방법

### 1. 이전 사용자 세션 만료

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/783604b2-cc21-41bb-be5c-f2ce65e6d587/Untitled.png)

새로운 로그인 요청이 들어왔을 경우 최대 세션 허용 개수 초과 시 이전에 접속해 있던 사용자를 만료 시킨다.

### 2. 현재 사용자 인증 실패

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da7503b1-1a5b-4dc3-9f62-3f194a99f32b/Untitled.png)

새로운 로그인 요청이 들어왔을 경우 최대 세션 허용 개수 초과 시 새로 접속한 사용자를 만료 시킨다.

### 3. 설정 방법

```java
protected void configure(HttpSecurity http) throws Exception {     
http.sessionManagement()
	 .maximumSessions(1)                 
      .maxSessionsPreventsLogin(true) 
      .invalidSessionUrl("/invalid")       
      .expiredUrl("/expired ")  	       
}
```

| maximumSessions()  | 최대 허용 가능 세션 수 , -1 : 무제한 로그인 세션 허용 |
| --- | --- |
| maxSessionsPreventsLogin(true)  | 동시 로그인 차단함,  false : 기존 세션 만료(default) |
| invalidSessionUrl | 세션이 유효하지 않을 때 이동 할 페이지 |
| expiredUrl | 세션이 만료된 경우 이동 할 페이지 |

maxSessionsPreventsLogin(true) 설정 시 `2. 현재 사용자 인증 실패` 로 설정

default는 `1. 이전 사용자 세션 만료`

## 세션 고정 보호

### 세션 고정 취약점 공격(Session Fixation Exploit)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a8e9e695-cf74-4779-8250-33a66ce05b97/Untitled.png)

1. 공격자가 서버에 접속해서 JSession 쿠키를 발급 받는다.
2. 발급받은 쿠키를 사용자의 세션쿠키에 심는다.
3. 사용자가 세션쿠키를 받은 채로 로그인을 시도하면 공격자의 쿠키 값으로 인증이 되기 때문에 로그인을 할 수 있다.

### 해결책

1. 세션 만료 시간 설정
2. 세션 id 재사용 금지 및 로그 아웃 시토큰 무효화

→ spring security에서는 default로 세션 재 사용을 막고 있다.

default로 설정되어 있어서 모르고 있었지만 정말 필요한 보안 정책을 spring security가 대신 해주고 있었던 것이다.

## 세션 정책

```java
protected void configure(HttpSecurity http) throws Exception {
	http.sessionManagement()
                .sessionFixation().changeSessionId() // 기본값
							    // none, migrateSession, newSession
}
```

none: 세션 고정 보호를 해제한다.

changeSessionId(default): 세션 고정 보호를 위해 접속할 때마다 sessionID를 변경한다.

newSession: 접속할 때마다 세션을 새로 만든다.

[출처] 인프런 정수원님 spring security 강의