#  🎯10주차 핵심 키워드

## Spirng Security

> *Java 기반의 Spring Framework를 위한 보안 프레임워크로, 애플리케이션에 인증(Authentication) 및 권한 부여(Authorization) 기능을 쉽게 추가할 수 있도록 설계됨. Spring 기반 애플리케이션을 보호하고, 보안 기능을 표준화함*>

## 주요 용어

### 인증(Authentication)

- 유저 아이디와 비밀번호를 이용하여 로그인하는 과정으로, 사용자 확인을 위함.

### 인가(Authorization)

- 인증된 사용자가 특정 리소스에 접근할 권한이 있는지를 확인함

### 접근 주체(Principal)

- 보호된 대상에 접근하는 유저

### 권한

- 인증된 주체가 애플리케이션의 동작을 수행할 수 있도록 허락되었는지를 결정할 때 사용하는 개념이자, 수행할 수 있는 작업의 범위

### CSRF

- 사용자를 속여, 의도치 않은 요청을 보내게 만드는 것을 방지하는 기능으로  Spring Security는 CSRF 토큰을 활용하여 보호함.

## 주요 구성 요소

### AuthenticationManager

- 인증 요청을 처리하는 중앙 관리자 역할
- 인증에 성공 또는 실패 여부를 반환하며, 내부적으로는 하나 이상의  AuthenticationProvider에 위임하여 인증을 수행함
- ProviderManager가 기본 구현체로 사용됨

### AuthenticationProvider

- 실제로 인증 로직을 수행하는 구현체를 말함
- 특정 인증 방식을 처리하며, AuthenticationManager에 의해 호출됨
- 다양한 인증방식을 지원할 수 있도록 여러 방식으로 구성할 수 있음

### UserDetialService

- 사용자 인증 정보를 제공하는 서비스
- Spring Security의 AuthenticationProvider에서 사용자 정보를 조회할 때 사용됨
- 기본적으로 사용자의 이름을 기반으로 자용자 정보를 검색함

### SecurityContext

- 인증된 사용자의 보안 정보를 저장함
- SecurityContextHodler를 통해 어플리케이션 전역에서 접근 가능함
- 사용자 인증 정보와 권한을 포함함
- 일반적으로, 요청 사이에서 상태 유지를 위해 세션이나 토큰을 사용함

### SecurityContextHolder

- 인증된 사용자 정보를 어플리케이션 전역에서 관리함
- 인증 정보를 SecurityContext 객체에 저장하며, 요청이 진행되는 동안 ThreadLocal에 저장됨
- SecurityContext(현재 인증 정보)와 Authentication(인증된 사용자 정보)으로 구성됨

### Filter Chain

- Spring Security가 요청을 처리하기 위해 사용하며, 요청에대한 인증 및 권한 부여를 처리함
- 모든 작업이 필터 체인을 통해 이루어지며, 요청이 필터를 통과할때마다 각 필터에서 보안검사 수행
- 역할1: 인증되지 않은 요청을 가로 챔
- 역할2: 인증/인과 과정을 실행
- 역할3: 요청이 적합한 경우, 컨트롤러에 전달함

**주요 필터**

**SecurityContextPersistenceFilter**

- 요청 간 SecurityContext를 유지
- 새 요청이 들어올 때 이전에 인증된 사용자의 정보를 복원

**UsernamePasswordAuthenticationFilter**

- 폼 기반 로그인을 처리함
- 사용자가 제출한 username과 password를 확인하여 인증을 시도함

**AnonymousAuthenticationFilter**

- 이전 필터에서 인증되지 않은 요청에 대해 익명 사용자 인증을 제공함

**ExceptionTranslationFilter**

- Spring Security 예외를 HTTP 응답으로 변환함
- 인증 실패 시 로그인 페이지로 리다이렉트하거나, 인가 실패 시 403 오류를 반환함

**FilterSecurityInterceptor**

- 접근 제어 결정을 내리는 마지막 필터
- 현재 인증된 사용자가 요청한 리소스에 접근할 권한이 있는지 확인함

## 인증(Authentication)

> *사용자가 누구인지 확인하는 과정으로, 사용자가 자신의 정체성을 증명해야하는 단계.
유효한 사용자인지를 확인하기 위한 과정이며, 사용자가 인증되면 Authentication 객체를 생성하여 인증 상태가 유지됨.*
*>

## 흐름

- 사용자의 로그인 요청이 들어오면 UsernamePasswordAuthenticationFilter가 요청을 가로채고 Authentication 객체를 생성함
- AuthencationManager가 적절한 AuthenticationProvider를 선택해 인증을 위임함
- 선택된  AuthenticationProvider가 UserDetailsServcie를 사용해 사용자 정보를 로드함
- 인증이 성공하면 Authentication 객체가 SecurityContext에 저장됨

## 인가(Authorization)

> *인증된 사용자의 권한을 확인하여 특정 리소스 작업에 대한 허가/거부를 결정하는 것이며, 사용자 인증 후, 요청한 리소스에 접근하려 할 때 수행됨.*
>

## 흐름

- 사용자의 리소스 접근 요청이 들어오면 FilterSecurityInterceptor가 요청을 가로채 권한 검사를 실행
- AccessDecisionManager가 현재 사용자의 권한과 요청된 리소스의 필요 권한을 비교함
- SecurityContext에서 현재 인증된 사용자의 권한 정보를 조회
- 권한이 충분하면 접근이 허용되고, 부족하면 403 Forbidden 응답 반환

## 인증과 인가의 차이

![](https://velog.velcdn.com/images/isb040818/post/57063dbe-d3bc-4924-b32a-4148b27399eb/image.png)
