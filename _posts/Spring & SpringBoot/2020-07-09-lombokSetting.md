---
title : "[SpringBoot] lombok 설정"
toc : true
toc_sticky: true
categories :
    - Spring & SpringBoot
tag :
    - SpringBoot
    - lombok
---

### 설치
롬복은 플러그인 설치는 한 번만 하면 되지만, 프로젝트마다 설정해야한다.

```
dependencies {
    compile('org.projectlombok:lombok') // 추가
    //...
}
```

### 설정

Preference > Annotation Processors > Enable Annotation processing 체크
![screenshot](/assets/images/2020-07-09_2.47.28.png)
