# [Spring]
## DataBinding - Converter & Formatter 
- `converter`와 `Formatter`는 `DataBinder`가 아닌, `ConversionService`에 등록이 된다.<br>

### Converter
- `PropertyEditor`의 여러 단점(`Stateful`, `String` <-> `Object` 관계의 변환만 가능 etc)들 보완하기 위해 나온 것이 `Converter`이다.<br>
- `String` <-> `Object` 이외에도 타입 변환 가능
- `Stateless` - 쓰레드 safe<br><br>
- `Converter`는 `source`와 `target`을 설정해야줘야한다.
- `converter<source, target>`<br>
`source`는 바꾸기 전 데이터 타입이고,<br>
`target`은 바꿀 데이터 타입이다.<br><br>
- 아래와 같이 `Converter`를 만들어 줄 수 있다.
```java
package com.basic.applicationContext;

import org.springframework.core.convert.converter.Converter;

public class eventConverter {
    // String을 Event 타입으로
    public static class StringToEventConverter implements Converter<String, Event>{

        @Override
        public Event convert(String s) {
            return new Event(Integer.parseInt(s));
        }
    }

    //Event 타입을 String으로
    public static class EventToStringConverter implements Converter<Event, String>{
        @Override
        public String convert(Event event) {
            return event.getId().toString();
        }
    }
}
```
- `converter` 같은 경우는 `stateless`기 때문에 `Bean`으로 등록해서 사용해도 아무 문제 없다.<br><br>
- `ConverterRegistry`에 등록해서 사용하는데,`config`파일을 만들어서 `converter`를 등록해주면 된다.
```java
package com.basic.applicationContext;


public class Event {

    private Integer id;

    private String title; 

    public Event(Integer id) {
        this.id=id;
    }
    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    @Override
    public String toString() {
        return "Event{" +
                "id=" + id +
                ", title='" + title + '\'' +
                '}';
    }
}
```
```java
package com.basic.applicationContext;

import org.springframework.context.annotation.Configuration;
import org.springframework.format.FormatterRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry formatterRegistry) {
        formatterRegistry.addConverter(new eventConverter.StringToEventConverter());
    }
}
```
```java
package com.basic.applicationContext;

import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class EventController {
    
    @InitBinder // 컨트롤러에서 사용할 바인더 등록
    public void init(WebDataBinder webDataBinder){
        // WebDataBinder에 Event 클래스 타입을 처리할 PropertyEditor 등록
        // WebDataBinder에 DataBinder의 구현체 중 하나
        webDataBinder.registerCustomEditor(Event.class,new EventEditor());

    }

    //event/1
    //event/2
    //이런식으로 요청이 들어오면 입력한 숫자를 Event 타입으로 변환하여 스프링이 받는다.
    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event){ //@PathVariable로 Event를 도메인으로 받음.
        System.out.println(event);
        return event.getId().toString();
    }
}
```
```java
package com.basic.applicationContext;

import org.springframework.core.convert.converter.Converter;

public class EventConverter {
    // String을 Event 타입으로
    public static class StringToEventConverter implements Converter<String, Event>{

        @Override
        public Event convert(String s) {
            return new Event(Integer.parseInt(s));
        }
    }

    //Event 타입을 String으로
    public static class EventToStringConverter implements Converter<Event, String>{
        @Override
        public String convert(Event event) {
            return event.getId().toString();
        }
    }

}
```
- 실행하면, `WebConfig` 에 넣어준 `Converter`가 모든 `Controller`에 전부 동작한다.<br><br>
따라서 `EventController`가 만약 `event/1` 요청을 받으면 `EventConverter`를 통해 `Event` 타입으로 변환이 되어  `Controller`에서 `Event` 타입으로 받을 수 있는 것이다.<br><br>
<b Style="color:orange">기본적으로 `Integer` 타입 같은 경우는 기본적으로 등록되어 있는 `Converter`가 자동으로 변환해준다.<br>
즉, 스프링에 기본적으로 등록되어 있지 않은 경우만 `Converter`를 만들어서 사용한다.</b>
<br><br>

### Formatter
웹 쪽에 특화되어 있다. <br><br>
`Formatter`는 처리할 타입 하나를 받고, 2개의 메서드를 구현해주어야 한다.<br><br>

```java
package com.basic.applicationContext;

import org.springframework.format.Formatter;

import java.text.ParseException;
import java.util.Locale;

public class EventFormatter implements Formatter<Event> {
    @Override
    public Event parse(String s, Locale locale) throws ParseException {
        return null;
    }

    @Override
    public String print(Event event, Locale locale) {
        return null;
    }
}

```
```java
package com.basic.applicationContext;

import org.springframework.format.Formatter;

import java.text.ParseException;
import java.util.Locale;

public class EventFormatter implements Formatter<Event> {

    //문자를 받아서 객체로 (Locale 정보를 기반으로)
    @Override
    public Event parse(String s, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(s));
    }
    // 객체를 받아서 문자로 (Locale 정보를 기반으로)
    @Override
    public String print(Event event, Locale locale) {
        return event.getId().toString();
    }
}
```
- `Fomatter`도 `stateless`, 즉 쓰레드 safe 하므로 `Bean`으로 등록해서 사용할 수도 있다.<br>
`Bean`으로 등록할 수 있으면, 다른 `Bean`을 주입 받을 수도 있다.<br><br>
```java
package com.basic.applicationContext;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.format.Formatter;
import org.springframework.stereotype.Component;

import java.text.ParseException;
import java.util.Locale;

@Component // 빈으로 등록
public class EventFormatter implements Formatter<Event> {

    @Autowired // 다른 Bean을 주입받을 수 있다.
    MessageSource messageSource;

    //문자를 받아서 객체로 (Locale 정보를 기반으로)
    @Override
    public Event parse(String s, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(s));
    }
    // 객체를 받아서 문자로 (Locale 정보를 기반으로)
    @Override
    public String print(Event event, Locale locale) {
        return event.getId().toString();
    }
}

```
`Converter`와 비슷하게 `WebConfig`에서 `Formatter` 등록을 해줘여한다.
```java
package com.basic.applicationContext;

import org.springframework.context.annotation.Configuration;
import org.springframework.format.FormatterRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry formatterRegistry) {
        formatterRegistry.addConverter(new EventFormatter());
    }
}
```
```java
    //event/1
    //event/2
    //이런식으로 요청이 들어오면 입력한 숫자를 Event 타입으로 변환하여 스프링이 받는다.
    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event){ //@PathVariable로 Event를 도메인으로 받음.
        System.out.println(event);
        return event.getId().toString();
    }
```
```java

```