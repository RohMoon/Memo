# Resource - AutoWired- Inject 차이

## 의존 객체 자동 주입(Automatic Dependency Injection)

### 의존 객체 자동 주입 (Automatic Dependency Injection)은 스프링 설정파일에서 혹은 태그로 의존 객체 대상을 명시하지 않아도 스프링 컨테이너가 자동적으로 의존 대상 객체를 찾아 해당 객체에 피룡한 의존성을 주입하는 것을 말한다.

`@Resource`, `@Autowired` , `@Inject` 이 태그들의 차이점은 의존 객체를 찾는 방식이 다르다.

### Resource

- Java에서 지원하는 어노테이션이다.
- 특정 프레임 워크에 종속적이지 않다.
- 찾는 순서
    - ` 이름 -> 타입 -> @Qualifier -> 실패`
    - name 속성의 이름을 기준으로 찾는다.
    - 없으면 타입, 없으면 `@Qualifier` 어노테이션의 유무를 찾아 그 어노테이션이 붙은 속성에 의존성을 주입한다.
        - `< context:annotation-config />` 구문을 꼭 xml 설정 파일에 추가해야 한다.
- 사용할 수 있는 위치
    - 멤버 변수, setter 메서드

### Autowired

- Spring에서 지원하는 어노테이션이다.
- 찾는 순서
    - ` 이름 -> 타입 -> @Qualifier -> 실패`
- `Autowired`는 주입하려고 하는 객체의 타입이 일치하는지를 찾고 객체를 자동으로 주입한다.
  - 만약 탕비이 존재하지 않는다면 @Autowired에 위치한 속성명이 일치하는 `@bean`을 컨테이너에서 찾는다.
  - 그리고 이름이 없을 경우 `@Qualifier` 어노테이션의 유무를 찾아 그 어노테이션이 붙은 속성에 의존성을 주입한다.
-`< context:annotation-config />`구문을 꼭 xml 설정파일에 추가해야하 한다.
- 사용할 수 있는 위치
  - 멤버 변수, setter 메서드, 생성자, 일반 메서드에 적용 가능

### Inject
- Java에서 지원하는 어노테이션이다.
- 특정 프레임워크에 종속적이지 않다.
- 찾는 순서
  - `타입-> @Qualifier -> 이름 -> 실패`
- `@Autowired`와 동일하게 작동하지만 찾는 순서가 다르다.
- `@Inject` 사용하기위해서는 `maven`이나 `gradle`에 `javax` 라이브러리 의존성을 추가해야 한다.
- 사용할 수 있는 위치
  - 멤버 변수, setter 메서드, 생성자, 일반 메서드에 적용 가능

### Qualifier
- 만약에 타입이 동일 한 bean 객체가 여러개 있다면 `Spring`이 `Exception`을 일으킨다. (ex. @Autowired를 동일한 타입에 쓴 곳이 있다면)
- 스프링이 어떤 bean을 주입해야 될지 모르기 때문에 스프링 컨테이너를 초기화 하는 과정에서 Exception
  - `@Autowired`의 주입 대상이 한 개여야 하는데 실제로는 두 개 이상의 빈이 존재해 주입할 때 사용할 객체를 선택할 수 없기 때문이다.
  - 단 `@Autowired`가 적용된 필드나 설정 메서드의 `property`이름과 같은 이름을 가진 빈 객체가 존재할 경우에는 이름이 같은 빈 객체를 주입 받는다.
```xml
<context:annotation-config>
    <!-- Member member = new Member() -->
    <bean id="member1" class="example.Member">
        <qualifier value="m1"/>
    </bean>
</context:annotation-config>
```
- 이런식으로 `qualifier`를 추가해준다.
```java
package config;
public class MemberDao{
    @Autowired @Qualifier ("m1")
    private Member member;
    
    public void setMember(Member member){
        this.member = member;
    }
}
```
- `@Qualifier("m1)` 이라고 해줬으니 member1 bean을 사용하겠다고 명시하는 것이다.

#### Exception
- `@Qualifier`에 지정한 한정자 값을 갖는 bean 객체가 존재하지 않으면 `Exception`이 발생한다.
