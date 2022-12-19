## 1. HTTP X-Forwarded-For 헤더에 대해 몰라서 IP 주소정보 잘못 받아 온 문제
<br />

HTTP X-Forwarded-For 헤더값은 클라이언트의 ip 주소를 알고 싶을 때 사용될 수 있다.

```
  X-Forwarded-For: client, proxy1, proxy2 
```

그런데 받아온 정보에 프록시를 거칠 때마다 뒤에 [, ] (콤마 스페이스)를 추가될 수 있다. => 맨 오른쪽 값이 클라이언트의 ip