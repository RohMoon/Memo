# @PostConstruct 와 @PreDestory Annotation 사용하기

--------------------------------------------------
### 언제 사용하는가
- `@PostConstruct`를 사용하면 `WAS`가 뜰 때 한번만 기본값을 세팅해두고 두고두고 사용이 가능하다.
  - `@PostConstruct`는 `WAS`가 뜰 때 `bean`이 생성된 다음 딱 한번만 실행된다.
  - `@PreDestory` 는 컨테이너에서 객체를 제거하기 전에 실행된다.

### @PostConstruct
- `Spring`은 `bean`을 초기화 한 이후에 `@PostConstruct`를 한번만 호출한다.  
즉 `@PostConstruct`는 `WAS`가 뜰 때 `bean`이 생성된 다음 딱 한번만 실행된다.
- `@PostConstruct`를 사용하여 기본 사용자라던가, 딱 한번만 등록하면 되는 key 값 등을 등록하여 사용할 수 있다.
```java
@Compnent
public class DbInit {
    
    @Autowired
    private UserRepository userRepository;
    
    @PostConstruct
    private void init () {
        // 초기화 처리
        User admin = new User("admin", "admin password");
        User normalUser = new User("user", "user password");
        
        userRepository.save(admin, normalUser);
    }
}
```
- 위에서 `UserRepository` 를 초기화 한다음에 `@PostConstruct` 메서드를 실행한다.

### @PreDestroy
- `@PreDestory` 역시 `Spring`이 어플리케이션 컨텍스트에서 `bean`을 제거하기 직전에 단 한번만 실행된다.
```java
@Component
public class UserRepository{
    private DbConnection dbConnection;
    @PreDestroy
    public void preDestroy() {
        //자원 반환 등 종료 처리
        dbConnection.close;
    }
}
```
### java 9 이상에서는 dependency 추가
```XML
<dependency>
    <groupId>javax.annotation</groupId>
    <artfactId>javax.annotation-api</artfactId>
    <version>1.3.2</version>
</dependency>
```

- 단 한번만 실행하면 되는 메소드라면 `@PostConstruct` and `@PreDestroy` 어노테이션을 쓰면 처리가 쉽게 가능하다.