# [Spring Boot] 
## 외부설정 1- Application.Properties

### 외부 설정 파일 이란?
- 어플리케이션에서 사용하는 여러가지 설정 값들을 어플리케이션의 밖이나 안에 정의할 수 있는 기능을 말한다. <br><br>
### application.properties
- 이 파일은 스프링부트가 애플리케이션을 구동할 때 자동으로 로딩하는 파일이다 .
- key-value 형식으로 값을 정의하면 애플리케이션에서 참조하여 사용할 수 있다.<br><br>
- ![img_28.png](img_28.png)
- 값을 참조할 때는 여러가지 방법이 있다. `@Value` 어노테이션으로 값을 받아올 수도 있다.
```java
package com.sm;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;
@Component
@Order(1)
public class AppRunner implements ApplicationRunner {

    @Value("${my.name}")// <<<
    String name;

    @Value("${my.age}") // <<<
    int age;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("AppRunner ApplicationRunner");
        System.out.println("foo : "+ args.containsOption("foo"));
        System.out.println("bar : "+ args.containsOption("bar"));
        System.out.println(name);
    }
}
```
### 프로퍼티 random 값 사용
- `application.properties` 에 `${random.무엇}`으로 랜덤 값을 줄 수도 있다.
- ![img_29.png](img_29.png)

### 프로퍼티 place holder
- `application.properties`에 정의해준 값을 변수로 사용하듯이 그대로 사용할 수 있다.
- ![img_30.png](img_30.png)

### 프로퍼티 우선순위
- 프로퍼티를 설정할 수 있는 방법은 매우 다양하다.<br> 따라서 각 방법마다 우선순위가 있다.<br>
여러 방법으로 같은 프로퍼티를 정의하고 있으면 우선순위가 제일 높은 방법으로 정의한 프로퍼티값이 오버라이딩 된다.
  - 우선순위는 아래와 같다.
    1. 유저 홈 디렉토리에 있는 `spring-boot-dev-tools.properties`<br><br>
    2. 테스트에 있는 `@TestPropertySource`<br><br>
    3. `@SpringBootTest` 어노테이션의 `properties` `Attribute`<br><br>
    4. 커맨드 라인 아규먼트<br><br>
    5. `SPRING_APPLICATION_JSON` (환경 변수 또는 시스템 프로퍼티) 에 들어 있는 프로퍼티<br><br>
    6. `ServletConfig` 파라미터<br><br>
    7. `ServletContext` 파라미터<br><br>
    8. `java:comp/env JNDI` Attribute<br><br>
    9. `System.getProperties()` 자바 시스템 프로퍼티<br><br>
    10. OS 환경 변수<br><br>
    11. `RandomValuePropertySource`<br><br>
    12. JAR 밖에 없는 특정 프로파일용 `application.properties`<br><br>
    13. JAR 안에 있는 특정 프로파일용 `application.properties`<br><br>
    14. JAR 밖에 있는 `application.properties`<br><br>
    15. JAR 안에 있는 `application.properties`<br><br>
    16. `@PropertySource`<br><br>
    17. 기본 프로퍼티  (SpringApplication.setDefaultProperties)<br><br>
  - `resources`- `application.properties`는 15번 순위이다.<br><br><br><br>
  - 우선 순위가 높은 `test`에서 `property`를 설정해보자.<br><br>
  - test 디렉토리 밑에 `java`에 클래스를 만들고 `@SpringBootTest`, `@RunWith(SpringRunner.class)`어노테이션을 붙여준다.<br><br>
  - `@Test` 어노테이션을 붙여 메서드를 하나 만들어준다.<br><br>
  - ![img_31.png](img_31.png)<br><br>
  - 모든 프로퍼티들을 기본적으로 `Enviroment`를 통해서 확인할 수 있다.<br><br><br>
  따라서 `Enviroment`를 주입받아 `getProperty`로 프로퍼티를 받아온다.<br><br>
  - ![img_32.png](img_32.png)<br><br>
  - 그리고 `test`디렉토리에 `resources` 디렉토리를 만들고 `project structure`에서 `Module`부분에서 만들어준 `resources`를 `Test Resources`로 바꿔준다.<br><br><br><br>
  - ![img_33.png](img_33.png)<br><br>
  - 그 후, 하위에 `application.properties`를 만들어준다. 그리고, 프로퍼티의 키는 그대로두고, 값을 바꿔준다.<br><br>
  - ![img_34.png](img_34.png)<br><br>
  - 그 후 테스트 파일을 돌려보면 우선순위가 높은 테스트파일의 프로퍼티가 출력된다.<br><br>
  - ![img_35.png](img_35.png)<br><br>
  - 테스트코드를 실행하면, 먼저 테스트코드가 빌드가 되고, 빌드를 할 때,`src`를 전부 빌드하여 `classpath`에 놓는다.<br><br>
  - 그 다음에 테스트 코드를 빌드하여 `classpath`에 놓는다. 그 때, `application.properties`가 바뀌는 원리이다.<br><br>
  - 그렇기 때문에 만약  `main` 의 `application.properties`에 정의되어 있는 값이 있고, `test`의 `application.properties`에는 그 값이 없으면 테스트가 깨진다.<br><br>
  - 실행 시 `application.properties`가 테스트 것으로 덮여쓰여지기 때문이다. 그래서 버그가 안나게 하기 위해서는 `test`의 것도 똑같은 `key` 값을 넣어줘야 한다.<br><br>
  - `@TestPropertySource` 라는 어노테이션을 사용해서도 프로퍼티를 정해줄 수 있다. 우선순위가 2순위인 방법이다.<br><br>
  - ![img_36.png](img_36.png)<br><br>
  - 만약 설정해주고 싶은 프로퍼티 값이 여러개일 경우 .properties 파일을 만들어 적용해줄 수도 있다.<br><br>
  - ![img_37.png](img_37.png)<br><br>
  - classpath 기준으로 만들어준 파일의 위치를 적어준다.<br><br>
  - ![img_39.png](img_39.png)<br><br>
### application.properties의 우선순위 
- `application.properties`를 같은 위치가 아니라 다른 위치에 둘 경우는 컴파일해도 덮어쓰지 않게 된다.<br><br>
- 이 때 우선순위는 아래와 같다.
  1. file:./config/: 프로젝트 디렉토리 바로 아래에 `config`라는 디렉토리를 만들고 그 안에 `application.properties` 만들어주는 방법
  2. file:./: 프로젝트 루트 바로 아래에 application.properties 만들어주는 방법
  3. classpath:/config/ : classpath 아래에 `config`라는 디렉토리를 만들고 그 안에 `application.properties` 만들어주는 방법
  4. classpath:/ :classpath 아래에 `application.properties` 만들어 주는 방법
- ![img_40.png](img_40.png)
- 