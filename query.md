# DB Query
기존 오라클만 사용했다가 프로젝트에서 PostGreSQL을 사용하게 되었고, 오라클과는 다르게 PL/pgSQL을 오라클 내에서 사용하는 SQL 문과는 다른 식으로 함수가 구성되어 있어서 체크함.

## Procedure
### Stored Procedure 장점
1. 여러 개의 Select, Update, Insert 등과 같은 Query문을 Stored Procedure문을 통해 하나도 묶어 실행함으로써 서버와 클라이언트 간의 Round Trip의 개수를 줄여준다.
   - Round Trip을 줄이면 불필요한 네트워크 통신 횟수를 줄여 더 빠르게 DBMS 연산 결과를 얻을 수 있다.
   - SELECT나 UPDATE, INSERT, DELETE 문 등을 java의 JDBC등을 이용해 실행하게 되면, Query문을 해석하고 실행하기 앞서 많은 준비가 필요한데,  Stored Procedure는 ___이미 컴파일이 되어 DBMS단에서 바로 실행 할 수 있도록 준비되어 있어 그 실행 속도가 매우 빠르고 ,함수 호출 시 인자만을 변경하여 빠르고 쉽게 재활용이 가능하다.___
   
### Procedure 단점
1. Stored Procedure를 C나 PL/pgSQL과 같은 프로그래밍 언어로 개발해야하므로 특별한 스킬을 요구하고, 문제 발생시 디버깅이 어렵다.
2. Stored Procedure는 표준인 SQL과 다르게 DBMS마다 다르므로 DBMS가 변경되면 해당 DBMS에 맞게 Stored Procedure를 다시 개발해야 한다.

## PL/pgSQL


1. PL/pgSQL을 이용한 간단한 stored Procedure
```postgresql
CREATE FUNCTION function_name(param1 type, param2 type)
RETURNS return_type AS
    BEGIN
    -- code
    END;
LANGUAGE language_name; 
```
- `function_name` 은 함수 이름.
- `param1 type`과 `param2 type`은 함수의 인자를 의미.  
인자 2개의 이름이 `param1`, `param2`이고, ___인자명 뒤에 인자의 타입___ 이 온다.  
- `return_type`에는 이 함수의 ___반환값의 타입___ 을 의미.
- `BEGIN`과 `END` 사이의 `--code`부분에 이 함수가 실행할 코드를 입력한다.
- language_name에는 함수에 대한 프로그래밍 언어가 무엇인지를 나타낸다.  
PL/pgSQL 언어를 사용하기에 항상 plpgsql을 입력한다.

기본 문법만 보았을때는 기존 사용했던 오라클에서와 다른 점은 `Language`를 명시해주는 것과 `RETURNS`에 대한 부분 이 다르다.   

기본 문구를 활용해서 2개의 정수를 입력 받아 합한 결과를 반환해 주는 Function
```postgresql
CREATE FUNCTION add(a INTEGER, b INTEGER)
RETURNS INTEGER AS
$$ BEGIN
RETURN a+b;
END; $$
LANGUAGE plpgsql;
```  
실행 문으로는
```postgresql
SELECT add(100,200);
```

2. PL/pgSQL에서의 함수 인자(Parameter)
- 사용자 정의 함수를 생성하는 CREATE FUNCTION 구문의 예
```postgresql
CREATE FUNCTION add(a INTEGER, b INTEGER)
RETURNS INTEGER AS
$$ BEGIN 
RETURN a+b;
   END;$$
LANGUAGE plpgsql;
```
오라클에서와 동일하게 `CREATE FUNCTION`을 `CREATE OR REPLACE FUNCTION`코드로 대체 할 수 있고 기능 또한 동일하다.
```postgresql
CREATE OR REPLACE FUNCTION add(a INTEGER, b INTEGER)
   RETURNS INTEGER AS
$$ BEGIN
   RETURN a+b;
END;$$
   LANGUAGE plpgsql;
```

