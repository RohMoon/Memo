#[SpringBoot]
## PROFILE
- `spring framework`에서 제공해주는 기능<br>
- 특정한 프로파일에서만 특정한 `Bean`을 등록하고 싶다거나,<br>
애플리케이션의 동작을 특정 프로파일 일 때 `Bean`설정을 다르게 하고 싶다거나 할 때 사용.
<br><br>
### 프로파일 예시
`config`라는 디렉토리를 만들고, 2개의 `Bean`설정을 만들어준다.
<br><br>
![img_43.png](img_43.png)
- 2개의 `Bean`설정 클래스 모두 hello 라는 `Bean`을 만들어준다.
```java
package com.sm.profile;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Profile("prod")
@Configuration
public class BaseConfiguration {

    @Bean // Configuration이니까 Bean을 만들 수 있는 것
    public String hello(){
        return "hello base";
    }
}

```
```java
package com.sm.profile;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class TestConfiguration {
    @Bean
    public String hello(){
        return "hello test";
    }
}
```
그리고 각각 다른 프로파일을 붙여준다.<br><br>
```java
package com.sm.profile;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class BaseConfiguration {
    @Profile("prod")//<<<
    @Bean // Configuration이니까 Bean을 만들 수 있는 것
    public String hello(){
        return "hello base";
    }
}

```
```java
package com.sm.profile;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Profile("test") //<<
@Configuration
public class TestConfiguration {
    @Bean
    public String hello(){
        return "hello test";
    }
}
```
즉, `BaseConfiguration`은 프로파일이 `prod`일 때만, 사용되고 그렇지 않을 때는 사용되지 않는다.<br><br>
마찬가지로 `TestConfiguration`은 프로파일이 `test`일 때만, 사용 되고 그렇지 않을 때는 사용되지 않는다.<br><br>
이 상태로 `hello`를 주입받아 사용하면 `error`가 발생한다.
![img_44.png](img_44.png)<br><br>
스프링 부트에서는 `application.properties`에서 사용할 프로파일을 설정해 줄 수 있다.<br><br>
![img_45.png](img_45.png)
이것을 적요해주면, 적용해준 프로파일의 `hello`가 출력됨을 볼 수 있다.<br><br>
`application.properties`에서 사용한 `spring.profiles.active`도 프로퍼티이다.<br><br>
#### 프로파일용 프로퍼티
- 프로파일용 프로퍼티를 만들어 줄 수도 있다.
- `prod`용 프로퍼티 파일과,`test`용 프로퍼티 파일을 만들어주고, 각각 동일한`key`값의 프로퍼티를 만들어준다.<br><br>
![img_46.png](img_46.png)
- 이런 프로파일 관련된 프로퍼티 파일의 우선순위가 기본 `application.yml`보다 높다.<br><br>
- `mvn clean package`이후,
  - `java -jar spring-boot-practice-1.0.jar --spring.profiles.active=prod`명렁어로 터미널에서 실행해보면 프로파일이 `prod`로 설정되어 `[prod]~`가 콘솔에 표시된다.<br><br>
  - (커맨드 라인 명령어가 프로퍼티 파일의 우선순위보다 높기 때문에 오버라이딩)
  ![img_47.png](img_47.png)
  - test도 마찬가지다.
#### 활성화할 프로파일 설정
- 프로퍼티 파일에 `spring.profiles.include=`를 이용하여 추가할 프로파일을 설정할 수 있다.
- ![img_48.png](img_48.png)
- 이 설정이 읽혀졌을 때 추가할 프로파일을 활성화 하라는 것이다.<br><br>
- 아래와 같이 코드를 두고 `--spring.profiles.active=prod`로 실행하면<br>
프로파일이 `prod`로 실행되고, `application-test.properties`에 의해 `application-test2.properties`가 활성화 되어 <br>
`myProperties.getFullName` 값은 `test2` 프로퍼티에 정의 되어 있는 `[test]kkyu`가 된다.<br><br>
- ![img_49.png](img_49.png)
- tip)프로파일 값을 빌드때마다 주고 싶으면 아래와 같이 줄 수 있다.<br><br>
![img_51.png](img_51.png)