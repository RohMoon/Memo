## RollingFileAppender

`RollingFileAppender`는 특정 크기 제한에 도달하거나 날짜 / 시간 패턴이 더 이상 적용되지 않으면 로그 파일을 롤오버하는 파일 첨부 프로그램입니다.  

이 글에서는 `RollingFileAppender`하여 이전 로그 파일을 백업하고 압축하는 방법을 설명합니다  

`RollingFileAppender`는 `FileAppender`를 상속하여 로그파일을 `Rollover`한다.  

### 여기서 RollOVer란?  
타깃 파일을 바꾸는 것으로 이해할 수 있다.  
예를 들어서 RollingFileAppender가 타깃 파일로 log.txt에
로그 메시지를 append 하다가 어느 지정한 조건에 다다르면 타깃 파일을 다른 파일로 바꿀 수 있습니다.

### RollingFileAppender와 함께 동작하는 두가지 Component가 존재합니다.
1. 첫번째는 `RollingPolicy`로 `rollover`에 필요한 action을 정의한다. = > 어떤 동작을 해줄까?
2. 두번째는 `TriggeringPolicy`로 어느 시점에 `rollover`가 발생할지 정의 한다. = > 언제 어떨때 해줄까?

- 간단히 `RollingPolicy`는 What, `TriggeringPolicy`는 When을 담당하고 있다고 보면 된다.  
  

- `RollingFileAppender`를 사용하기 위해서는 `RollingPolicy`와 `TriggeringPolicy`가 모두 필수적으로 필요하다.  
하지만, `RollingPolicy`는 `TriggeringPolicy` 인터페이스의 구현체로, 하나의 `RollingPolicy`만 지정하여도 사용 가능하다.

### RollingFileAppender는 아래의 Properties를 갖는다.

    
   |용어|속성|설명
   |:---:|:----:|:----|
   |`file            `    |`String               `   |타깃 파일의 이름
   |`append          `    |`boolean(default:true)`   |`append` 정책, `true`:이어쓰기, `false` :덮어쓰기
   |`encoder         `    |`Encoder              `   |로그 이벤트가 `OutputStreamAppender`에 기록되는 방식
   |`rollingPolicy   `    |`RollingPolicy        `   |`Rollover`발생 `RollingFileAppender`의 행동
   |`triggeringPolicy`    |`TriggeringPolicy     `   |`rollover `활성화 시점
   |`Predent         `    |`boolean              `   |`FileAppender`와 동일.
   |                      |                          |`FixedWindowRollingPolicy` 는 `prudent mode`를 지원하지 않는다.
   |                      |                          |`RollingFileAppender`는 `TimeBasedRollingPolicy`일 경우 두가지 제한 조건에 대해 Prudent mode를 제공한다.
   |                      |                          |1. 파일 압출 불가능
   |                      |                          |2. file 프로퍼티는 설정되지 않아야한다.

## Rolling Policies
`RollingPolicy`는 파일 `move와 renaming을 포함한 rollover 절차를 담당합니다.  
=> RollingPolicy가 What을 담당하기 때문에.

RollingPolicy의 인터페이스는 다음과 같습니다.
```java
package ch.qos.logback.core.rolling;

import ch.qos.logback.core.FileAppender;
import ch.qos.logback.core.spi.LifeCycle;

public interface RollingPolicy extends LifeCycle {

    public void rollover() throws RolloverFailure;
    public String getActiveFileName();
    public CompressionMode getCompressionMode();
    public void setParent (FileAppender appender);
    }
```
- `rollover` 메서드는 현재의 로그 파일을 아카이빙하는 과정을 포함하여 rollover 작업을 수행한다.
- `getActiveFileName` 메서드는 현재의 로깅이 이루어지는 타깃 파일 이름을 가져옵니다.
- `getCompressionMode` 메서드를 통해 RollingPolicy의 압축 모드를 확인할 수 있습니다.
- `setParent` 메서드를 통해 부모(`FileAppender`)에 대한 참조가 가능합니다.

## TimeBasedRollingPolicy

`TimeBasedRollingPolicy는` 가장 잘 알려진 `RollingPolicy` 종류 중 하나입니다.

***시간에 기반하여 `rollover` 정책을 정의할 수 있으며, 주로 일 또는 월 ` 단위로 rollover합니다 .***

***TimeBasedRollingPolicy는 rollover뿐만 아닌 Trigger에 대한 책임도 지기 때문에 RollingPolicy와 TriggeringPolicy 인터페이스를 모두 implements 합니다.***

`TimeBasedPolicy`는 필수적으로 `fileNamePattern` 속성을 가집니다.  
아래는 `TimeBasedPolicy` 가지는`properties`입니다.

1. fileNamePattern             `String(default %d:yyyy-MM-dd)`:
   - 아카이브 될 로그 파일의 패턴을 정의합니다. %d 문자를 이용해 파일의 적절한 부분을 dateTime패턴으로 치환하도록 합니다.

   - `FileAppender`의 `file` 프로퍼티를 통해 활성 로그 파일의 위치와 보관 될 로그 파일의 위치를 분리할 수 있습니다. file프로퍼티로 등록된 곳이 활성 로그 파일의 위치가 됩니다.

