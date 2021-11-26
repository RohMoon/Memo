#LogBack Architecture


`Logback` 아키텍쳐는 `Generic` 하기 때문에 다양한 상황에서 이용될 수 있다.  

`Logback`은 아래의 세 가지 모듈로 나뉜다.

- `logback-core`
- `logback-classic`
- `logback`

`logback-core`는 다른 두 모듈을 위한 기반 역할을 하는 모듈이다.

`logback-classic`은 `logback-core`에서 확장된 모듈로, `log4j`에서 매우 향상된 버젼에 해당 된다.  
`logback-classic`은 기본적으로 `SLF4J API`를 구현하여 `log4j` 또는 `Java.util.loggin(JUL)`과 같은 다른 로깅 시스템을 쉽게 전환할 수 있도록 한다.

`logback-access`는 `Servlet Container`와 통합되어 `HTTP엑세스`에 대한 로깅 기능을 제공한다.

##logback-classic

---
`Logback`은 `Logger`, `Appender`, `Layout` 이 세가지의 기본 클래스로 구성되어 있다.  
이 세 가지 `Component`는 개발자가 타입이나 레벨에 따라 메시지를 기록할 수 있도록 하고 이런 메시지들이 런타입에 어떤 형식으로, 어디에 출력될지 제어할수 있도록 한다.

`Logger`클래스는 `logback-classic` 모듈에 속하고, `Appender`와 `Layout`인터페이스는 `logback-core`모듈에 속하기 때문에,
`logback-core`에는 `looger`의 개념이 없다.

##Logger context

----
`System.out.println`을 통한 출력에 비해 `Logger` API가 갖는 가장 큰 장점은 환경이나 사용에 따라 특정 메시지를 비활성화시킬 수 있다는 점이다.  
개발자는 이러한 기능을 통해 모든 로그에 대해서 분류하거나 컨트롤 할 수 있다.  
`Logger`는 상위 계층인 `LoggerContext`에 부착되며, 각각 `Logger`는 로그를 만들어 낼 수 있다.

`Logger`는 하나의 `Entity`이며 계층을 갖는다. 이러한 계층은 이름 규칙에 따라 파악할 수 있다.  
로거의 이름 뒤에 `dot(.)`이 있다면 `dot`이전의 `prefix` 이름을 가진 로거가 상위 로거가 된다.  
예를 들면 `com.foo`라는 로거는 `com.foo.Bar`의 상위 로거라는 이야기이다.  
`Root Logger`는 로거 계층의 맨 위에 존재한다.  이 로거는 다음과 같은 코드를 통해 가져올 수 있다.
```java
Logger rootLogger = LoggerFactory.getLogger(org.slf4j.Logger.Root_Logger_NAME);
```

모든 로거는 `org.slf4j.LoggerFactory`의 `getLogger` 메서드를 통해 가져올 수 있다.  
메서드의 파라미터로는 원하는 로거의 이름이 전달 된다.  
`getLogger`메서드를 통해 가져오는 `Logger`인터페이싀 형태는 다음과 같다.
```java
package org.slf4j;
public interface Logger{
    
    //Printing methods:
    public void trace(String message);
    public void debug(String message);
    public void info(String message);
    public void warn(String message);
    public void error(String message);
}
```

`logback-classic` 모듈의 `ch.qos.logback.classic.Level` 클래스에는 로거가 사용 가능한 다섯 가지 레벨인 `TRACE`,`DEBUG`,`INFO`,`WARN`,`ERROR`가 정의되어 있다.  
로거에 레벨이 정해지지 않았더라면 상위 로거의 레벨을 상속 받으며 기본 레벨은 `DEBUG`이다.

로그의 레벨은 아래와 같으며, 지정된 레벨 이하의 메서드 호출은 기록되지 않는다.
```
TRACE < DEBUG < INFO < WARN <ERROR
```
예를 들어 `INFO`레벨로 지정한 로거는 `INFO`,`WARN`,`ERROR`로그만 기록하게 된다.

