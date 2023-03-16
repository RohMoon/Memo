# 프록시 패턴이란?
- 프록시 패턴(Proxy Pattern)은 객체 지향 디자인 패턴 중 하나로, 다른 객체를 대신하여 그 객체의 인터페이스 역할을 하는 객체를 제공하는 패턴.

- 프록시는 실제 객체와 같은 인터페이스를 제공하며, 실제 객체를 대신하여 클라이언트의 요청을 처리.  
이 때 프록시 객체는 요청을 처리하기 전에 전처리 작업을 수행하거나, 실제 객체가 생성되기 전까지는 빈 객체로 대체되어 요청을 처리할 수 없는 경우 대기 상태로 들어갈 수도 있다.

- 스프링에서는 프록시 패턴이 많이 사용한다.  
특히, 스프링 AOP(Aspect Oriented Programming) 기능을 구현하기 위해 프록시 패턴이 사용한다.  
AOP는 애플리케이션의 흩어진 다양한 모듈에서 공통적으로 적용되는 로직을 분리하여 관리할 수 있도록 지원하는 기능이다.  
이를 위해 스프링은 프록시 객체를 사용하여 AOP 기능을 구현합니다.

- 스프링은 DI(Dependency Injection)를 구현하기 위해 프록시 패턴을 사용.  
DI는 객체 간의 의존성을 외부에서 설정하는 기능.  
이 때 스프링은 프록시 객체를 사용하여 의존성을 주입하며, 이를 통해 객체 간의 결합도를 낮추고 유연성을 높이는 효과를 갖는다.
```java
// 인터페이스
public interface UserService {
    void save(User user);
}

// 실제 객체
public class UserServiceImpl implements UserService {
    @Override
    public void save(User user) {
        // User 저장 로직
    }
}

// 프록시 객체
public class UserServiceProxy implements UserService {
    private UserService userService;
    
    public UserServiceProxy(UserService userService) {
        this.userService = userService;
    }
    
    @Override
    public void save(User user) {
        // 전처리 로직
        userService.save(user);
        // 후처리 로직
    }
}

// 클라이언트
public class Client {
    public static void main(String[] args) {
        UserService userService = new UserServiceProxy(new UserServiceImpl());
        userService.save(new User());
    }
}
public class User {
    private String name;

    public User() {
        // 기본 생성자
    }

    public User(String name) {
        this.name = name;
    }

    // Getter, Setter
}
```

- UserService는 인터페이스로 정의되어 있고, UserServiceImpl은 실제 객체로서 UserService를 구현.  
- UserServiceProxy는 UserService 인터페이스를 구현하며, 실제 UserService 객체를 감싸고 있다.

- Client에서는 UserService 인터페이스를 사용하여 User 객체를 저장하는데, 이 때 UserService 인터페이스를 구현한 UserServiceProxy 객체를 사용한다.  
- 이를 통해 UserService 객체의 요청을 전처리 및 후처리할 수 있다.