`%d{}`내부의 `dataTime` 패턴 안에 `'/'` 또는 `'\'` 기호는 디렉토리 분리자로 인식합니다. 이를 통해 시간으로 원하는 디렉토리 구조를 구성할 수 있습니다.

ex)`/var/log/%d{yyyy/MM}/myapplication.%d{yyyy-MM-dd}.log`

2. maxHistory                   int:  
- 아카이브에 저장 유지할 로그 파일의 개수를 지정합니다.  
예를 들어 rollover를 1개월 마다 하며 값을 6으로 지정했다면, 6개월의 히스토리가 남게 됩니다. 다음 월의 파일이 아카이브 될 경우 오래된 파일이 삭제됩니다.


3. totlaSizeCap                 int
: 로그 파일 아카이브 저장소의 최대크기를 지정합니다.
totalSizeCap을 초과한다면 가장 오래된 파일이 삭제됩니다.  
maxHistory와 함께 쓰일 경우 1순위로 maxHistory에 대하여 처리된 후 totalSizeCap이 적용됩니다.  


4. cleanHistoryOnStart          booelan
: Application이 시작될 때 아카이브된 로그 파일을 모두 지웁니다.
false로 지정되면 삭제하지 않고 시작됩니다.  


5. fileNamePattern에 명시된 dateTime 패턴의 최소 단위에 따라 rollover단위가 달라집니다.  

아래의 예시를 통해 rollover가 trigger 되는 시점을 확인하시기 바랍니다.
 - /foo.%d - default %d는 yyyy-MM-dd 입니다. 매일 자정에 새로운 로그 파일로 rollover 합니다.
 - /foo/%d{yyyy/MM}/bar.txt 매월 새로운 디렉토리를 만들며 하위에 bar.txt. 파일로 rollover합니다.
 - /foo/bar.%d{yyyy-MM-dd_HH-mm} 매 분 새로운 로그 파일로 rollover 합니다.
 - /foo/bar.%d.gz 매일 새로운 로그 파일로 rollover하고 이전 로그파일은 GZIP 으로 압축합니다.

```xml
    <configuration>
     <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
      <file>logFile.log</file>
      <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <!-- daily rollover-->
        <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>

        <!-- keep 30 days' worth of history capped at 3GB total size -->
        <maxHistory>30</maxHistory>
        <totalSizeCap>3GB</totalSizeCap>

      </rollingPolicy>

      <encoder>
        <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
      </encoder>f
    </appender>

    <root level="DEBUG">
      <appender-ref ref="FILE" />
    </root>
   </configuration>
   ```

위의 configuration을 살펴봅시다.  
`TimeBasedRollingPolicy`를 적용하였으며 `file태그`와 `fileNamePattern태그`를 함께 사용하고 있습니다.
   
`Application`이 동작중일 땐 활성화된 `logFile.log`파일에 로그를 쌓지만, 매일 자정이 지나면 `logFile.2020-10-03.log`와 같은 이름으로 아카이브 됩니다.  
아카이브 되는 파일의 개수는 30개이며, 아카이브 된 파일의 크기는 총 3GB를 넘을 수 없습니다.  
이 조건을 만족하지 못하면 아카이브 된 로그 파일 중 가장 오래된 파일을 삭제 합니다.

## SizeAndTimeBasedRollingPolicy

`SizeAndTimeBasedRollingPolicy` 는 파일의 크기까지 고려한 `TimeBasedPolicy를 상속한 `RollingPolicy`이다.  
이전 `timeBasedPolicy` 속성 중에 `totalSizeCap`을 이용하면 전체 아카이브 된 로그 파일의 크기를 제한할 있습니다. 하지만 이 `Policy`는 각각의 로그 파일에 대한 크기를 제한 합니다.
```xml
  <configuration>
    <appender name="ROLLING" class="ch.qos.logback.core.rolling.RollingFileAppender">
     <file>mylog.txt</file>
     <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRolloingPolicy">
     <!-- rollover daily -->
     <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
     <!-- each file should be at most 100MB, Keep 60days worth of history, but at most 20GB -->
     <maxFileSize>100MB</maxFileSize>
     <maxHistory>60</maxHistory>
     <totalSizeCap>20GB</totalSizeCap>
    </rollingPolicy>
    <encoder>
     <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="DEBUG">
    <appender-ref ref="ROLLING"/>
  </root>
