# 🎯8주차 핵심 키워드


# java의 Exception 종류들

## Error / Exception

> ***Error**
프로그램 코드를 통해 수습될 수 없는 심각한 오류*
>
- 메모리 부족, 스택오버플로우 등 발생하면 복구할 수 없으며, 예측이 불가능함
- JVM 실행에 문제가 생긴 것이므로 개발자가 대처할 수 없음

> ***Exception**
프로그램 코드를 통해 수습될 수 있는 다소 미약한 오류*
>
- 알고리즘 오류로, 개발자가 구현한 로직에서 발생한 실수 또는 사용자에 의해 나타남
- 예외에 대한 대응 코드를 미리 만들어서 프로그램의 비정상적인 동작이나 종료를 막을 수 있음

## java의 Exception 클래스

자바에서는 오류를 Error와 Exception으로 나누었으며, 클래스로 구현하여 처리하도록 함
(단, Error 클래스는 개발자가 대응 불가)

JVM은 프로그램 실행 중에 오류가 발생하면 해당 예외 클래스로 객체를 생성하고, 예외 처리 코드에서 예외 객체를 이용할 수 있도록 함

> ***Throwable 클래스***
*오류와 예외 클래스는 자바 최상위 클래스인 Object 클래스를 상속받으며, 그 사이에서 Throwable 클래스도 상속받음.*
>
>
> *Throwable 클래스는 오류나 예외에 대한 메시지를 담는 역할을 하며, 대표적으로 getMessage() 와 printStackTrace() 메서드를 제공함.*
>

> ***Exception 클래스***
*자바에서 다루는 모든 예외를 처리하는 클래스로, 모든 예외 클래스는 Exception 클래스를 상속받음*
>

Exception 클래스는 런타임 에러를 다루는 RuntimeException 클래스와 그 외의 하위 클래스로 나눌 수 있음.

**Exception 클래스 및 하위 클래스**

- 사용자의 실수와 같은 외적인 요인에 의해 발생하는 컴파일 타임 예외

**RuntimeException 클래스**

- 프로그래머의 실수로 발생하는  런타임 예외

## Exception 하위 클래스 종류

**FileNotFoundException**

⇒ ****존재하지 않는 파일의 이름 입력하면 발생하는 예외

**ClassNotFoundException**

⇒ ****실수로 클래스의 이름을 잘못 기재하면 발생하는 예외

**DataFormatException**

⇒ 입력한 데이터의 형식이 잘못된 경우 발생하는 예외

**IOException**
⇒ 입출력 작업 중에 발생하는 예외

## RuntimeException 하위 클래스 종류

**ArithmeticException**

⇒ 어떤 수를 0으로 나누는 등의 비정상적인 계산을 진행하면 발생하는 예외

**ArrayIndexOutOfBoundsException**
⇒ 배열의 범위를 넘은 인덱스를 참조할 때 발생하는 예외

**NullPointException**

⇒ null 객체에 접근하여 메서드를 호출(비어있는 객체 사용)하면 발생하는 예외

**NumberFormatException**

⇒ 정수가 아닌 문자열을 정수로 변환할 때 발생하는 예외

**ClassCastException** 

⇒상속 또는 구현관계가 아닌 클래스로 타입 변환할 때 발생하는 예외

**InputMismatchException**

⇒ 의도되지 않은 입력이 들어오면 발생하는 예외
(ex. 정수를 입력해야하는데 문자열을 입력한 경우)

## Checked Exception / UnChecked Exception

> ***Checked Exception**
RuntimeException을 상속받지 않은 예외 클래스로,  컴파일 예외 클래스들을 의미함*
>
- 해당 Exception들은 예외 처리를 강제함.
- 예외 확인 시점이 컴파일 단계이므로 예외처리를 하지 않으면 컴파일이 되지 않음.
- try-catch문 또는 throw를 통해 예외 처리 필수

> ***UnChecked Exception**
RuntimeException을 상속받은 예외 클래스로, 런타임 예외 클래스들을 의미함*
>
- 해당 Exception들은 예외 처리를 강제하지 않음
- 개발자가 충분히  주의하면 회피할 수 있으며, 상대적으로 미약한 예외이기 때문에 자바 컴파일러가 별도의 예외 처리를 하지 않도록 설계됨


# @Valid

## 유효성 검사

> ***Validation**
서비스의 비즈니스 로직이 올바르게 동작하기 위해 사용되는 데이터에 대한 사전 검증 작업.
즉, 들어오는 데이터가 의도한 형식에 맞는 값으로 들어오는지를 확인하는 과정.
Controller, Service, Repository 등 여러 계층에서 발생하는 흔한 작업.*
>

**일반적인 Validation의 문제점**

- 어플리케이션 전체적으로 분산되어 존재함
- 코드의 중복이 심하여 코드가 복잡해짐
- 비즈니스 로직에 섞여 있어 검사 로직 추적이 어려움

## Bean Validation / Hibernate Validator

> ***Bean Validation**
위의 문제를 해결하기 위해 Java에서 2009년부터 제공하기 시작한 유효성 검사 프레임워크.
어노테이션을 통해 다양한 데이터를 검증할 수 있게 기능을 제공함*
>

> ***Hibernate Validator**
Bean Validation 명서에 대한 구현체이며, Spring Boot의 유효성 검사 표준은 Hibernate Validator를 채택함.*
>

## @Valid

> ***@Valid***
>
>
> *빈 검증기(Bean Validator)를 이용해 객체의 제약 조건을 검증하도록 지시하는 어노테이션.
> 즉, 해당 객체의 필드에 적용된 조건(@NotNull, @Size, @Email 등)을 기반으로 유효성 검사를  함.*
>

## **@Vallidated**

> @Validated
@Valid와 같이 객체의 유효성을 검사하는 어노테이션.
@Valid와 달리 groups 기능을 제공하여, 하나의 필드에 대해 상황에 따라 다른 검증을 할 수 있음.
>

## @Valid 검증 오류 처리

**BindingResult가 명시되어 있을 때**

Spring Boot는 @Valid 가 붙은 객체에서 Bean Validation에 실패할 경우 BindingResult 에 검증 실패에 관한 정보를 넣음.

⇒ BindingResult 를 이용해 검증 실패 로직을 알맞게 작성

**BindingResult가 명시되어 있지 않을 때**

스프링 부트는 MethodArgumentNotValidException 예외를 발생시킴.

⇒ @RestControllerAdvice 등을 통해 공통 예외 처리를 수행하거나, 컨트롤러 안에서 try-catch
등을 활용하여 예외 발생 시 처리 로직을 작성