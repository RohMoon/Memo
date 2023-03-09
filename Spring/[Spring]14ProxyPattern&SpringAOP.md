#[Spring] 
## 프록시 패턴 & 스프링 AOP
- 스프링 `AOP`는 프록시 기반의 `AOP` 구현체이며, 스프링 `Bean`에만 `AOP` 적용 가능하다.
### 프록시 패턴이란?
- 프록시 객체는 원래의 객체를 감싸고 있는 객체이다. 원래 객체와 타입은 동일하다.<br>
프록시 객체가 원래 객체를 감싸서 `client`의 요청을 처리하게 하는 패턴이다.
- 프록시 패턴을 쓰는 이유는 접근을 제어하고 싶거나, 부가 기능을 추가하고 싶을 때 사용한다.<br><br>
`EventService`라는 `interface`가 있고, 
```java
package com.basic.applicationContext;

public interface EventService {
    void createEvent();

    void publishEvent();

}
```
- `EventService`를 `implements`한 `TestEventService`가 있다고 하면,
```java
package com.basic.applicationContext;

import org.springframework.stereotype.Service;

@Service
public class TestEventService implements EventService{
    @Override
    public void createEvent() {
        System.out.println("Create Event");
    }

    @Override
    public void publishEvent() {
        System.out.println("Publish Event");
    }
}
```
- 이 코드를 실행시키는 `main`이 `client`라고 볼 수 있고, `TestEventService`가 원래 객체라고 볼 수 있다.<br><br>
- `TestEventService`의 `createEvent`메서드와 `publishEvent`메서드에 동일한 기능을 하는 코드를 넣고 싶을 때, 각각 메서드에 넣어주는 것은 효율성이 떨어진다.<br><br>
- 이럴 때 프록시 패턴을 사용한다.<br><br>
- 프록시 객체는 원래 객체와 같은 `interface`를 구현해줘야 한다.<br>
원래 객체를 주입받아서, `interface`의 메서드들을 위임받아 사용하고, 원하는 추가 코드를 넣어주면 된다.<br><br>

