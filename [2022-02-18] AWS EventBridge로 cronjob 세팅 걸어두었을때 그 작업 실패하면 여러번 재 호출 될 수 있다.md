## 1. ⚠️ AWS EventBridge로 cronjob 세팅 걸어두었을때 그 작업 실패하면 여러번 재 호출 될 수 있다!
<br />

아침에 AWS EventBridge로 cronjob 세팅 걸어둔 작업이 계속 실행된 로그를 확인해서 왜 이게 여러번 실행 되었을 까 찾아보니,  
EventBridge는 기본적으로 최장 24시간까지 실패한 호출을 재시도한다는 사실을 알게 되었다...   
<br />
[관련 문서](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html)