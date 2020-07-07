---
title : "Junit parameter 테스트"
toc : true
toc_sticky: true
categories :
    - TDD
tag :
    - junit
---
### 의존성 추가
```xml
<!-- junit5 기준 -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <version>5.4.2</version>
    <scope>test</scope>
</dependency>
<!-- // junit5 기준 -->

<!-- junit4 기준 -->
<dependency>
    <groupId>pl.pragmatists</groupId>
    <artifactId>JUnitParams</artifactId>
    <version>1.1.1</version>
    <scope>test</scope>
</dependency>
<!-- //junit4 기준 -->
```

### 테스트
junit5 파라미터 테스트
```java
@ParameterizedTest
@CsvSource({
        "0, 0, true",
        "100, 0, false",
        "0, 100, false"
})
public void testByJunit5(int param1, int param2, boolean param3) {
}

@ParameterizedTest
@MethodSource("paramsForTest")
public void testByJunit5(int param1, int param2, boolean param3) {
}

private static Stream<Arguments> paramsForTest() {
        return Stream.of(
                Arguments.of(0,0,true),
                Arguments.of(100, 0, false),
                Arguments.of(0, 100, false)
        );
    }
```

junit4 파라미터 테스트
```java
@Test
@Parameters({
        "0, 0, true",
        "100, 0, false",
        "0, 100, false"
})
public void testParamByJunit4(int param1, int param2, boolean param3) {
}

// [parametersFor + 테스트메소드 이름]의 규칙을 사용시 method = ""를 생략할 수 있다.
@Test
@Parameters(method = "paramsForTest")
public void testMethod(int param1, int param2, boolean param3) {
}
 
private Object[] parametersForTestMethod() {
    return new Object[] {
        new Object[] {0, 0, true},
        new Object[] {100, 0, false},
        new Object[] {0, 100, false}
    };
}
```