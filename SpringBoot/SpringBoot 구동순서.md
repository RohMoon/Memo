### 스프링부트 애플리케이션의 구동 순서는 다음과 같다.

1. SpringApplication 클래스의 정적 run() 메소드를 호출하여 애플리케이션 컨텍스트를 생성합니다.
2. 애플리케이션 컨텍스트를 초기화합니다.
   1. 어플리케이션 컨텍스트를 위한 환경 설정(프로파일, 속성, ...)을 로드합니다.
   2. @Configuration 클래스들을 스캔하여 빈들을 등록합니다.
   3. BeanPostProcessor 등록과 초기화를 수행합니다. 
3. 애플리케이션 컨텍스트를 refresh() 합니다. 
4. 웹 서버를 구동합니다. 
5. 필요한 빈들을 추가로 등록하고 초기화합니다. 
6. 애플리케이션이 구동 완료될 때까지 블로킹합니다. 
 
### 일반적으로 어노테이션들이 적용되는 순서는 다음과 같습니다.

1. @Configuration 어노테이션이 적용된 클래스들이 스캔됩니다.
2. @Import 어노테이션을 사용하여 다른 @Configuration 클래스를 가져올 수 있습니다.
3. @Bean 어노테이션을 사용하여 빈 객체를 등록합니다.
4. @ComponentScan 어노테이션을 사용하여 스프링 컨텍스트가 빈을 검색할 패키지를 지정합니다.
5. @PropertySource, @Value 어노테이션을 사용하여 프로퍼티 파일을 로드하고 값들을 주입합니다.
6. @Profile 어노테이션을 사용하여 특정 환경에서만 빈을 등록할 수 있습니다.
7. AOP(Aspect-Oriented Programming) 관련 어노테이션들은 빈 등록 이후에 적용됩니다.
8. 필터(Filter), 인터셉터(Interceptor) 등과 같은 웹 애플리케이션 관련 어노테이션들은 웹 서버 구동 이후에 적용됩니다.

Spring에서 AOP, 필터, 인터셉터를 적용하는 순서는 다음과 같습니다.

필터(Filter): 필터는 DispatcherServlet 이전에 실행되며, 요청에 대한 처리를 수행하기 전에 요청과 응답을 조작할 수 있습니다. 필터는 web.xml에 등록하거나 Spring Boot에서는 FilterRegistrationBean으로 등록합니다.

인터셉터(Interceptor): 인터셉터는 DispatcherServlet이 Controller를 호출하기 전과 후에 요청과 응답을 가로채서 처리합니다. 인터셉터는 HandlerInterceptor 인터페이스를 구현하여 작성하며, Spring에서는 인터셉터를 등록할 수 있는 WebMvcConfigurerAdapter 또는 WebMvcConfigurer 인터페이스를 사용합니다.

AOP(Aspect-Oriented Programming): AOP는 핵심 로직과 부가적인 관심사를 분리해서 프로그래밍하는 기법입니다. AOP는 스프링에서 AspectJ와 같은 라이브러리를 사용해서 구현할 수 있습니다. AOP는 Proxy를 사용하기 때문에, Target Object를 감싸는 형태로 적용되며, 메소드 실행 전후 또는 예외 발생 시점에 특정 기능을 수행할 수 있습니다.

위의 순서는 전체적인 순서이며, AOP, 필터, 인터셉터는 서로의 영향을 받지 않습니다. 다만, Spring에서 제공하는 Proxy 기반 AOP를 사용할 경우, Bean 객체의 메소드 호출을 Proxy 객체가 감싸게 되는데, 이때 필터와 인터셉터는 Proxy 객체를 대상으로 처리됩니다. 따라서 AOP는 필터와 인터셉터보다 먼저 실행되지만, Proxy 객체에 대한 메소드 호출을 가로채기 때문에 필터와 인터셉터의 처리를 막을 수 있습니다.


Spring Framework의 어플리케이션 컨텍스트는 크게 3가지 단계로 초기화됩니다.

환경(Environment)의 로딩

스프링 어플리케이션 컨텍스트는 Environment 객체를 생성하여 시스템 환경 변수, JVM 시스템 프로퍼티, application.properties 또는 application.yml 등의 설정 파일을 로딩합니다.
빈(Bean) 정의의 로딩과 생성

어플리케이션 컨텍스트는 BeanDefinitionReader를 사용하여 빈(Bean) 정의를 로딩하고, BeanFactory를 생성합니다.
이때, 빈(Bean) 정의를 토대로 해당 빈(Bean)을 생성하진 않습니다.
빈(Bean) 생성 및 초기화

생성된 BeanFactory를 통해 빈(Bean)을 생성하고 초기화합니다.
@PostConstruct나 InitializingBean 인터페이스를 구현한 메서드 등을 사용하여 빈(Bean)을 초기화할 수 있습니다.
AOP, 필터, 인터셉터 등은 빈(Bean) 생성 후에 적용되는 기능입니다. 따라서, 어플리케이션 컨텍스트 초기화 이후에 AOP, 필터, 인터셉터가 적용됩니다. 그러나, 각각의 적용 순서는 다음과 같습니다.

필터(Filter)

서블릿 컨테이너에서 요청(Request)이 들어오면, 요청(Request)을 받는 첫 번째 단계에서 필터(Filter)가 적용됩니다.
AOP(Aspect-Oriented Programming)

AOP는 프록시를 이용하여 대상 메서드의 호출 전/후에 횡단 관심사(Cross-cutting Concerns)를 적용합니다.
따라서, 프록시를 생성하기 위해 빈(Bean)이 필요합니다. 따라서, 빈(Bean) 생성 후에 AOP가 적용됩니다.
인터셉터(Interceptor)

인터셉터는 스프링 MVC에서 요청 처리 과정에서 컨트롤러(Controller)에 진입하기 전/후에 처리되는 기능입니다.
따라서, 컨트롤러(Controller)에 진입하기 전/후에 적용되기 때문에, AOP와 마찬가지로 빈(Bean) 생성 후에 적용됩니다.


스프링 어플리케이션 컨텍스트는 Environment 객체를 가장 먼저 생성합니다. 이때 Environment 객체는 먼저 내부적으로 프로퍼티 소스를 로딩하고, 커스텀 프로퍼티 소스를 추가하고, 시스템 프로퍼티를 로딩하고, 프로필을 처리합니다. 그 후, 스프링 어플리케이션 컨텍스트는 Environment 객체를 사용하여 빈 팩토리를 초기화하고, 빈 팩토리를 사용하여 빈을 생성하고 의존성을 주입합니다. 이러한 과정을 거쳐 어플리케이션 컨텍스트가 초기화됩니다.


Spring은 @Configuration 애노테이션을 사용하여 구성 클래스를 정의하고 이를 스프링 애플리케이션 컨텍스트로 로드합니다. @Configuration 애노테이션이 선언된 구성 클래스가 로드될 때는 다음과 같은 순서로 처리됩니다.

@Configuration 애노테이션이 있는 구성 클래스가 스프링 애플리케이션 컨텍스트에 로드됩니다.
스프링은 구성 클래스에 정의된 @Bean 메서드를 식별합니다.
@Bean 메서드는 호출되고 반환된 객체는 스프링 애플리케이션 컨텍스트에 등록됩니다.
스프링은 의존성 주입을 처리하고 @Autowired 애노테이션이 있는 필드에 해당하는 빈을 찾습니다.
따라서 @Configuration 애노테이션은 애플리케이션 컨텍스트가 로드되기 전에 먼저 처리됩니다. 이후 @Bean 메서드와 @Autowired 애노테이션이 처리됩니다.