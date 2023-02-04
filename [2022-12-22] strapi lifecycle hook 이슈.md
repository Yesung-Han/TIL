# Strapi lifecycle hook 관련 이슈

## 😰 문제 상황
Strapi에서 모델의 생성 업데이트시 일어나는 작업을 커스터마이징 해야하는 이슈가 생겼다.


Strapi의 경우, lifecycle hook을 통해 각 모델의 생성, 업데이트시 일어나는 작업을 오버라이딩 할 수 있다. 폴더에 lifecycle.js 라는 파일을 만들면 해당 파일로 데이터 생성, 업데이트 시 일어나는 일을 오버라이딩 할 수 있는데 기존 DisplayDate컬럼이 없는 상태로 포스트를 생성하거나 수정할때 현재 시간 값을 지정하도록 하기 위해 코드를 다음과 같이 작성을 했다.

```
module.exports = {
	// 생성 전에 실행
  beforeCreate(event) {
    const { data } = event.params;

    if (!data.displayDate && data.createdAt !== null) {
      data.displayDate = data.createdAt;
    }
  },

	// 업데이트 또는 publish 상태 변경 전에 실행
  beforeUpdate: async (event) => {
    const { uid } = event.model;
    const { data, where } = event.params;
    const currData = await strapi.query(uid).findOne({ where });

    if (!data.displayDate) {
      data.displayDate = currData.createdAt;
    }
  }
};
```

먼저 lifecycle hook으로 제공되는 beforeCreate, beforeUpdate 함수가 실행 되는 시점은 다음과 같다.

    beforeCreate : 모델 생성 시
    beforeUpdate : 모델 업데이트 또는 모델 발행 시(draft -> publish)


위 두 함수 중 beforeUpdate는 모델 업데이트 또는 모델 발행 시(draft -> publish) 두가지 경우에 실행 될 수 있는데 각각의 경우에 들어오는 데이터값이 다르다.


모델 발행 시에 data로 들어오는 값:
```
{
	updatedBy: 1,
	publishedAt: 2022-12-22T13:51:54.419Z,
	updatedAt: 2022-12-22T13:51:54.425Z
}
```

모델 업데이트 시에 data로 들어오는 값:
```
{
	title: 'test2',
	subtitle: null,
	content: '<p>sssssssssssssss</p>',
	displayDate: '2022-12-01T12:20:00.000Z',
	thumbnail: null,
	updatedBy: 1,
	updatedAt: 2022-12-22T13:52:57.223Z
}
```

즉 같은 함수인데 상황에 따라 다른값이 들어오므로, 아래와 같이 그냥 if문으로 처리해 버리면 문제가 생길 수 있다.
```
  if (!data.displayDate) {
      data.displayDate = currData.createdAt;
    }
```
이렇게 처리를 해버리면 업데이트 시에는 displayDate값이 이전 값으로 잘 들어가 있으므로 초기화가 안일어나지만, 

draft 상태의 포스트를 publish 상태로 발행 할 경우 displayDate값이 모델에 이미 있어도 data에는 안들어가 있으므로 if문안의 코드가 실행되어 "이미 있는 값을 초기화를 해버리는 문제"가 발생될수 있다.😰

<br>

## 🥹 해결
그래서 beforeUpdate 함수에 현재 데이터베이스에서 읽어온 데이터에 이미 설정 된 displayDate가 없고, 새로 들어온 data에도 displayDate가 없는 경우에만 현재 날짜로 채워주도록 수정했다.
```

if (!currData.displayDate && !data.displayDate) {
      // 현재 displayDate 데이터도 없고, displayDate 수정 요청도 없을 경우에만 현재 시각으로 초기화
      data.displayDate = currData.createdAt;
    }

```

## 🤔 이슈 회고
Strapi의 경우, python의 Django와 같이 어드민 페이지를 직접 만들어 준다는 장점이 있지만, 그만큼 내부적으로 이미 구현되어있어 예상치 못하게 작동되는 부분들이 많은것 같다. 다행이 문서가 잘되어있고 커뮤니티와 개발진행이 활성화 되어있어 lifecycle 관련한 문제로 수정을 진행할수 있었던 부분은 참 다행이라고 생각한다.