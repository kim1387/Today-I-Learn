# csrfFilter

## CSRF 공격 기법

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/883b537c-4c36-4d45-b765-9bcb80696f1e/Untitled.png)

1. 사용자가 쇼핑몰에 로그인 한다.
2. 공격자가 이메일이든 문자든 악의적인 url를 사용자에게 보낸다.
3. 사용자가 악의적인 링크를 열면 사용자가 쿠키로 저장하고 있는 session ID 값을  가지고 해당 링크에서 쇼핑몰에 요청을 보낸다.
4. 사용자의 정보 조회나 주문, 배송지 등록 등 공격자가 승인되지 않은 요청을 수행할 수 있다.

## 대응 방안 - csrf 토큰 이용

1. 모든 요청에 대해서 csrf 토큰을 요청한다.
2. 요청 시 전달되는 토큰 값과 서버에 저장 값을 비교하여 다르면 요청을 실패로 처리한다.
    
    →(403) 인증되지 않은 요청
    

## spring security를 이용하면 해결

spring security 의존성을 추가하게 되면 default로 설정된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/76e21287-c3d3-4cc2-bf74-c65b23491c5b/Untitled.png)

`http.csrf().disabled()` 로 비활성화 디폴트는 활성화

1. 모든 요청에 대해서 csrf 토큰을 요청한다.(Get 방식을 제외한)
2. 토큰 값이 맞지 않거나 null이면 403 반환

클라이언트 요청 시 header에 `X-CSRF-TOKEN`에 `(crsf의 토큰 값)`을 넣어서 요청을 보낸다.

## ETC

### Q: 세션 id는 클라이언트의 쿠키에 저장되는 반면 csrf토큰은 어디에 저장된다고 봐야 할까요?

A:기본적으로는 쿠키에 저장하는 것 같지는 않습니다.

CsrfFilter 내용을 보면

```java
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
      throws ServletException, IOException {
   request.setAttribute(HttpServletResponse.class.getName(), response);
   CsrfToken csrfToken = this.tokenRepository.loadToken(request);
   boolean missingToken = (csrfToken == null);
   if (missingToken) {
      csrfToken = this.tokenRepository.generateToken(request);
      this.tokenRepository.saveToken(csrfToken, request, response);
   }
   request.setAttribute(CsrfToken.class.getName(), csrfToken);
   request.setAttribute(csrfToken.getParameterName(), csrfToken);
   if (!this.requireCsrfProtectionMatcher.matches(request)) {
      if (this.logger.isTraceEnabled()) {
         this.logger.trace("Did not protect against CSRF since request did not match "
               + this.requireCsrfProtectionMatcher);
      }
      filterChain.doFilter(request, response);
      return;
   }
```

위의 내용중에

```java
request.setAttribute(CsrfToken.class.getName(), csrfToken);
request.setAttribute(csrfToken.getParameterName(), csrfToken);
```

를 보시면 request 객체에 담고 있습니다.

근데 tokenRepository 를 보니까 여러 구현체 중에서 CookieCsrfTokenRepository 가 있긴 합니다.

그리고 이 클래스에서는

```java
@Override
public void saveToken(CsrfToken token, HttpServletRequest request, HttpServletResponse response) {
   String tokenValue = (token != null) ? token.getToken() : "";
   Cookie cookie = new Cookie(this.cookieName, tokenValue);
   cookie.setSecure((this.secure != null) ? this.secure : request.isSecure());
   cookie.setPath(StringUtils.hasLength(this.cookiePath) ? this.cookiePath : this.getRequestContext(request));
   cookie.setMaxAge((token != null) ? this.cookieMaxAge : 0);
   cookie.setHttpOnly(this.cookieHttpOnly);
   if (StringUtils.hasLength(this.cookieDomain)) {
      cookie.setDomain(this.cookieDomain);
   }
   response.addCookie(cookie);
}
```

의 구문도 있습니다.

즉 쿠키에 담고 있습니다.

어떤 조건에 따라 쿠키에 담아 보내는 것 같기도 한데 잘 모릅니다.