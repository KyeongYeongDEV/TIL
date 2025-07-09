# Spring Bean 생명주기란

Spring은 단순히 객체를 생성하는 게 아니라, 객체의 생성부터 소멸까지 전 과정을 관리합니다.

이 과정을 Spring Bean의 생명주기라고 부릅니다.

## 생명주기의 흐름

1. 객체 생성 → @Component, @Bean 등록
2. 의존성 주입(DI) → @Autowired, 생성자 등
3. 초기화 콜백 → @PostConstruct, afterPropertiesSet()
4. 사용 → 비즈니스 로직
5. 소멸 콜백 →@PreDestroy, destroy()

## 생명주기 콜백 정리

Spring은 특정 타이밍에 실행되는 콜백 메서드를 제공합니다.

### 초기화

- @PostConstruct
    - Bean 생성 후 실행 (가장 많이 사용)
- afterPropertiesSet()
    - InitializingBean 인터페이스 구현
- @Bean(initMethod = “init”)
    - 자바 Config에서 명시적 등록

### 소멸

- @PreDestroy
    - 컨테이너 종료 전 실행
- destroy()
    - DisposableBean 인터페이스 구현
- @Bean(destoryMethod = “close”)
    - 자바 config에서 명시적 등록

## 스프링 컨테이너 생성 예시

![image.png](./assets/생명주기_스프링%20컨테이너%20생성%20예시.png)

- Spring 컨테이너를 생성할 때, 파라미터로 넘어온 클래스 정보를 사용해서 Spring Bean을 등록한다.
- 주의점 - Bean 이름은 항상 다른 이름을 부여할 것

# 의존성 주입 (DI)

Spring에서 객체 간의 관계는 3가지 방식으로 주입할 수 있다.

<aside>
💡

의존성 주입이란?
스프링 컨테이너에서 객체 Bean을 먼저 생성해두고 생성한 객체를 지정한 객체에 주입하는 방식을 의존성 주입이라고 한다. 객체 자체가 코드 상에서 객체 생성에 관여하지 않아도 되기때문에 객체 사이의 의존도를 낮출수 있고, 유연하고 확장성이 뛰어난 코드를 작성할 수 있다.

</aside>

## DI 예시

- `@Repository`를 사용해 `MemberRepository`를 Bean으로 등록한다.
- `@Service`를 사용해 `MemberService`를 Bean으로 등록한다.
- `@Autowired`로 `MemberRepository`가 `MemberService`에 자동 주입

```java
// 데이터 저장
@Repository
class MemberRepository {
    public void save(String name) {
        System.out.println(name + " 저장 완료!");
    }
}

// 서비스 로직
@Service
class MemberService {
    private final MemberRepository memberRepository;

    @Autowired  // Spring이 자동으로 주입
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    public void registerMember(String name) {
        memberRepository.save(name);
        System.out.println(name + " 등록 완료!");
    }
}

// 스프링 애플리케이션 실행
@SpringBootApplication
public class DependencyInjectionExample {
    public static void main(String[] args) {
        var context = SpringApplication.run(DependencyInjectionExample.class, args);
        
        // 컨테이너에서 MemberService Bean 가져와 실행
        MemberService memberService = context.getBean(MemberService.class);
        memberService.registerMember("홍길동");
    }
}
```

## 생성자 주입 (권장)

```java
@Service
public class MemberService {
    private final MemberRepository memberRepository;

    @Autowired // Spring 4.3+에서는 생략 가능
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

- `final` 키워드를 사용하여 의존성을 반드시 설정하도록 강제할 수 있다.
- 테스트 코드에서 Mock 객체를 쉽게 주입할 수 있다.
- Spring 4.3이상에서는 `@Autowired` 없이 자동 주입 가능
- 가장 권장되는 방식

## Setter 주입 (특정 경우에 사용)

```java
@Service
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;
}
```

- 선택적 의존성 주입이 가능
- 객체가 변경될 위험이 있음
- Setter를 여러 번 호출하면 주입된 Bean이 변경될 수 있음
- 주로 설정 파일에서 동적으로 Bean을 변경해야 하는 경우에만 사용

## 필드 주입 (비추천)

```java
@Service
public class MemberService {
    private MemberRepository memberRepository;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

- Spring 컨테이너 없이 순수한 자바 코드에서 테스트 불가능
- 객체 생성 후 변경이 불가능 (final 사용 불가)
- Spring 프레임워크에 강하게 결합됨 (DI 숨겨짐)

## @Bean() 예제

자바 설정 파일에서 `@Bean`을 통해 직접 초기화/소명 메서드를 지정할 수도 있다.

```java
@Configuration
public class AppConfig {

    @Bean(initMethod = "init", destroyMethod = "close")
    public NetworkClient networkClient() {
        return new NetworkClient();
    }
}
```

```java
public class NetworkClient {
    public void init() {
        System.out.println("초기화 메서드 호출");
    }

    public void close() {
        System.out.println("소멸 메서드 호출");
    }
}
```

# Reference

https://velog.io/@yjl8628/Spring-Bean%EC%9D%98-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0%EC%99%80-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85-%EB%B0%A9%EC%8B%9D
