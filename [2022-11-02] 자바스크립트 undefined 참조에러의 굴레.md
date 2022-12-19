프론트 이슈 중 코드의 undefined 체크가 안되어 있어서 예기치 못한 에러가 난 상황이 있었다.


```
import ExampleModule from 'ua-parser-js';

.
.
.

const exampleFunc = (ua) => {
  
  const { name, version } = new ExampleModule().getSomeValue();
  const versionNum = parseInt(version.split('.')[0], 10);
	...
  }
};

.
.
.
```

이 부분에서 생길 수 있는 문제는 무었일까?

 

바로 version이 없어서 undefined 인 경우에 version.split 을 호출 했을때 undefined 참조 에러가 날 수 있다.