위의 FUNCTION에서 `add`라는 이름의 함수는 정수형 타입 `INTEGER`인 `a`와 `b`를 갖는다. 기본적으로 인자는 `IN`의 형식이다. (`IN`,`OUT`이 기재되어 있지않다면)
`IN`형식 이외에 `OUT`과 `INOUT`형식도 있는데,  
`IN`형식은 함수에 값만 전달해주는 기능을 하고,  
`OUT`은 함수 종료시 이 인자에 값을 전달해 외부에 반환해주는 기능을 갖고 있다.
`INOUT`은 위에 설명한 `IN` `OUT`의 기능 모두를 갖고 있다.

- OUT 형식의 함수 인자를 사용하는 예
```postgresql
CREATE OR REPLACE FUNCTION getmaxmin (
    v1 NUMERIC,
    v2 NUMERIC,
    OUT min_value NUMERIC,
    OUT max_value NUMERIC)
AS
$$ BEGIN
    min_value := GREATEST(v1,v2);
    max_value := LEAST(v1,v2);
END;$$
    LANGUAGE plpgsql;
```  
`getmaxmin` 이라는 함수에서 인자를 4개를 받고 있다. 첫번째 두번째 인자는 별도의 지정이 없으니 `IN`형식으로,  
`min_value`와 `max_value`는 `OUT` 형식으로 지정되어있다.  
첫번째, 두번째 지정된 안자값 중 최대값을 `max_value`의 인자로 넘기고, 최소 값은 `min_value`의 인자로 넘긴다.  
이 앞전의 예와는 다르게 `RETURNS` 구문이 사용되지 않았는데, 이유는 별도의 반환 값을 지정하기 위해서 사용하지 않고,
`OUT` 형식으로 지정된 인자에 값을 반환하게 된다.

- INOUT 형식의 함수 인자를 사용하는 예
```postgresql
CREATE OR REPLACE FUNCTION getmaxmin2(
    INOUT v1 NUMERIC,
    INOUT v2 NUMERIC)
    AS
    $$ DECLARE 
        v3 NUMERIC := v1;
        BEGIN
   v1 := GREATEST(v2,v3);
   v2 := LEAST(v2,v3);
end; $$
LANGUAGE plpgsql;
```  
이 `getmaxmin2` 함수는 `INOUT`형식의 인자를 2개 갖고 있는데, 이 2개의 인자 값에서 최대 최소값을 다시 이 2개의 인자들에 각각 값을 기록해 반환한다.
때문에 102번, 103번 줄에서 `v3`라는 변수 하나를 정의해서 사용하고 있다. 

- VARIADIC 형식의 인자를 갖는 예  
`IN`, `OUT`, `INOUT` 형식 이외에 `VARIADIC` 형식은 가변인자로써 데이터 타입의 여러개의 인자들을 한번에 지정할 수 있다.

```postgresql
CREATE OR REPLACE FUNCTION  sum(VARIADIC params numeric[])
RETURNS numeric AS
$$ DECLARE 
    res numeric :=0;
    BEGIN
For i IN 1 .. array_length(params,1) LOOP
        res := res + params[i];
   end loop;
    return res;
end; $$
LANGUAGE plpgsql;
```
위 코드에서 `params`인자는 `VARIADIC`형식이고 타입은 NUMERIC[] 이다.  
지정한 인자의 개수는 `array_length` 내장 함수를 사용해 얻을 수 있다.  
FOR 반복만을 사용하는데, 1부터 인자의 개수만큼 반복하고 있다.  
이 함수는 여러개의 숫자들로 인자를 지정해서 합한 결과를 반환한다.

3. 변수와 상수
- 변수는 ___변수 이름___ 과 어떤 타입을 다를 건지의 ___변수타입___ 을 지정해야한다.  
- 변수의 이름은 개발자에게 의미있는 이름으로 부여할 것.  
- 값의 타입은 integer, numeric, char, varchar 등과 같이 각각 정수, 실수, 문자, 가변길이 문자 등으로 지정 가능하다.
- 변수 선언시에 기본값 지정 가능.
- 변수 선언시에 기본값 지정하지 않는다면 NULL 값을 갖게 된다.

