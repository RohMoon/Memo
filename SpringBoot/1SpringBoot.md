# [Spring Boot ]
## 스프링 부트란?
- 제품 수준의 스프링 기반 어플리케이션을 만들 때 빠르고 쉽게 만들 수 있게 도와주는 것.
- 사용자가 일일히 모든 설정을 직접 하지 않도록, 많이 쓰이는 설정을 제공해준다.(필요시 설정들을 직접 바꿔줄 수 있다.)
- code generation이 없고, XML도 사용하지 않는다.
- java 8이상부터 사용 가능하다.


## 스프링 부트 프로젝트 구조
- src - main - java : 자바 소스코드
- src - main -resource

## 스프링 boot 자동 설정 
### @EnableAutoConfiguration
- 메인 클래스에 붙어 있는 `@SpringBootApplication`은 크게 3가지가 합쳐진 것이라고 생각할 수 있다.
  1. @SpringBootCofiguration
  2. @ComponentScan
  3. @EnableAutoConfiguration
- 스프링 부트 어플리케이션은 `Bean`을 2번 등록한다. 처음에 `ComponentScan`으로 등록하고, <bR>
그 후에 `EnableAutoConfiguration`으로 추가적인 `Bean`들을 읽어서 등록한다.

#### @ComponentScan
- `@ComponentScan`은 해당 패키지에서 `@Component` 어노테이션을 가진 Bean들을 스캔해서 등록하는 것이다.<br>
  (`@Configuration`, `@Repository`, `@Service`, `@Controller`, `@RestController` 포함)
> #### Component란 ?<br>
> 구성요소라는 뜻으로 컴포넌트는 독립적인 단위 모듈이다. 유저가 사용하는 시스템에 대한 조작장치를 이야기한다.<br>
> #### @Component란? <br>
> 개발자가 직접 작성한 Class 를 Bean으로 만드는 것이다.<bR>
> 싱글톤 클래스 빈으로 생성하는 어노테이션이다.<br><br> 물론 @Scope를 통해 싱글톤이 아닌 방식으로도 생성이 가능하다.<br><br>
> 이 어노테이션은 선언적(Declarative)인 어노테이션이다.<br>
> 즉, 패키지 스캔 안에 이 어노테이션은 <b>"이 클래스를 정의했으니 빈으로 등록하라"</b>라는 뜻이 된다.<bR>
> * Componentscan : Component 어노테이션이 붙은 클래스들을 검색한다.<br>
> #### @Bean 이란 
> 개발자가 작성한 `Method`를 통해 반환되는 객체를 `Bean`으로 만드는 것이다.<bR>
> 주로 `@Configuration` 어노테이션이 들어간 `Spring`을 설정하는 클래스 내에 들어가는 메서드에서 선언한다.<br>
> @Component로 따지면,<br>스프링은 스캔할 패키지를 검색해서 @Component 어노테이션을 발견하면 이렇게 등록하는 꼴 이된다.<br>
> <br>
> 설정 할 때에 `XML`로 하는 장점이라면?<br>
> 바로 넣었다가 뺏다, 클래스 바꾸는 등의 유연한 설정이 가능하다는 이점이다.<br>
> 그냥 `XML`파일 편집하고 앱을 재시작하면 끝이다. 간단하다, 필요없는 빈은 주석처리하면 된다.<br>
> `XML` 이나 클래스나 수정하면 앱을 재시작 해야하는건 마찬가지다. 또한 스캔할 필요 없이 빈등록이 빠르게 이루어진다.

#### @EnableAutoConfiguration
- `AutoConfiguration`은 결국 `Configuration` 이다. 즉, `Bean`을 등록하는 자바 설정 파일이다.<br><br>
- `spring.factories` 내부에 여러 `Configuration`들이 있고, 조건에 따라 `Bean`을 등록한다.<br>
- 따라서 메인 클래스 (`@SpringBootApplication`)을 실행하면,<br>
- `@EnableAutoConfiguration` 에 의해 `spring.factories` 안에 들어있는 수많은 자동 설정들이 조건에 따라 적용이 되어 수 많은 `Bean`들이 생성되고,<br>
스프링 부트 어플리케이션이 실행되는 것이다.<br><br>