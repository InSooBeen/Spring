# 7주차 핵심 키워드🎯

# RestContollerAdvice

## @ExceptionHandler

> ***@ExceptionHandler***
>
>자바에서는 Exception이 발생하면 try-catch문을 통해 예외를 처리해야 하는데, 이런 방식은 코드 증가 및 중복, 가독성 감소를 야기함.
Spring에서는 `@ExceptionHandler`  를 통해 유연하고 간단한 예외처리를 지원함.
>

**장점**

사용시 HttpServletRequest, WebRequest를 얻을 수 있으며, 반환 타입으로는 ResponseEntity, String, void 등을 자유롭게 활용할 수 있음.

**단점**

특정 컨트롤러에서 발생하는 예외만 처리하기 때문에, 여러 컨트롤러에서 발생하는 예외가 중복처리 될 수 있고, 컨트롤러의 기능에 예외처리 코드가 섞이며 단일 책임 원칙(SRP)가 위배됨.

**사용 예제**

```java
@GetMapping("/posts")
public String posts(){
	int exception = 4/0;
	return "posts";
}

@ExceptionHandler(ArithmeticException.class)
public ResponseEntity<String> handleNoSuchElementFoundException(ArithmeticException exception) {
	//구현
}
```

## @ControllerAdvice와 @RestControllerAdvice

> ***@ControllerAdvice, @RestControllerAdvice***
>
>각각 @Controller, @RestController가 붙은 컨트롤러에서 발생하는 예외(@ExceptionHandler)를 AOP를 적용해 전역적으로 처리할 수 있도록 함.
>

**@ControllerAdvice**

- @ExceptionHandler, @ModelAttribute, @InitBinder가 적용된 메서드들에 AOP를 적용해 Controller에서 예외를 처리하기 위해 고안된 어노테이션
- 클래스에 선언하여 모든 @Controller에 대해 전역적으로 발생할 수 있는 예외를 처리할 수 있음

**@RestControllerAdvice**

- @ControllerAdvice와 @ResponseBody를 합쳐놓은 어노테이션
- @ControllerAdvice와 동일한 역할을 수행하며 추가적으로 @ResponseBody를 통해 객체를 리턴할 수 있음

⇒ *예외만 처리하고 싶다면 @ControllerAdvice를 적용, 예외처리 후 응답으로 객체를 리턴해야 한다면 @RestControllerAdvice를 적용*

**사용 예제**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ArithmeticException.class) //1번
    public ResponseEntity<String> handleArithmeticException(ArithmeticException e) {
			//구현
    }
    
     @ExceptionHandler({Exception.class}) // 2번
    public ResponseEntity<Object> handleIllegalArgument(Exception e) {
			//구현
    }
}
```

ArithmeticException이 발생하면 1번에서 처리가 되며, 예외 발생 후 별다른 처리가 없으면 최상위 예외인 Exception을 처리하는 2번에서 처리가 됨.

**장점**

- 각 컨트롤러에서 예외 처리 로직의 중복이 사라지고, 코드가 간결해짐
- 컨트롤러에서 발생하는 예외를 집중적으로 처리할 수 있어 코드의 일관성을 유지할 수 있음


# lombok

## lombok

> ***lombok***
>
>어노테이션을 사용하여 반복되는 메소드를 자동으로 작성해주는 라이브러리
일반적으로 VO, DTO, Model, Entity등의 데이터 클래스에서 주로 사용됨
ex) @Getter, @Setter, @NoArgsConstructor, @AllArgsConstructor, @Data, @ToString
>

**장점**

- 어노테이션을 기반으로 코드를 자동 생성하여 생산성이 향상됨
- 코드의 반복을 줄임으로써, 가독상과 유지보수성이 향상됨
- 빌드패턴, 로그생성 등의 다양한 방면으로 활용 가능

## @Getter와 @Setter

클래스에 선언되어 있는 필드를 기반으로 ‘getField’, ‘setField’와 같은 형식의 메소드를 자동으로 생성함

```java
//@Getter, @Setter 미사용
public class MemberDTO{
	private String name;
	private String email;
	private String oranization;
	
	public String getName(){
		return name;
	}
	public void setname(String name){
		this.name = name;
	}
	public String getEmail(){
		return email;
	}
	public void setEmakl(String email){
		this.email = email;
	}
	public String getOrganizaiton(){
		return organization;
	}
	public void setOrganization(String organization){
		this.organization = organization;
	}
}

//@Getter, @Setter 사용
@Getter
@Setter
public class MemberDTO{
	
	private String name;
	private String email;
	private String organization;
}
```

## @NoArgsConstructor, @AllArgsConstructor, @RequiredArgsConstructor

- 자동으로 생성자를 만들어주는 어노테이션.
- 파라미터가 없는 생성자(@NoArgsConstructor)
- 모든 필드값을 파라미터로 갖는 생성자(@AllArgsConstructor)
- final이나 @NotNull인 필드값을 파라미터로 갖는 생성자(@RequiredArgsConstructor)

```java
@NoArgsConstructor
@AllArgsConstructor
public class MemberDTO{
	
	private String name;
	private String email;
	private String organization;

	//@NoArgsConstructor
	public MemberDTO(){}
	//@AllArgsConstructor
	public MemberDTO(String name, String email, String organization){
		this.name = name; this.email = email; this.organization = organization;
	}
}

```

## @ToString

- toString 메소드를 자동으로 생성해주는 어노테이션.
- exclude속성을 사용해 특정 필드를 toString에서 제외시킬 수 있음

```java
//@ToString 미사용
public class MemberDTO{
	
	private String name;
	private String email;
	private String organization;
	
	@Override
	public String ToString(){
		return "MemberDTO{" + "
		"name=" + name + "\" + 
		", email=" + email + "\" +
		", organization=" + organization + "\" +
		"}";
	}
}

@ToString
public class MemberDTO{
	
	private String name;
	private String email;
	private String organization;
}
```

## @EqualsAndHashCode

- equals, hashCode 메소드를 자동으로 생성해주는 어노테이션
- equals : 두 객체의 내용이 같은지(동등성)를 비교하는 연산자.
- hashCode: 두 객체가 같은 객체인지(동일성)를 비교하는 연산
- callSuper 속성을 통해 부모 클래스의 필드 고려 여부를 설정할 수 있음

## @Data

- 클래스에 앞서 나온 기능들을 한번에 추가해주는 어노테이션

## @Builder

- 클래스의 객체를 생성할 때, Builder 패턴을 적용시켜주는 어노테이션.
- 모든 변수들에 대해 build하려면 클래스 위에 사용
- 특정 변수만 build하려면 생성자 작성 후, 위에 사용

## @Delegate

- 한 객체의 메소드를 다른 객체로 위임시켜주는 어노테이션

```java
@AllArgsConstructor
@Getter
public class Review {
	private String userName;
	private String content;
}

@Getter
@NoArgsConstructor
public class Store{
	/*
		필드들
	*/
	@Delegate // revieList에서 사용하는 함수들을 Store로 위임하여 코드를 편리하게 작성
	private List<Review> reviewList;
}

ex) store.getReviewList().add(review) => store.add(review)
```