## Reflection이란?

### Relflection 이란?
***구체적인 클래스 타입을 알지 못해도 그 클래스의 메서드, 타입, 변수들에 접근할 수 있또록 해주는 자바 API***
- 런타임에 지금 실행되고 있는 클래스를 가져와서 실행해야 하는 경우.
- 동적으로 객체를 생성하고 메서드를 호출하는 방법
- 자바의 리플렉션은 클래스, 인터페이스, 메서드들을 찾을 수 있고, 객체를 생성하거나 변수를 변경하거나 메서드를 호출할 수 있다.

#### 어떤 경우에 사용되나
- 코드를 작성할 시점에는 어떤 타입의 클래스를 사용할지 모르지만, 런타임 시점에 지금 실행되고 있는 클래스를 가져와서 실행하는 경우.
- 프리임워크나 IDE에서 이런 동적인 바인딩을 이용한 기능을 제공한다.
  - IntelliJ 자동완성 기능은 알거같은데 어노테이션은 어떻게 동직하는지 잘 모르겠다... 어노테이션을 학습해봐야 알 수 있을듯하다.

### 어떻게 가능한가
- 자바 클래스 파일은 프로그램 생성시에 힙영역에 저장된다.  
그래서 클래스 이름만 알고 있다면 언제든 이 영역에 들어가서 클래스의 정보를 가져올 수 있다.

### 예제
```java
package c;
public class Parent {
    public final static int x = 1;
    void hi(){
        System.out.println("parent hi");
    }
    
    public int bye(int x, boolean y) throws IndexOfBoundsException {
        System.out.println(x + ", " + y);
    }
}
```

```java
package c;
public class Main extends Parent {
  public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, SecurityException, IllegalAccessException, IllegalArgumentException, InvocationTargetException, InstantiationException {
   Class cls = Class.forName("Parent");
   
   //declare if Object is instance of cls
    System.out.println(cls.isInstance(new Object()));
    System.out.println(cls.isInstance(new Main()));
    System.out.println();
  
  
  //Find class methods
  Method[] methList = cls.getDeclaredmethods();
  for (Method m : methList){
      System.out.println("method : " + m.getName);
      System.out.println("class : " + m.getDeclaringClass());
      
      System.out.println("params");
      Parameter[] parList = m.getParameters();
      for (Parameter p : parList){
        System.out.println("type: " + p.getType());
        System.out.println("name: " + p.getName());
      }
      
      Class<?> [] eList = m.getExceptionTypes();
      for (Class e : eList){
          System.out.println("exception : " + e);
      }
  }
  }
}
```


### Java Reflection 사용 실습
- Person 이라는 클래스를 생성하고 리플렉션을 사용해보자.
```java
package c;

class Person {
    int age;
    
    Person(){
        this.age = 27;
    }
    
    Person(int age){
        this.age = age;
    }
    int getAge(){
        return this.age;
    }
}
```

### 생성자 찾기
- getDeclaredConstructor()를 이용해 클래스로부터 생성자를 가져올 수 있다.
```java
package c;
class DeclaredConstructor {
Class clazz = Class.forName("Person");
Constructor constructor = clazz.getDeclaredConstructor();
}
```
 - getDeclaredConstructor()는 인자가 없는 생성자를 가져온다.

### Method 찾기
```java
package c;
class DeclaredMethod {
Class clazz = Class.forName("Person");
Method[] methods = clazz.getDeclaredMethods();
System.out.println(methodsList[0].invoke(clazz.newInstance()));
}
```
- invoke()메서드를 사용하면 Method객체를 실행할 수 있다.  
첫번째 인자는 호출하려는 객체, 두번째 인자는 전달할 파라미터 값을 준다.

### Field 변경
```java
package c;
class DeclaredField {
    Class clazz = person.class;
    Field[] field = clazz.getDeclaredFields();
    
    Person person = new Person();
    field[0].set(person,17);
    System.out.println(field[0].get(person)); // 17이 출력됨
}
```
- set() 메서드를 사용해서 객체의 변수를 변경할 수 있다.

