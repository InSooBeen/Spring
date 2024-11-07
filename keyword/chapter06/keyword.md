# 6주차 핵심 키워드🎯

## Fetch Type

- JPA가 하나의 Entity를 조회할 때, 연관관계에 있는 객체들을 어떻게 가져올 것인지를 나타내는 설정값
- JPA는 사용자가 직접 쿼리를 생성하는 것이 아닌, JPA에서 JPQL을 이용하여 쿼리문을 생성함
  ⇒ 객체와 필드를 보고 쿼리를 생성
- 코드에 다른 객체와 연관관계 매핑이 되어있다면 그 객체들까지 조회하며, 객체를 불러오는 방식을 설정할 수 있는데 이를 Fetch Type이라고 함.
- default 값: `@xxToOne`에서는 EAGER, `@xxToMany`에서는 LAZY

## 지연로딩

> ***지연로딩**
객체를 조회하는 시점에서 실제 객체가 아닌, 가짜(프록시) 객체를 가져온 후 , 객체를 사용하는 시점에 실제 객체를 가져오도록 하는 방식*
>

- `@xxToxx(Fetch = fetchType.LAZY)` 의 형식으로 사용함

```java
@Entity
public class User {
    @Id
    @GeneratedValue
    private long id;
    private String firstName;
    private String lastName;

    @ManyToOne(fetch = FetchType.EAGER)		// 지연 로딩
    @JoinColumn(name = "team_id", nullable = false)
    private Team team;
}

@Entity
public class Team {
    @Id
    @GeneratedValue
    private long id;
    private String name;

    @OneToMany(fetch = FetchType.EAGER)
    private List<User> users = new ArrayList<>();
}
```

- 지연 로딩을 사용하면 User를 조회하는 시점이 아닌 Team을 사용하는 시점에 쿼리가 발생하게 할 수 있음. (단, Team을 사용하는 시점에는 추가적인 N번의 쿼리가 발생함)

**지연로딩의 장점**

- 지연 로딩은 필요한 시점에만 연관된 데이터를 로딩하므로 성, 메모리 사용의 최적화가 가능함(반면, 즉시 로딩은 필요하지 않은 데이터까지 가져올 수 있음)
- 데이터를 사용하는 시점에만 로딩하므로, 데이터베이스 접근이 최적화 됨
- 객체를 조회할 때 필요한 데이터만 로딩되므로 무한한 순환 참조를 방지할 수 있음

## 즉시로딩

> ***Fetch join**
연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 방법
사용방법: 쿼리문에 직접 fetch를 명시하기 또는 @EntityGraph 사용*
>
- `@xxToxx(Fetch = fetchType.EAGER)` 의 형식으로 사용함

```java
@Entity
public class User {
    @Id
    @GeneratedValue
    private long id;
    private String firstName;
    private String lastName;

    @ManyToOne(fetch = FetchType.LAZY)		// 즉시 로딩
    @JoinColumn(name = "team_id", nullable = false)
    private Team team;
}

@Entity
public class Team {
    @Id
    @GeneratedValue
    private long id;
    private String name;

    @OneToMany(fetch = FetchType.LAZY)
    private List<User> users = new ArrayList<>();
}
```

즉시 로딩을 사용하면 User를 조회하는 시점에 Team을 조회하는 쿼리까지 발생하여 연관된 데이터를 한 번에 불러옴.

**즉시로딩의 장점**

- 즉시로딩을 사용하면 연관된 객체까지 한 번에 조회되므로 객체 간의 관계를 편리하게 활용할 수 있음.
- 데이터베이스에서 조인을 사용하여 복잡한 연관관계를 해결하지 않고, 모든 데이터를 한 번에 가져올 수 있음.


## Fetch Join

> ***Fetch Join**
SQL의 조인이 아닌, JPQL에서 성능 최적화를 위해 제공하는 기능으로, Fetch Join을 사용하면 연관된 Entity나 컬렉션을 한번에 조회할 수 있음.*
>

**사용하는 이유**

- 글로벌 로딩 전략을 지연로딩(LAZY)로 설정한 경우 Fetch join을 이용하면 성능을 향상시킬 수 있음

## Join VS Fetch Join

**일반 Join**

- 연관 Entity에 Join을 걸어도 실제 쿼리에서 SELECT 하여 영속화하는 Entity는 오직 JPQL에서 조회하여 주체가 되는 Entity임.

Fetch Join

- 조회의 주체가 되는 Entity 이외에 Fetch Join이 걸린 연관 Entity도 함께 SELECT하여 영속화 함.

## Fetch Join의 한계점

- fetch join 대상에는 별칭을 줄 수 없음.  
  ⇒ JPA 표준에서는 별칭을 지원하지 않고 hibernate를 포함한 몇몇의 구현체들은 별칭을 지원하긴 하나, 별칭을 잘못 사용하면 연관된 데이터의 수가 달라져 데이터 무결성이 깨질 수 있으므로 사용하지 않는 것이 좋음.
- 둘 이상의 컬렉션을 fetch 할 수 없음.
- 일대다 관계에서 컬렉션을 fetch Join하면 페이징 API를 사용할 수 없음


## @EntityGraph

> ***@EntityGraph**
JPA에서 fetch join을 해당 어노테이션을 통해 사용할 수 있도록 만든 기능*
>