- 여러 개의 변수를 선언하는 PL/pgSQL 코드
 ```postgresql
DO $$
    DECLARE
        age integer := 40;
        korean_name varchar(10) :='김형준';
        alias_name varchar(50) :='Dip2K';
        weight numeric(3,1) :=65.5;
        BEGIN
       RAISE NOTICE '%의 별명은 % 이고 나이는 %이며 몸무게는 % 입니다.',
           korean_name, alias_name , age,weight;
    end;
        $$
```  
위 코드에서는 `DECLARE` 구문 바로 뒤에 변수를 선언하고 있다. 4개의 변수를 선언하고 있고,  
초기 값이 40인 정수형 변수 `age`와 `'김형준'`값을 가지는 가변길이 10인 문자형 변수 `korean_name`,  
그리고 `65.5`값을 가지는 실수형 변수 `weight`를 갖는다.  
실수형 타입을 지정하기 위해서 `numeric(n,m)`으로 선언하는데, `n`은 __전체자리이고__, `m`은  __소수점 자리수__ 이다.    
`RAISE NOTICE`는 해당 변수들을 원하는 문자열로 포맷으로 구성한 것을 PL/pgSQL의 실행환경에서 표시하는 코드이다.

- 시간(time) 타입 코드
```postgresql
DO $$
    DECLARE 
        current_time time := now();
        BEGIN
RAISE NOTICE '%', current_time;
end;$$
```

- 변수의 데이터 타입을 결정할 때 기존에 존재하는 테이블의 특정 필드의 데이터 타입을 가져와 그 데이터 타입으로 변수의 타입을 지정해할 필요가 있다.
예로 `employee` 라는 테이블의 `salary`라는 필드의 데이터 타입을 가져와 `my_salary`라는 변수를 선언하는 코드는 아래와 같다.
```postgresql
my_salary employee.salary#TYPE := 100000.12;
```
동일한 대상의 값을 담고 있는 변수에 대해 또 다른 이름(별칭)을 붙일 수 있다.
그에 대한 예를 아래 코드를 참조.
```postgresql
DO $$
    DECLARE 
        current_time time := now();
        old_time ALIAS FOR current_time;
        BEGIN
RAISE NOTICE '%', old_time;
end;$$
```
- 상수는 변수와 동일하게 `DECLARE` 구문 뒤에서 이루어지며, 상수 이름과 타입 사이에 CONSTANT라고 지정하여 사용한다.
```postgresql
DO $$
    DECLARE 
        vat CONSTANT numeric := 0.1;
        cost numeric := 100000;
        real_cost numeric;
        
        BEGIN 
        real_cost = cost + cost + vat;
        RAISE NOTICE '부가세를 적용한 실제 가격은 % 입니다.', real_cost;
end;$$
```
위 코드에서는 `vat`가 상수로 지정되어있다. 

5. IF 조건문  
PL/pgSQL에서 제공하는 조건문은 IF문과 CASE 문으로 크게 구분할 수 있다.  
- IF문 문법
```postgresql
IF <조건> THEN
<참의 실행코드>
ELSE
<거짓의 실행코드?
END IF;
```
<조건>이 부합되는 거짓(false)이 아닌 참(true)일 경우 <참의 실행 코드>를 실행하고, <조건>이 거짓일 경우 <거짓의 실행코드>를 실행합니다.  
조건에 대한 실행 코드는 1줄 이상으로 구성할 수 있습니다.

일단 간단한 코드를 통해 ELSE 구문이 없는 IF문의 예를 살펴보겠습니다.

```postgresql
DO $$
    DECLARE
        a integer := 20;
        b integer := 40;
        c integer := 20;
    BEGIN
       IF a >b THEN
          RAISE NOTICE 'a가 b보다 더 큽니다.';
       end if;
    
       IF a < b THEN
          RAISE NOTICE 'a가 b보다 더 작습니다.';
       END IF;
       
       IF a = b THEN
          RAISE notice 'a와 b가 동일합니다.';
       end if;
       
       If a >= c THEN
          raise notice 'a가 c보다 크거나 같습니다.';
       end if;
       
       if a != b then
          raise notice 'a와 b가  같지 않습니다.';
       end if;
        
       if a != b AND a = c THEN
          RAISE NOTICE 'a와 b가 같지 않고 a와 c가 같습니다';
       end if;
       
       if a = b OR a = c THEN
          RAISE NOTICE 'a와 b가 같거나 a와 c가 같습니다.';
       end if;
       
       if not (a = b or a = c) THEN
          RAISE NOTICE 'a와 b가 같지 않고 a와 c도 같지 않습니다.';
       end if;
    END $$;
```

