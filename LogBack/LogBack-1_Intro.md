# LogBack Intro

## LogBack 이란?

---
- `Logback`은 Java 커뮤니티에서 가장 널리 사용되는 로깅 프레임워크이다.
- `Log4j`의 설립자 Ceki Gulcu에 의해 디자인이 되었기 때문에 `Log4j`를 계승한다. 빠르게 구현할 수 있는 것이 특징이다.
- 많은 구성 옵션을 제공하기 때문에 Log파일보다 유연하
  ---게 아카이빙 가능하다.

## LogBack 시작

- `Logback` 사용을 위한 몇 가지 준비 사항
  - `maven dependency` 에 `logback-core`와 `SLF4J-api` 추가 한다.    
    `Logback`은 기본 인터페이스로써 `SLF4J(Simple Logging Facade for Java)`를 사용한다.  
    이름만으로 보아 `Facade` 패턴을 적용한 것을 확인 할 수 있다.
  ```html
   <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.3</version>
  </dependency>
  
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.30</version>
    <scope>test</scope>
  </dependency>
  ```
아래와 같이 `org.slf4j.Logger` 와 `org.slf4j.LoggerFactory`를 `import`하고 코드를 작성합시다.
```java
package chapters.introduction;

import org.slf4j.Logger;
import org.sl4fj.LoggerFactory;

public class HelloWorld1{
    
    public static void main(String[] args){
        
        Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld1");
        
    }
    
}
```
위 코드를 분석해보면
`LoggerFactory.getLogger()`메서드를 통해 `Logger` 인스턴스를 가져오는 것을 알 수 있습니다.  
이 `Logger`의 이름은 `getLogger()`메서드에 파라미터로 전달한 `"chapters.introduction.HelloWorld1"`이다.  
`Logger`는 `debug()`메서드를 통해 `DEBUG`레벨의 로깅으로 `"HelloWorld"`를 출력한다.  
  
위의 예에서 `LogBack`클래스를 참조하지 않고 오직 `slf4j` 클래스만을 통해 로깅하는 것을 알 수 있습니다.  
`Logback`은 `slf4j api`를 이용하여 `Logback`클래스 사용방법을 알지 못하더라도 쉽게 로깅 프레임워크를 이용할 수 있도록 해줍니다.  

`main`을 실행시켜보면 
```java
20:49:07.962 [main] DEBUG chapters.introduction.HelloWorld1 - Hello world.
```
`logback`의 기본 출력을 저장하지 않았다면 `ConsoleAppender`를 `root logger`에 추가합니다.
> `logger`는 로그 메세지를 위한 하나의 `context`로, `appender`는 `logger`가 출력하는 위치로 간단히 이해하시고 넘어가자.


### Logger의 상태 데이터에 대하여 출력하는 법을 알아보자.
```java
package chapters.introduction;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld2 {
    
    public static void main(String[] args){
        Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld2");
        logger.debug("Hello World.");
        
        //print internal state
        LoggerContext lc = (LoggerContext) LoggerFactory.getILoggerFactory();
        StatusPrinter.print(lc); 
    }
}
```
`Logback`은 순서대로 설정 정보를 담고 있는 파일일 `logback.groovy`,`logback-test.xml`,`logback.xml`에 대해 찾습니다.  
하지만 우리는 지정해둔 설정 파일이 없기에 해당 `configuration files`를 찾는데 실패했음을 볼 수 있습니다.  
이렇게 되어서 `logBack`은 자신의 기본 설정 정책을 통해 `ConsoleAppender`를 이용하게 됩니다.

`Logback`이 사용할 설정 파일을 찾지 못하였을 때 이는 중요한 에러이며, 내부에서 `Logback` 이 이러한 정보를 자동으로 출력한다는 것을 알 수 있다.