## LoggerFactory.getLogger 반환 Logger

---
앞서 `LoggerFactory.getLogger` 메서드를 통해 원하는 이름에 로거를 가져올 수 있다.  
그렇기에, 파라미터로 전달된 값이 같다면 반환하는 로거도 정확히 같은 오브젝트이다.

주로 `Logger`의 이름은 `software component`를 따라 지정하게 된다.
예시로, 한 클래스에서 사용하는 로거는 그 클래스의 정규화된 이름과 동일하게 인스턴스화 하여 사용 합니다.    
로그 출력에는 생성 로거의 이름이 표시되므로 이러한 로거 명명 규칙을 통해 로그 메시지의 출처를 쉽게 식별할 수 있습니다.

## Appenders와 Layout

---
로깅을 선택적으로 허용/비허용 하는 것은 하나의 기능일 뿐이다.  
`Logback`은 로깅을 다양한 ***destinations*** 에 대하여 출력하도록 지정할 수 있다.  
`Logback`에서는 이러한 ***destinations***을 `Appender`라고 명명한다.
현재 `Appender`의 종류는 
- Console (콘솔)
- files(파일)
- Remote Socket Servers(원격 소켓 서버)
- DB(MySQL, PostgreSQL)  
등이 있다.

하나 이상의 `Appender`는 하나의 `Logger`에 부착될 수 있다.  
(반대로, 하나의 `Logger`는 여러 개의 하나 이상의 `Appender`를 가진다고 생각하는게 더 이해가 빠를 것이라고 한다.)

`addAppender` 메서드를 통해 `Logger`에 `appedner`를 추가할 수 있다.  
특정 로거로 들어오는 로깅 요청은 자신을 포함한 상위 계층의 로거의 모든 `Appender`에게 전달된다.. (`Appender Additivity`).


예를 들어 `ConsoleAppender`가 `RootLogger`에 지정되었다면 모든 로깅 요청은 적어도 `Console`에 출력된다.

사용자들은 출력 ***destinations***를 정의하는 것 이외에 출력 포맷을 수정하고 싶을 수 있는데,  
이를 만족 시켜줄 `component`가 바로 `Layout`이다.  
`Layout`은 사용자의 요청에 따라 로그 메시지의 포맷을 지정해준다.  
`PatternLayout`은 C언어의 `pringf`함수처럼 로그를 출력하도록 도와준다.

## 파라미터를 이용한 로깅

---
`logback-classic`의 `Logger`는 `SLF4J API`를 `implements` 한다.  
그렇게 `print` 방식에 여러 파라미터를 허용하도록 한다.

```Java
Object entry = new SomeObject();
//logback {}과 parameter 방식을 이용한 로깅 -(1)
logger.debug("the entry is {}.", entry);
//'+' 로 문자열 생성을 통한 로깅 - (2)
logger.debug("The entry is "+ entry +".");
```
문자열에서 중괄호로 ('{}') 묶은 부분이 등자앟면 해당 부분은 뒤이어 나오는 파라미터로 대체 된다.
위에서 (1)`.Logback`이 선택한 파라미터를 이용한 로깅은 기존의 '+' 연산자를 이용한 문자열 생성 후 전달하는 방식(2)에 비해 ***속도***에 유리함을 갖는다.  

차이점은 `logger.debug`의 호출 순서인데,  
(1) 방법은 `logger.debug`메서드 내부에서 파라미터를 검증하고 출력을하는데,   
반면 (2) 방법은 '+' 연산자를 이용한 문자열 생성 이후 `logger.debug`메서드가 실행된다.  

이 순서는 로깅 레벨에 따라 성능 상 차이를 보이게 되며,  
지정한 로깅 레벨이 INFO라면 logger.debug 메서드는 실행 목록에서 제외된다.  
하지만 (2)방법을 사용했다면 호출되지도 않을 메서드의 파라미터 생성에 비용이 들게 된다. 