**@EntityGraph의 2가지 타입**

- FETCH: @EntityGraph에 명시한 attribute는 EAGER로 fetch 하며, 나머지는 LAZY로 fetch함
  (default)
- LOAD: @EntityGraph에 명시한 attribute는 EAGER로 fetch하며, 나머지는 Entity에 명시한 fetch type을 따름

## @EntityGraph vs Fetch Join

- @EntityGraph는 fetch type을 eager로 변환하는 방식이며, outer left join을 사용하여 데이터를 가져옴.
  ⇒조회하는 엔티티에 해당되지 않는 연관 엔티티까지 조회가 됨
- fetch join은 따로 outer join을 명시하지 않으면 inner join을 수행하여 데이터를 가져옴.
  ⇒ 조회하는 엔티티에 해당되는 연관 엔티티만 조회가 됨

**사용 방법**

**`@EntityGraph(attributePaths = {"fetch join 할 객체의 필드명"}`** 의 형식으로 사용함

**사용 시 주의사항**

- Fetch Depth를 설정하여 연관된 엔티티 객체를 가져오는 깊이를 제한하여 무한 재귀 호출을 방지하는 것이 좋음
- 즉시로딩을 사용하는 것이기 때문에, 성능에 영향을 주지 않으려면 필요한 경우에만 사용해야 함


## JPQL

> ***JPQL**
JPA는 SQL을 추상화 한 JPQL이라는 객체 지향 쿼리 언어를 제공함.
⇒ SQL과 달리 테이블이 아닌 엔티티 객체를 대상으로 쿼리를 만듦
⇒ 데이터 베이스 구조와 독립적*
>
- **객체 지향적** : 엔티티 클래스와 속성을 기반으로 쿼리를 작성하기 때문에 객체 지향 프로그래밍에 부합하고, 엔티티 간의 관계를 쉽게 활용할 수 있음
- **데이터베이스 독립적:** 특정 데이터베이스 구문에 의존하지 않으므로 다양한 데이터 베이스 시스템에서 사용 가능
- **성능** : 때때로 Native SQL에 비해 최적화가 덜 되어있어 성능이 떨어질 수 있음
- **하위 쿼리 제한적 :** From 절의 하위 쿼리 사용이 제한적임

**JPA문법 대신 JPQL을 사용하는 이유는?**

- JPA 문법은 CRUD 명령어 처리에서 효율적인 명령어 기능을 제공하지만, 검색 명령어에 대해 비효율적임
- JPA는 객체 중심이므로 검색을 할 때에도 엔티티 객체를 대상으로 하는데, DB의 모든 데이터를 엔티티로 변경하여 처리하는 것이 불가능함

⇒ 특수한 경우에 대해 처리할 수 있도록 만든 쿼리가 JPQL임

  ****

**JPQL의 문법**

`select u from User as u where a.age > 18`  ⇒ 기본적으로 SQL과 유사한 구문을 사용함

- 엔티티 클래스 이름, 엔티티 필드의 대소문자가 일치해야 함 (대문자, 소문자 구분 필요)
- JPQL의 키워드는 대소문자를 구분하지 않음
- 별칭을 붙이는 것이 필수적임
- SQL을 추상화한 것이므로 특정 SQL에 의존하지 않음
- JPQL은 최종적으로 SQL로 변환되어 데이터베이스에 적용

**JPQL의 장점**

- JPA와 통합되어 있기 때문에, 일관된 API를 제공함
- 객체 지향 패러다임에 맞춰 쿼리를 작성할 수 있음.

**JPQL의 단점**

- 기본 문자열로 작성되기 때문에 컴파일시 에러가 발생하지 않으며, 런타임에 문제가 발생함
  ⇒ JPQL이 길어지면 문자열 사이의 에러를 찾기 힘들어짐.
- 정적 쿼리에 적합하며, 복잡한 동적 쿼리 작성에 한계가 있음.
- 엔티티 객체에 의존적이므로 객체 수정에 따른 JPQL의 수정이 필수적임.


## QueryDSL

> *QueryDSL
JPQL을 자바 코드로 작성할 수 있도록 하는 라이브러리*
>
- 정적 타입을 이용해 SQL과 같은 쿼리를 생성할 수 있도록 함.
- 쿼리를 문자열로 작성하는 것이 아닌, QueryDSL이 제공하는 Fluent API를 이용해 쿼리를 생성할 수 있도록 함.

**QueryDSL을 사용하는 이유?**

- 기존 JPQL은 문자열로 쿼리를 작성했기 때문에, 컴파일 시점에 에러가 발생하지 않음.
  ⇒QueryDSL을 통해 자바 코드로 쿼리를 작성하게 함으로써, 컴파일 시점에 에러를 잡을 수 있게 되었음
- JPQL을 이용해 동적 쿼리를 다루기 위해서는 문자열을 조건에 맞게 조합해서 사용해야 했기 때문에, 코드가 복잡해지고 런타임 에러 발생 확률이 높았음
  ⇒ QueryDSL을 통해 복잡한 동적 쿼리를 Q클래스, 메서드를 활용하여 쉽게 다룰 수 있음, 가독성도 향상

**QueryDSL의 단점**

- 초기 설정이 복잡하며, JPA와의 통합을 위해 추가적인 설정과 코드기 필요함