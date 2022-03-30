# ingress vs ingress controller

ingress는 설정 파일이고

ingress controller는 그걸 바탕으로 설정된 loadbalance 겸 apigateway

spring cloud로 치면

spring netflix eruka == ingress controller 인듯

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6170dd0a-d45f-46d9-902a-4644d6b33f24/Untitled.png)

[Spring Cloud for Microservices Compared to Kubernetes | Red Hat Developer](https://developers.redhat.com/blog/2016/12/09/spring-cloud-for-microservices-compared-to-kubernetes)

아래 링크 보면 istio나 traefik이 국룰인 것 같다.

[https://docs.google.com/spreadsheets/d/1DnsHtdHbxjvHmxvlu7VhzWcWgLAn_Mc5L1WlhLDA__k/edit#gid=0](https://docs.google.com/spreadsheets/d/1DnsHtdHbxjvHmxvlu7VhzWcWgLAn_Mc5L1WlhLDA__k/edit#gid=0)

[https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b](https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b)

성능 비교

`nginx` 많이 쓸 줄 알았는데 성능이 좋지 않은 것 같다.

[https://www.youtube.com/watch?v=WWGGoHPIvTE](https://www.youtube.com/watch?v=WWGGoHPIvTE)