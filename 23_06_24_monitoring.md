# Monitoring

## 요구사항

- 현재 근무하고있는 본부에서 Logging, APM을 측정하기위한 서비스가 필요했음
- 원래라면... ELK나 Grafana, Prometheus를 구성해야 할텐데.. 그럴 여건, 시간이 부족 (내가 못함)
- 그래서 DataDog을 쓰기로 했음

## DataDog

- DataDog 정말 잘 만든것 같음
- Datadog-Agent를 프로세스 레벨로 구동시켜서 해당 DataDog이 APM을 추적하거나, 기재한 로그위치를 기반으로 로그들을 추적해서 바로바로 쌓더라.. 좋더라 -> 잘 만들었더라

## Todo

- EB Logging
- ECS Logging

## Elastic Beanstalk Logging

- EB같은 경우에는 .ebextensions 폴더안에 99datadog.json에 내용을 기재해서 사용한다.
- 해당 내용에는 agent의 설치와 fileLogging에 위치를 기반으로 로그들을 추적한다
- 우리 회사같은 경우에는 winston을 활용해서 log를 _-debug.log, _-info.log, \*-error.log 떨어트리는데 그대로 기재해주었다.
- 그리고 APM 측정은 dd-trace를 기반으로 하는데, postgreSQL이나 여러 Trace를 측정한다. (많이 공부해야 할듯)

## ECS (Fargate)

- ECS Fargate같은 경우에는 sidecar 형식으로 agent가 동작한다.
- ecs task_definition에 해당 application, datadog-agent를 설치해서 tracking한다.
- 로그추적은 두가지 방법이 있는데,

  - ecs -> cloudwatch -> lambda -> datadog을 추적하는 방법 (별로라고 생각함)

  ```
      왜냐면, cloudwatch까지는 알아서 ecs에 추적해주는데,
      뭔가 람다까지 끼는건 불필요하고, Scaling이 엄청 늘지 않을까? 로그가 얼마나 쌓일지도 모르는데?
  ```

  - fluenbit을 사용해서 로그를 추적한다. (이방법을 사용함)

  ```
      filebeat?, fluentd 형식인건가? fluentd는 로그수집기 역할이니까 Logstash랑 같은거고
      그럼 결국에 filebeat인데....

      task_definition에 logConfiguration을 firlow? 로 설정한후에 datadog설정을하고
      이러한 설정을 기반으로 또다른 container fluendbit을 설정해서 Tracking하는 구조인것같다.
  ```

## 아쉬운 점

- 뭐... 핑계라고 하면 DataDog 구성에 모든 시간을 쏟은 건 아니라서, 많이 설정을 못했다.
- 기존 DataDog을 사용해보신 Backend Engineer가 대부분 설정해주셨다. 나는 Agent, apm설치만...
- 문서를 끼고한번 살아야겠다. 문서를 잘봐야 DataDog을 사용할 수 있을 것같다.
- 근데... DataDog을 계속쓸까? 하는생각은 있다. 결국 여러 비즈니스에 맞춰서 나중에는 직접 구성하지 않을까? 그때까지 공부믈 많이 해놔야겠다.
