## 대표적인 로그인 방식

### 세션 방식

### 토큰 방식

## 서버가 여러 개일 때 session 처리 시 발생하는 문제

### 요약

서버가 여러 대일 때는 session 정보를 어디에 저장해야 하는지 문제가 생깁니다.

사용자의 세션 값을 A서버에 저장할 경우 다른 서버로 접속 할 경우 세션 값을 찾지 못하는 문제가 생깁니다. 대부분 세션에는 사용자의 로그인 정보를 저장하는데 요청을 보낼 때마다 로그인을 해야하는 경우가 생길 수 있는 것입니다. 이러한 문제를 session의 정합성 문제라고 합니다. 

### 그림으로 설명

이해를 돕기 위해 그림으로 설명 해보겠습니다.

![image](https://user-images.githubusercontent.com/58874807/154282779-9dd1a41c-898b-45cb-9746-147df75354aa.png)

(1) 사용자가 서버에 요청을 보냅니다. *load balancer가 임의로 서버 A로 요청을 할당합니다.

(2) 사용자의 세션 값이 존재하지 않음으로 session 값을 발급해 줍니다.

- 현재 사용자의 세션은 A 서버에 저장된 상태다.

---

load balancer란 단어 뜻 그대로 부하를 분산해주는 역할을 하는 서버입니다.

위 그림에서는 요청이 서버 A나 서버 B로만 쏠리는 것을 방지하기 위해 적절히 요청을 분산합니다.

![image](https://user-images.githubusercontent.com/58874807/154282978-5b49e9b2-ec75-4280-98a5-0218e75ca01a.png)

(3)다시 사용자가 서버에 요청을 보낸다. 로드 밸런서가 서버 B로 요청을 할당합니다.

이 때 문제가 생기는데 사용자의 세션 값은 서버 A에 저장되어 있음으로 session 값을 찾을 수가 없게 됩니다. 우리는 이러한 문제를 하기 위한 몇 가지 방법이 있습니다.

# 세션 정합성 해결 방법

## **Session Clustering**

### 동작 방식

모든 WAS에서 모든 세션의 정보를 가지고 있는  방식입니다. 새로운 세션이 추가될 때마다 모든 서버의 세션을 업데이트합니다. 

### 장점

사용자가 어느 서버에 접속하든 동일한 세션 정보를 확인할 수 있습니다.

### 단점

- 모든 서버에 세션 정보를 저장해야 하므로 메모리 비 효율이 생깁니다.
- 세션 정보를 업데이트를 할 때 네트워크 통신을 해야 해서 자원을 많이 사용하게 됩니다.
    
    →was 개수가 많거나 msa와 같이 서버의 개수가 많을 경우 매우 비효율적이다.
    

## **Sticky Session**

### 동작 방식

사용자의 세션이 A 서버에서 생성이 되었다면 사용자의 요청은 A서버로만 보내는 방식으로 

load balancer에 의해 동작합니다. 세션이 만료되기 전에는 세션이 생성되었던 A서버로 요청이 전송됩니다. 이 때 어느 서버로 가야 하는지 결정하는 방법은 sticky session에 대한 쿠키를 발행하고 

load balancer에서 해당 쿠키를 확인합니다.

![image](https://user-images.githubusercontent.com/58874807/154283211-a2cf2fc8-188f-4933-9f35-36b4128bd9d8.png)


> [참고 자료]
[https://www.imperva.com/learn/availability/sticky-session-persistence-and-cookies/](https://www.imperva.com/learn/availability/sticky-session-persistence-and-cookies/)
> 

sticky session이 적용되지 않은 위쪽 그림은  request를 임의로 서버에게 보내지만 

sticky session이 적용된 아래쪽 그림은 request를 특정 서버로만 보냅니다.

### 장점

1. 최소한의 데이터 교환
    
    로드 밸런스를 통해 사용자의 요청이 관리 되기 때문에 동일 네트워크 간의 서버는 세션 데이터를 교환할 필요가 없습니다. 
    
2. ram 캐시 사용
    
    application의 ram cache를 효과적으로 이용할 수 있고 캐시기 때문에 결과적으로 빠른 응답을 얻을 수가 있다.
    

### 단점

1. 대규모 서비스에서는 많은 비용이 발생할 수 있습니다.
2. 서버가 꺼지면 서버에 저장된 세션 정보가 분실 됩니다.
3. 특정 사용자가 하나의 서버에만 의존하고 있기 때문에 특정 서버에 트래픽이 몰려 장애가 발생할 가능성이 있습니다.

---

aws의 elastic load balancing을 이용하여 sticky 세션을 유지 하는 방법

[애플리케이션 로드 밸런서에 대한 고정 세션](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/sticky-sessions.html)

## **캐시 서버를 이용한 session storage 사용**

### 동작 방식

Redis와 같은 Inmemory DB를 활용하여 session storage를 운영한다. session 정보를 서버에 저장하지 않고 외부 redis 서버를 띄워서 세션 정보를 저장한다.

### 장점

- 서버 간의 네트워크 요청이 필요 없어 서버의 부하를 줄일 수 있고 stateless한 상태를 만들 수 있다.
- 또한 WAS에 문제가 생기더라도 세션 정보가 손실 되지 않는다.

### 단점

- 서버 간 네트워크 요청은 없지만 캐시 서버에 네트워크 요청을 보내야 함으로 session 보다 속도가 느리다.
- was의 개수가 많이 늘어나거나 msa가 될 경우 단일 캐시 서버로는 세션을 관리하기 힘듦으로 캐시 서버를 다중화 해야 하는데 캐시 서버를 다중화해야하는데 캐시 서버 간의 정합성을 유지하는 알고리즘은 구현이 복잡하다.

## 세션은 아니지만 같은 효과를 낼 수 있는 방법

### 토큰을 이용한다

토큰을 이용하여 사용자 인증을 관리한다면 세션을 관리할 필요가 없어진다.

각 서버에 비밀키만 일치해 놓으면 서버가 몇 천대가 되어도 사용자가 어느 서버를 접속하든 인증을 효과적으로 관리할 수 있다.

최근 OAuth와 jwt 토큰을 많이 사용하는 이유로 연결될 것 같다.

## 하나더!? 토큰 인증 방식도 필요 없다?!?

자세히는 모르겠지만 최근 나오는 web 3.0을 이용하면 토큰 인증 방식 자체도 필요 없어진다는..

[You don't need JWT anymore](https://medium.com/@bytesbay/you-dont-need-jwt-anymore-974aa6196976)

[참고 자료]

[https://www.imperva.com/learn/availability/sticky-session-persistence-and-cookies/](https://www.imperva.com/learn/availability/sticky-session-persistence-and-cookies/)

[https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/sticky-sessions.html](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/sticky-sessions.html)