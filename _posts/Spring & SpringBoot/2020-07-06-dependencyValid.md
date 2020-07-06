---
title : "@Valid 유효성 체크"
toc : true
toc_sticky: true
categories :
    - Spring & SpringBoot
tag :
    - SpringBoot
    - Spring
---

### pom dependency추가

spring boot 2.3.1버전에서 아래의 의존성을 추가해주었습니다.
```xml
<!-- https://mvnrepository.com/artifact/javax.validation/validation-api -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### Controller @Valid 사용

```java
    @PostMapping(value = "/api/events/", produces = MediaTypes.HAL_JSON_VALUE)
        public ResponseEntity createEvent(@RequestBody @Valid EventDto eventDto) {
            // @Valid 검증 후 결과를 Errors객체에 전달해 준다.
            if (errors.hasErrors()){
                return ResponseEntity.badRequest().build();
            }
        }
```

### Dto 검증

```java
@Data @Builder @NoArgsConstructor @AllArgsConstructor
public class EventDto {
    @NotEmpty
    private String name;
    @NotEmpty
    private String description;
    @NotNull
    private LocalDateTime beginEnrollmentDateTime;
    @NotNull
    private LocalDateTime closeEnrollmentDateTime;
    @NotNull
    private LocalDateTime beginEventDateTime;
    @NotNull
    private LocalDateTime endEventDateTime;
    private String location;
    @Min(0)
    private int basePrice;
    @Min(0)
    private int maxPrice;
    @Min(0)
    private int limitOfEnrollment;
}
```

