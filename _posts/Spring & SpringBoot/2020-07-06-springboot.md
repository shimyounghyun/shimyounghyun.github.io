---
title : "[SpringBoot] 입력값 이외에 에러 발생"
toc : true
toc_sticky: true
categories :
    - Spring & SpringBoot
tag :
    - SpringBoot
---

### 설정

src -> main -> resources -> application.properties

```properties
# JSON을 객체로 변환하는 과정에서 unkown properties가 있을 경우 badrequest(400)를 반환한다
spring.jackson.deserialization.fail-on-unknown-properties=true
```

