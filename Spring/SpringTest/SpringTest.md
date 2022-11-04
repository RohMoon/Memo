### @WebMvcTest

- @Controller, @RestController가 설정된 클래스들을 찾아 메모리에 생성한다.

- @Service나 @Repository가 붙은 객체들은 테스트 대상이 아닌 것으로 처리되기 때문에 생성되지 않는다.

- @WebMvcTest가 설정된 테스트 케이스에서는 서블릿 컨테이너를 모킹한 MockMvc타입의 객체를 목업하여 컨트롤러에 대한 테스트코드를 작성할 수 있다.

- @WebMvcTest 어노테이션을 사용하면 MVC 관련 설정인 @Controller, @ControllerAdvice, @JsonComponent와 Filter, WebMvcConfigurer,  HandlerMethodArgumentResolver만 로드되기 때문에, 실제 구동되는 애플리케이션과 똑같이 컨텍스트를 로드하는 @SpringBootTest 어노테이션보다 가볍게 테스트 할 수 있다.

외부 라이브러리 있으면 테스트 임플 해야해요