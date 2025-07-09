# 1. 제어 역전 (loC, Inversion of Control)

개발자가 직접적으로 생성하거나 의존관계에 대해 관여하는 게 아닌 Spring에 의해 객체가 생성되고 의존관계가 형성된다.

- 스프링 애플리케이션에서는 오브젝트(빈)의 생성과 의존 관계 설정, 사용, 제거 등의 작업을 애플리케이션 코드 대신 스프링 컨테이너가 담당한다.
- 이를 스프링 컨테이너가 코드 대신 오브젝트에 대한 제어권을 갖고 있다고 해서 loC라고 부른다.
- 따라서, 스프링 컨테이너를 loC 컨테이너라고 부른다.

일반적인 경우 클래스에서 사용할 의존성을 직접 만들어서 사용한다.

```java
class OwnerController {
	private OwnerRepository repo = new OwnerRepository();
}
```

위이 코드와 같이 OwnerController에서 사용할 의존성을 직접 생성(new Owner Repository();) 해서 사용한다.

- Spring 이전 개발자들은 객체를 직업 선언/생성하고 메소드를 호출해야 하는 번거로움이 있는 반면,
Spring 이러한 흐름을 Controller가 담당한다.
- Spring의 설정대로만 하면 그에 따라 `Bean`을 생성하고 `의존관계`(Dependency Look up)를 통해 `주입`(Dependency Injection)해준다.
## 예시

```java
Class Person {
	Galaxy galaxy;
    
    public Person() {
    	galaxy = new Galaxy();
    }
}

Class Person {
	Iphone iphone;
    
    public Person() {
    	iphone = new Iphone();
    }
}

Class Galaxy implements Phone {
	...
}
```

Person이라는 클래스에 휴대폰 기기를 등록하는 필드가 있다고 가정한다. 위 코드에서 Person은 Galaxy 혹은 Iphone이라는 객체와 의존관계를 맺고 있다고 한다. Person은 직접 Galaxy 혹은  Iphone을 생성하여 사용하고 있다. 이렇게 생성된 객체는 직접 생성을 통해 사용된다.

```java
Class Main {
	Person person = new Person();
}
```

Spring 적용시

```java
Class controller {
	@Autowired
	Person person;
}

Class Person {
	@Qualifier("galaxy")
	Phone phone;
}
```

person을 직접 생성할 필요 없이 Spring Container에서 Person을 Bean으로 등록하고 알아서 주입까지 해준다.
개발자는 의존관계에 관여할 필요가 없다.

# 2. 관점 지향 프로그래밍 (AOP, Aspect Oriented Programming)

개발을 하다보면 서비스에 공통으로 필요한 보안, 인증, 로그 기록과 같은 기능이 있는 반면, 각 서비스의 핵심이 되는 비즈니스 로직이 따로 있다.

```java
Class Service {
	// 보안처리
	
	핵심기능
	
	// 로그 기록
}
```

만약 공통 부분이 떼어지지 않는다면 모든 Service에 위롸 같은 코드 형식으로 구성될 것이다.
이러한 형태는 후에 유지보수에도 좋지 않고 작은 변경에도 버그가 나는 참사가 일어날 수 있다.

> AOP는 보안처리, 로그 기록 같은 공통 기능을 따로 떼어내어 서비스는 서비스만의 핵심 기능에서만 집중할 수 있도록 제공한다.
> 

Aspect는 Pointcut + Advice를 의미하며
Pointcut은 어느 부분에 공통 기능을 삽입할 것인지 정의하는 것이고 Advice는 공통 모듈 자체를 의미한다.

Spring은 Proxy를 기반으로 AOP를 지원한다.
Aspect가 적용되는 클래스(객체)에 대해 프록시를 만든다.
AOP 대상 클래스에 직접적으로 접근하는 것이 아니 프록시에 접근하는 것이다.

## AOP 적용 기법

1. 다이나믹 프록시를 사용하는 방법
    - 기존 코드에 영향을 주지 않고 부가 기능을 적용해주는 데코레이터 패턴을 응용한 것이다
    - 부가 기능을 부여할 수 있는 곳은 메소드의 호출이 일어나는 지점뿐이라는 제약이 있다.
2. AspectJ를 이용한 언어의 확장
- 유명 오픈소스 AOP 툴이다.
- 프록시 방식에서 불가능한 다양한 조인 포인트를 제공한다.
- Java, JDK의 지원만으로 불가하여 적용 방법이 까다로우나 필요할 경우 사용한다.

# 3. PSA (Protable Service Abstraction)

> 하나의 추상화로 여러 서비스를 묶어둔 것 = 잘 만든 **인터페이스**
> 

잘 만들어진 인터페이스 덕분에 우리의 소스를 변경하지 않고 내부적으로 동작하는 로직을 변경할 수 있다.

**Service Abstraction**

- 추상화 계층을 사용하여 어떤 기술을 내부에 숨기고 개발자에게 편의성을 제공해주는 것

```java
try {
	dbConnection.setAutoCommit(false);

	doSomething()...

	dbConnection.commit();

	System.out.println("Done!");

} catch(SQLException e) {
	dbConnection.rollback();
}
finally {
	...
    	dbConnection.close();
}
```

위의 코드는 Transaction 처리에 대한 코드 일부를 가져온 것이다.

하지만 우리는 Service에 `@Transaction`이라는 어노테이션을 붙여 Transaction을 활용하기만 했을 뿐, 
**내부적으로 어떻게 동작하는지**에 대해 개발시에 살펴볼 일이 거의 없다.

Spring Framework에서 내부적으로 작동하는 기능들을 숨기고, 추상화하여 개발자에게 제공하기 때문에 편리하다.
위 코드와 같이 JDBC 기반으로 Database에 접근할 수 있고, 혹은 JPA를 활용하여 ORM 접근을 할 수도 있다.
하지만 개발자는 어떤 방법을 사용할지라도 Transaction 내부 코드를 수정하지 않는다.

## 예시1

Spring에서는 PlatformTransactionManager라는 최상위 Manager를 사용하고, 각각 사용자의 선언에 따라서 JPATransactionManager, DatasourceTransactionManager, HibernateTransactionManager 등을 상황에 맞게 의존성 주입을 받아 사용하게 된다.
따라서 개발자는 내부적으로 어떠한 Database Mapping 전략을 사용하든지 관계없이 `@Transaction`이라는 어노테이션을 활용하여 Transaction 처리를 할 수 있게 된다.

## 예시2

Spring MVC를 살펴보면 확인할 수 있다.

기본적으로 Servlet 기반 Application을 활용하지만, 개발자는 Spring framework를 사용하면 Servlet을 직접적으로 코딩할 일이 거의 없다

`@Controller` `@Get(url)` 등의 어노테이션만 활용하게 되면 보이지 않는 곳에서 Spring이 우리가 원하는 기능을 편리하게 해준다.
즉, 개발자는 HttpServlet을 구현하여 모든 Mapping에 대해 직접적으로 구현할 일이 없게 Service Abstraction을 하여 도와주는 것이다.

## 결론

Spring이 Interface로 DI를 받아 Low Level 코드를 알아서 시켜준다.   
그래서 우리는 로직에만 집중할 수 있게 도와준다.   
이것이 PSA이다   

# Reference

[https://velog.io/@zioo/spring-스프링의-철학-IoCAOPPSA-IoC](https://velog.io/@zioo/spring-%EC%8A%A4%ED%94%84%EB%A7%81%EC%9D%98-%EC%B2%A0%ED%95%99-IoCAOPPSA-IoC)

[https://jessyt.tistory.com/57](https://jessyt.tistory.com/57)
∏