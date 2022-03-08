# spring security 인증 기능(1) 로그인 로그아웃 rememberme filter 등

목차

# 인증 API

## 사용자 정의 보안 기능 구현

### 초기 세팅- 아무런 설정을 하지 않았을 경우

1. 사용자 계정 1개 제공
2. 별도의 권한에 따른 인가 설정 없음
3. 별도의 보안 옵션 없음 but 최소한의 기능만 가지고 있음

→ 별도의 사용자 설정이 필요함 -  `WebSecurityConfigurerAdpater`를 상속 받아 설정해야함

### WebSecurityConfigurerAdpater

WebSecurityConfigurerAdpater에는 세부정인 보안 기능을 설정할 수 있는 기능들이 존재한다.

- **Authentication ( 인증 ) 기능**
    - 로그인, 로그아웃, CSRF, 기초적인 http 설정, Session 관리,
    - 예외 처리, 필터 추가, rememberMe 기능
- **Authorization ( 인가 / 권한 부여 ) 기능**
    - http.authorizeRequests()
    - 권한 관리, 인가 설정

```java
@Configuration // 설정 컨포넌트임
@EnableWebSecurity // 웹 보안 활성화
//@WebSecurityConfiguration,OAuth2ImportSelector 등 여러 보안 설정들이 세팅된다. 
public class SecurityConfig extends WebSecurityConfigurerAdapter { //

@Override // 인증 인가 설정하는 메서드
protected void configure(HttpSecurity http) throws Exception { 
	http
		.authorizeRequests()			
		.anyRequest().authenticated()		
	.and()
		.formLogin(); 			
}
```

spring security가 setting이 되어 있으면 spring security가 초기화 되면서 웹 보안 기능을 활성화 한다. (디폴트 설정)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/885c1d77-9a9d-44c9-816e-3cb2ec7a9061/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7474c97c-3353-4e69-aa74-917de12e2e2e/Untitled.png)

> spring security WebSecurityConfigurerAdapter 코드 중 일부 많은 설정들을 초기화한다.
> 

configure 함수로 가보면

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9f51e029-7a93-48d5-b504-ff049944e488/Untitled.png)

추가적인 인증 방식 설정을 하고 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adb269eb-40da-44fc-8822-1d37858e2109/Untitled.png)

이러한 기본적인 설정 때문에 의존성만 추가하여도 인증 로직이 추가가 된다.

## 인증 API

### login form 활성화

1. 사용자가 서버에 api 요청
    - 인증되지 않은 요청이면 로그인 페이지로 redirect
    - 인증이 되면 정상 요청
    
2.  로그인 요청 
    - Post {id:””,pw:””}
    - 성공하면 session 및 인증 토큰 생성/저장
3. 세션에 저장된 인증 토큰으로 접근 및 인증 유지

 

### HTTP basic 인증

- 자체적인 인증 관련 기능을 제공하며 HTTP 표준에 정의된 가장 단순한 인증 기법
- 간단한 설정과 stateless가 장점이다. - Session Cookie(JSessionId)가 사용하지 않는다.
- 보호 자원 접근 시 서버가 클라이언트에게 401 Unauthiorized 응답과 함께 WWW-Authenicate header를 사용하여 인증요구를 보낸다.
- ID:PASSWORED 값을 Base64를 통한 인코딩한 문자열을 인증 헤더에 추가해 서버에게 리소스 요청을 한다.
- Base64로 인코딩되어 있어 보안에 취약 SSL이나 TLS가 필요하다.

설정 방법

```java
protected void configure(HttpSecurity http) throws Exception {
	http.httpBasic();
}
```

### BasicAuthenticationFilter

**인증 실패**

1. 클라이언트가 요청을 보낸다. 
2. `BasicAuthenticationFilter`를 만난다. 
    
    filter 내부 코드 
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/505a37b0-2f2d-4a9a-8591-f7821e8287f0/Untitled.png)
    
    a. filter에서 `authenticationConverter` convert 함수를 만난다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1de78150-145a-4387-91d4-18ef97f706c9/Untitled.png)
    
    체크 사항
    
    1. header
    2. 인증 토큰이 Basic으로 시작하지 않을 때
    
    체크 사항 위반 시 인증 실패 →  401 인증되지 않음 상태 코드 반환
    

**인증 성공**

1. base64.encode(id:password)로 암호화 → Authorization 헤더: Basic SWsdfEsafOBd
    
    *id: password 인코딩 시 나오는 값은 SWsdfEsafOBd이라고 가정
    
