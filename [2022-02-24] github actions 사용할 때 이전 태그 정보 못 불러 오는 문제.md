# github actions 사용할 때 이전 태그 정보 못 불러 오는 문제

```
  - uses: actions/checkout@v2
    with:
      fetch-depth: 20
```

이렇게 fetch-depth 지정하면 나중에 이렇게 태그정보 같은거 불러올려고 할때 fetch가 다안되서 이전에 생성한 태그정보를 못 불러 올 수 도 있다.
<br /><br />
```
  - name: Get previous tag name
    id: get-prev-tag
    run: |
      PREV_TAG=$(git describe --abbrev=0 --tags ${GITHUB_REF}^)
      echo "::set-output name=tag::$PREV_TAG"
```
<br />

>   => fatal: No tags can describe ‘d5da8bda17e65fefbdeb7881010815f98269a87d’.
>   Try --always, or create some tags.

<br />

git tag -l 명령어로 태그 정보 확인 해보면 태그 정보가 origin으로 부터 안받아진것을 확인 할 수 있었다…

이전에 생성한 태그 정보 같은게 필요하다면 actions/checkout@v2 단계에서 일단은 이렇게 지정해 주자! (디폴트는 1이고, 0으로 지정하면 모든 커밋을 다 받아온다고 한다.)
fetch-depth: 0