```java
package com.basic.applicationContext;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Service;

@Primary //client에서 EventService를 주입받아 메서드를 호출하면 @Primary 어노테이션으로 인해, 프록시 객체가 호출된다.
@Service
public class ProxyTestEventService implements EventService{//원래 객체와 같은 interface를 구현해줘야함.

    @Autowired
    TestEventService testEventService; //이론적으로는 interface 타입의 bean을 주입받는걸 추천
                                       //그러나, 프록시는 원래 객체의 bean을 주입받아 사용 해야 한다.

    @Override
    public void createEvent() {
        long begin =  System.currentTimeMillis(); // 새로 추가한 기능
        testEventService.createEvent(); // 원래 객체의 기능 위임
        System.out.println(System.currentTimeMillis()-begin);//새로 추가한 기능
    }

    @Override
    public void publishEvent() {
        long begin =  System.currentTimeMillis(); // 새로 추가한 기능
        testEventService.publishEvent(); // 원래 객체의 기능 위임
        System.out.println(System.currentTimeMillis()-begin);//새로 추가한 기능
    }
}
```
### 스프링 AOP
이렇게 해주면, 원래 코드에 손을 쓰지 않고도 기능을 추가 할 수는 있지만  
프록시 객체에 중복코드가 발생할 수 있고, 다른 클래스에서도 동일한 기능을 사용하고자 할 때,<br>
매번 코딩을 해줘야하는 부분에서 효율적이지 못하다.<br><br>
이런 문제를 해결해주는게, 런타임시, 동적으로 프록시객체를 만들어주는 것인데, 그것이 <b>스프링 AOP</b>이다.
<br><br>
위의 예제로 살펴보면,
1) `TestEventService`가 `Bean`으로 등록이 되면,
2) 스프링이 `AbstractAutoProxyCreator`라는 <br>
`BeanPostProcessor`(어떤 `Bean`이 등록되면, 그 `Bean`을 가공할 수 있는 `life-cycle interface`)로 `TestEventService`라는 `Bean`을 감싸는 프록시 `Bean`을 만들어,<br>
3) 그 프록시 `Bean`을 `TestEventService`대신에 등록해준다.<br><br>
- 스프링 `AOP`를 사용해주기 위해서는 `pom.xml`에 의존성을 추가해주어야 한다.<bR><br>
```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>5.0.9.RELEASE</version>
        </dependency>
        
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.8.6</version>
        </dependency>
        
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.6</version>
        </dependency>
```
Aspect 클래스를 만들어준다.
```java
package com.basic.applicationContext;

import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Component// bean으로 등록
@Aspect  // Aspect 클래스임을 나타냄
public class PerfAspect {
    
}
```
`Asepect`에는 2가지 정보가 필요하다.<br>
`pointcut`(어디에 적용할 것인지)과 `advice`(해야할 일,기능)이다.<br><br>
`public Object logPerf` 자체가 하나의 `advice`이며, 이 `advice`가 어디에 적용될 것인지(`pointCut`) `@Around` 어노테이션으로 표시해준다.<br><br>
`@Around("execution( * 패키지.interface.메서드)")`
```java
package com.basic.applicationContext;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.stereotype.Component;

@Component// bean으로 등록
@Aspect  // Aspect 클래스임을 나타냄
@EnableAspectJAutoProxy
public class PerfAspect {

    @Around("execution(* com.basic.*(..))")// 이 Advice를 어떻게 적용할 것인가?/PonitCut을 적어줌.
    public Object logPerf(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{// ProceedingJoinPoint: 이 advice가 적용되는 대상.
        long begin = System.currentTimeMillis();
        Object retVal = proceedingJoinPoint.proceed(); // target에 해당하는 메서드를 호출
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }

}
```
- `execution`말고 어노테이션을 만들어서 사용할 수도 있다.<br>
어노테이션 파일을 만들고 `@Retention` 어노테이션을 `CLASS`타입으로 설정해준다.
```java
package com.basic.applicationContext;

import java.lang.annotation.*;

@Documented//javaDoc 만들 때,Documentation이 되도록
@Target(ElementType.METHOD)// 메서드를 타겟으로 잡음.
@Retention(RetentionPolicy.CLASS)//이 어노테이션 정보를 얼마나 유지할 것인지.(즉, 이경우 .Class파일까지 유지하겠다는 뜻)
public @interface PerfLogging {

}
```
```java
@Retention(RetentionPolicy.SOURCE)//이 경우 컴파일시 사라짐
public @interface PerfLogging {

}
```
- 사용할 곳에 만들어준 어노테이션을 붙여준다.
```java
package com.basic.applicationContext;

import org.springframework.stereotype.Service;

@Service
public class TestEventService implements EventService{
    @PerfLogging
    @Override
    public void createEvent() {
        System.out.println("Create Event");
    }
    @PerfLogging
    @Override
    public void publishEvent() {
        System.out.println("Publish Event");
    }
}
```
`Asepct` 클래스의 `@Around` 어노테이션에 `execution`대신 `@annotation`을 사용한다.<br><br>
```java
package com.basic.applicationContext;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.stereotype.Component;

@Component// bean으로 등록
@Aspect  // Aspect 클래스임을 나타냄
@EnableAspectJAutoProxy
public class PerfAspect {

//    @Around("execution(* com.basic.*(..))")// 이 Advice를 어떻게 적용할 것인가?/PonitCut을 적어줌.
    @Around("@annotation(PerfLogging)")//@PerfLoggin 어노테이션이 달려 있는 곳에 이 advice를 적용하라는 뜻.
    public Object logPerf(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{// ProceedingJoinPoint: 이 advice가 적용되는 대상.
        long begin = System.currentTimeMillis();
        Object retVal = proceedingJoinPoint.proceed(); // target에 해당하는 메서드를 호출
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }

}
```
- `execution` 과 `annotation` 대신 `bean`을 사용할 수도 있다.
```java
package com.basic.applicationContext;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.stereotype.Component;

@Component// bean으로 등록
@Aspect  // Aspect 클래스임을 나타냄
@EnableAspectJAutoProxy
public class PerfAspect {

//    @Around("execution(* com.basic.*(..))")// 이 Advice를 어떻게 적용할 것인가?/PonitCut을 적어줌.
//    @Around("@annotation(PerfLogging)")//@PerfLoggin 어노테이션이 달려 있는 곳에 이 advice를 적용하라는 뜻.
    @Around("bean(testEventService)")// 해당 bean의 모든 public 메서드에 적용
    public Object logPerf(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{// ProceedingJoinPoint: 이 advice가 적용되는 대상.
        long begin = System.currentTimeMillis();
        Object retVal = proceedingJoinPoint.proceed(); // target에 해당하는 메서드를 호출
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }

}
```
- `@Around` 어노테이션은 강력한 편이고,
- 간단히 사용할 때는 `@Before`,`@AfterReturning`,`@AfterThrowing` 등을 사용해도 된다.
```java

```