</configuration>
```

`SizeAndTimeBasedRollingPolicy`를 이용한 `configration`을 함께 살펴봅시다.  
`TimeBasedRollinPolicy`와 다른점은 `fileNamePattern`에서 `%i`와 `%d` 가 필수적인 토큰이라는 것.  
그리고 `maxFileSize`라는 태그가 추가되었습니다.  
`maxFileSize태그`는 각각 로그파일이 가질 수 잇는 최대 크기를 제한 합니다.  
활성화된 로그 파일의 크기가 `maxFileSize`에 다다르면 해당 로그파일을 아카이브 하며, 이때 `%i`토큰이 `index`로 작용하여 다음 로그 파일의 이름을 결정합니다.  
나머지 속성들은 `TimeBasedRollingPolicy`와 동일합니다.

## FixedWindowRollingPolicy

`FixedWindowRollingPolicy`는 `Fixed Window` 알고리즘에 따라 로그 파일의 이름을 지정합니다.
`fileNamePattern`은 파일의 이름 패턴을 지정하며 반드시 `%i` 토큰이 필요합니다.

|이름|속성|설명|                                                                                                                                                     
|---------------|-----------------|--------------------- |                                                                                                                            
|`minIndex`       | `int   `      |window index의 최소값  |                                                                                                        
|`maxIndex`       | `int   `      |window index의 최대값  |                                                                                                        
|`fileNamePattern`| `String`      |`FixedWindowRollingPolicy`를 통해 지어지게된 로그 파일 이름입니다.   |                      
|               |                 |반드시 `window index`를 위해 `%i`토큰을 반드시 포함하고 있어야 합니다. |         
|               |                 |아카이브할 로그 파일을 압축 하고 싶다면 `.zip`이나 `.gz`을 끝에 붙여 처리할 수 있습니다.|              
|               |                 |아래의 샘플 `configuration` 파일을 보며 로그파일이 어떻게 만들어지는지 살펴봅시다.|

``` XML
<configuration>
 <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
  <file>test.log</file>

  <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
   <fileNamePattern>tests.%i.log.zip</fileNamePattern>
   <minIndex>1</minIndex>
   <maxIndex>3</maxIndex>
  </rollingPolicy>

  <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
   <maxFileSize>5MB</maxFileSize>
  </triggeringPolicy>
  <encoder>
   <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
  </encoder>
 </appender>

 <root level="DEBUG">
  <appender-ref ref="FILE" />
 </root>
```

- `rollover`가 한번도 일어나지 않았을 때 `file 태그`의 이름으로 활성화된 로그 파일 (`test.log`)만이 존재합니다.
- `rollover`가 **1번** 일어나면, 아카이브된 로그 파일의 이름은 `minIndex=1`로부터 시작되어서 `test.1.log.zip`으로 만들어집니다. 여전히 활성 로그파일의 이름은 `test.log`입니다.
- `rollover`가 **2번** 일어나면, 이전의 `test.1.log.zip` 파일의 이름이 `test.2.log.zip`으로 수정됩니다. 그리고 새로 아카이브 될 로그파일의 이름이 `test.1.log.zip`으로 지어집니다.
- `rollover`가 **4번** 일어나면, 기존에 아카이브 된 로그 파일 `test.1.log.zip`, `test.2.log.zip`, `test.3.log.zip` 중 `maxIndex`에 해당하는 `test.3.log.zip`파일이 *삭제* 됩니다.  
그리고 각 파일들이 다음 `index` 파일명으로 `renaming` 됩니다.  
새로 아카이브 될 로그파일의 이름은 항상 `test.1.log.zip` 으로 지어집니다.

## Triggering Policies

`TriggeringPolicy`는 언제 `rollover`가 발생할지 정의합니다.   
`TriggeringPolicy`의 인터페이스는 아래와 같습니다.

```java
package ch.qos.logback.core.rolling;

import java.io.File;
import ch.qos.logback.core.spi.LifeCycle;

public interface TriggeringPolicy<E> extends LifeCycle {

    public boolean isTriggeringEvent(final File activeFile, final <E> event );
}

```
- `isTriggeringEvent` 메서드는 `activeFile`과 현재의 로깅 이벤트를 파라미터로 전달 받습니다.  
메서드 내부에서는 `rollover`가 일어날 조건을 판단하여 `rollover` 발생 혹은 현재 로그 파일 유지 여부를 반환합니다.

## SizeBasedTriggeringPolicy

`SizeBasedTriggeringPolicy` 는 현재 활동 로그 파일의 크기를 판단의 요소로 사용합니다.  
활성 로그 파일이 명시한 파일 크기보다 크다면 `RollingFileAppender`가 현재 활동 로그 파일을 `rollover`하도록 신호를 보냅니다.

`SizeBasedTriggeringPolicy`는 단 하나의 ***프로퍼티*** 로 `maxFileSize`만을 가지며 설정하지 않았을 때의 ***기본 크기는 10MB***입니다.
`maxFileSize`는 숫자와 함께 파일 크기`(KB, Mb, GB)`의 단위와 함께 사용되어 표기 가능합니다. (5000KB, 5MB, 2GB)

 ```XML
<configuration>
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>test.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
            <fileNamePattern>test.%i.log.zip</fileNamePattern>
            <minIndex>1</minIndex>
            <maxIndex>3</maxIndex>
        </rollingPolicy>

        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <maxFileSize>5MB</maxFileSize>
        </triggeringPolicy>
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n </pattern>
        </encoder>
    </appender>

    <root level= "DEBUG">
        <appender-ref ref="FILE" />
    </root>
</configuration>

```

위의 설정 파일은 `SizeBasedTriggeringPolicy`를 사용하였으며 활성 로그 파일의 크기가 ***5MB 이상 파일***이 되었을 때 `rollover`를 `trigger`합니다.
