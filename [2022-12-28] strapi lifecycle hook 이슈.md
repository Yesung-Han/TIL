# Strapi 배포 시 trust proxy 관련 에러

## 😰 문제 상황
Strapi를 통해 만든 회사 홈페이지를 테스트 배포 후 작동을 테스트 하던 중 swagger document 링크에 들어가지지 않는 문제가 발생했다.

cloudwatch에서 오류를 확인 해 보니 다음과 같은 오류가 뜨고있었다.
```
Error: Cannot send secure cookie over unencrypted connection
```

해당 에러를 발생시킨 부분을 찾아보니 Koa 라이브러리에서 발생하고 있었다.
Strapi는 내부적으로 백엔드 프레임워크로 Koa를 채택해서 작동한다.

그리고 koa 라이브러리에서는 



<br>

## 🥹 해결


## 🤔 이슈 회고

> strapi 는 왜 코아를 선택했을까? - https://strapi.io/blog/why-koa