위 코드는 변수 `a`,`b`,`c` 에 20,40,20으로 초기화하고, 각 값에 대해서 IF문을 통해 비교하는데, 
오라클과 마찬가지로 OR, AND, =, >=, != 등을 통해서비교가 가능하다.

마찬가지로 `ELSE`구문도 포함할 수 있다.

- ELSE를 포함한 IF문 예시
```postgresql
DO $$
    DECLARE
        a integer := 20;
        b integer := 40;
BEGIN
    IF a > b THEN
        RAISE NOTICE 'a가 b보다 더 큽니다.';
    ELSE
        RAISE NOTICE 'a가 b보다 더 크지 않습니다.';
end if;
end$$;
```  
if 문에서 `a`가 `b`보다 큰가에 대한 조건이 거짓(false)이므로 ELSE문 다음의 실행코드가 실행된다.

-ELSE IF 문 또한 오라클과 같다.
```postgresql
DO $$
    DECLARE 
        a integer := 20;
        b integer := 40;
        
    BEGIN
        IF a > b THEN
            RAISE NOTICE 'a가 b보다 더 큽니다.';
        ELSEIF a = b THEN
            RAISE NOTICE 'a와 b가 같습니다.';
        ELSE
            RAISE NOTICE 'a와 b가 같지 않고, a가 b보다 더 크지도 않습니다.';
        END IF;
end$$;
```
ELSEDIF 문을 사용하는 것도 기존 사용하던 오라클에서의 IF 문과 동일하다.

6. CASE 조건문
오라클에서도 갖고 있던 CASE문이다. 
IF문과 같이 조건을 같지만 JAVA의 switch문과도 비슷하다.

- CASE 문을 활용한 함수 예
```postgresql
CREATE OR REPLACE FUNCTION get_surname(part_name CHAR(20))
RETURNS CHAR(2) AS $$
    DECLARE
        full_name CHAR(20);
        surname CHAR(1);
    BEGIN
        SELECT INTO full_name name
        FROM person
        WHERE name LIKE '%' ||part_name||'%';
        
        IF full_name IS NOT NULL THEN
        surname = substr(full_name,1,1);
        
        CASE surname
        WHEN '김' THEN
            RETURN '金民';
        WHEN '이' THEN
            RETURN '理民';
        WHEN '박' THEN
            RETURN '博民';
        WHEN  '최' THEN
            RETURN '崔民';
        ELSE RETURN '모름';
        END CASE;
        
        RETURN surname;
        ELSE
        RETURN NULL;
        END IF;
        END;$$
        LANGUAGE plpgsql;
```
위 함수에서 `CASE`의 조건절을 보면 `surname`변수에 조회된 이름의 성에 대한 1개의 문자가 할당된다.  
이 문자에 따라 한자로 그 결과를 반환해 주게 되는데, `CASE` 에 조건과 비교할 대상 값을 담고 있는 변수를 지정하고,
아래의 `WHEN`에 일치하는 값을 지정한다. 조건과 일치하는 `WHEN`을 만나면 `WHEN`의 밑에 존재하는 코드 부분이 실행된다.
```postgresql
CREATE OR REPLACE FUNCTION get_age_level(part_name CHAR(20))
RETURNS CHAR(20) AS $$
    DECLARE 
        the_age INTEGER;
    BEGIN
        SELECT INTO the_age age
        FROM person
        WHERE name LIKE '%' || part_name || '%';
        
    CASE
     WHEN the_age < 10 THEN 
        RETURN '어린아이';
     WHEN the_age >= 10 AND the_age < 20 THEN
        RETURN '10대 사춘기';
     WHEN the_age >= 20 AND the_age < 30 THEN
        RETURN '20대 취준생';
     WHEN the_age >= 30 AND the_age < 40 THEN
        RETURN '30대 청춘';
     ELSE
        RETURN '불노장생';
    END CASE;
    
end;$$
LANGUAGE plpgsql;
```  
위 함수 `get_age_level`은 인자로 지정한 이름과 유사한 `Row의 `age` 필드 값의 범위에 따라 각기 그 나이대를 표현하는 문자열을 반환한다.  
이 때 나이 범위에 대한 조건을 `CASE WHEN`구문으로 분류하고 있다. 