2. 다시 `BasicAuthenticationFilter`를 만난다. 
3.  체크 사항 확인
    1. header
    2. 인증 토큰이 Basic으로 시작하지 않을 때
    
    체크 사항 확인 완료!
    
4. `authenticationManager.authenticate` 함수를 통해 인증함
5. securityContext를 만들고 인증 토큰을 추가 해서 사용자 인증 완료
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/43c70799-4fb6-4dbf-9a03-30796e355453/Untitled.png)
    
6. 내부 로직 처리
7. 200 OK 상태 코드 반환

### UernamePasswordAuthenticationFilter - LoginForm 인증

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c4b887a6-c42b-4c34-a1f2-191e285d3bbe/Untitled.png)

***인증 처리를 하기 전 필터의 역할***

1. 요청 url이 `/login`과 mapping 되는지 확인한다. 아니라면 다음 필터로 넘어간다.
    
    로그인 할 때만 사용되는 필터이기 때문 설정으로 mapping url를 변경할 수 있다.
    
2. 사용자가 입력한 id와 비밀번호로  Authentication 객체를 만들고 AuthenticationManager에게 넘겨 준다.

***인증 처리 과정***

1. AuthenticationManager가 AuthenticationProvider 객체 중 하나에게 위임한다.
2. AuthenticationProvider가 실제로 인증을 처리하는 객체이다. 실패한다면 예외를 발생시키고
    
    예외는 필터에게 전달하여 필터가 후속 작업을 처리한다.
    
3. 인증에 성공하면  Authentication 객체에 사용자의 권한( ex)관리자, 점주, 손님)도 함께 저장하여 AuthenticationManager에게 전달한다.
4. AuthenticationManager가 인증 객체를 반환한다.

***인증 처리 후 필터에서***

1. SecurityContext에 Authentication를  저장한다.
2. successHandler를 처리한다.

[실제 작동 원리]

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8d06531b-5503-4a4c-9858-e10d93524d5e/Untitled.png)

### logoutFilter

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3a434ac4-e270-40ec-ba52-e0e23ff61aa6/Untitled.png)

1. url이 `“/logout”` 인지 확인해 본다.
2. SecurityContext에 Authentication 객체를 요청한다.
3. logoutHandler 통해 세션을 만료, 쿠키 삭제, SecurityContextholder의 내용을 초기화 한다.
4. logoutFilter에서 로그인 페이지로 rediret한다.

[실제 작동 방식]

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e1b0828-f9dd-4c5d-b2a8-8869820d77b6/Untitled.png)

### RememberMeAuthentificationFilter RememberMe 인증

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f9fa1e1e-5b5d-4cdb-80b6-6c88001278e5/Untitled.png)

- 이 필터의 작동 조건
    1. Authentification null
    2. remember-me 쿠키 존재
1. 클라이언트가 remember-me 쿠키와 함께 요청을 보낸다.
2. RememberMeAuthentificationFilter 에서 Authentification 객체가 null이 아니면 동작하지 않는다.
    
    이미 로그인이 된 상태이기 때문이다.
    
3. Authentification 객체가 없으므로 remember-me 쿠키 값을 이용하여 사용자 인증을 시도한다.
4. RememberMeService는 interface
    - TokenBasedRememberService 구현체는 인메모리 토큰을 저장하고 관리
    - PersistentTokenBasedRememberService 구현체는 DB에 토큰을 저장하고 관리
5. remberMeService가 사용자 토큰 쿠키를 추출하고
6. Decode를 통해 존재하는 토큰인지 확인하고 User 계정이 존재하는지 확인하고 새로운 Authentication 객체를 생성하고 AuthenticationManager로 전달하여 인증 처리를 한다.
    
    *UernamePasswordAuthenticationFilter 참조
    

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e2c000a8-d03b-47b0-be57-1d5198290299/Untitled.png)

### AnonymousAuthenticationFilter - 익명 사용자 인증 처리 필터

- 필터 발동 조건
    
    인증되지 않은 사용자
    
1. 인증객체가 null이라면 AnonymousAuthenticationToken으로 인증 객체를 만든다.
2. SecurityContext에 AnonymousAuthenticationToken으로 저장하여 익명 사용자의 요청을 
    
    처리한다.
    

특징

- 익명 사용자를 NULL로 관리하지 않는다.
- 화면 인증 여부를 구현할 때 isAnonymous(), isAuthenticated()로 구분해서 사용한다.
- 인증 객체를 세션에 저장하지 않는다.


[출처] 인프런 정수원님 spring security 강의