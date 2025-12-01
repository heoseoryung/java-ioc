## 🧩 문제 1. 자바 `new`를 스프링 IoC로 바꾸기

아래는 **스프링 없이** 작성된 코드입니다.

```java
public class HelloService {
    public String sayHello() {
        return "Hello, Spring IoC!";
    }
}

public class HelloController {
    private HelloService helloService = new HelloService();

    public void handleRequest() {
        System.out.println(helloService.sayHello());
    }
}

public class App {
    public static void main(String[] args) {
        HelloController controller = new HelloController();
        controller.handleRequest();
    }
}
```

### ✏️ 요구사항

1. 위 코드를 **스프링 IoC 컨테이너를 사용하도록 리팩터링**하세요.
2. 다음 조건을 만족해야 합니다.

   * `HelloService`, `HelloController`를 **스프링 빈**으로 등록할 것
   * `HelloController`는 `new HelloService()`를 사용하지 말 것
   * `main` 메서드에서 `AnnotationConfigApplicationContext` 또는 `SpringApplication`을 사용해서 빈을 가져와 실행할 것

> 🎯 결과적으로 `App.main`에서 컨트롤러를 스프링 컨테이너에서 꺼내와서 `handleRequest()`를 호출하면 됩니다.

---

## 🧩 문제 2. `@Configuration` + `@Bean`으로 직접 빈 등록하기

아래는 일반 자바 클래스입니다.

```java
public class MemberRepository {
    public void save(String name) {
        System.out.println("save member: " + name);
    }
}

public class MemberService {
    private MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    public void join(String name) {
        memberRepository.save(name);
    }
}
```

### ✏️ 요구사항

1. 위 두 클래스를 사용하는 **스프링 설정 클래스(AppConfig)** 를 작성하세요.

   * `@Configuration` 사용
   * `@Bean` 메서드를 사용해 `MemberRepository`, `MemberService` 빈 등록
2. `main` 메서드에서 `MemberService`를 꺼내와서 `join("홍길동")`을 호출해 보세요.

```java
public class App {
    public static void main(String[] args) {
        // TODO: 스프링 컨테이너 생성
        // TODO: memberService 빈 가져오기
        // TODO: join 호출
    }
}
```

---

## 🧩 문제 3. 필드 주입 → 생성자 주입으로 변경하기

다음은 **좋지 않은 방식(필드 주입)**의 코드입니다.

```java
@Component
public class OrderService {

    @Autowired
    private DiscountPolicy discountPolicy;

    public int discount(int price) {
        return discountPolicy.discount(price);
    }
}

@Component
public class DiscountPolicy {
    public int discount(int price) {
        return price - 1000;
    }
}
```

### ✏️ 요구사항

1. 위 코드를 **순수 생성자 주입 방식**으로 변경하세요.
2. 조건:

   * `@Autowired`는 **생성자에만** 사용할 것 (또는 생략 가능하게 설계)
   * 필드에는 `@Autowired`를 붙이지 않는다.
   * `OrderService`의 필드는 `final`로 선언해도 동작하도록 작성

---

## 🧩 문제 4. 구현체 교체가 쉬운 구조 만들기 (DI)

아래는 할인 정책 인터페이스와 구현체입니다.

```java
public interface DiscountPolicy {
    int discount(int price);
}

public class FixDiscountPolicy implements DiscountPolicy {
    @Override
    public int discount(int price) {
        return price - 1000;
    }
}

public class RateDiscountPolicy implements DiscountPolicy {
    @Override
    public int discount(int price) {
        return (int)(price * 0.9);
    }
}
```

### ✏️ 요구사항

1. `OrderService` 클래스를 추가로 작성하세요.

   * 생성자 주입을 사용해 `DiscountPolicy`를 주입받도록 설계하세요.
2. 스프링 설정 파일(AppConfig 또는 어노테이션 방식)에서

   * 처음에는 `FixDiscountPolicy`를 주문 서비스에 주입하도록 설정
   * 나중에 **코드 수정 없이 설정만 바꿔서** `RateDiscountPolicy`를 사용하도록 변경 가능하게 작성

> 🔍 핵심 포인트:
>
> * **인터페이스(DiscountPolicy) 의존**
> * **구현체(Fix vs Rate)는 외부에서 주입**

---


## 🧩 문제 5. 스프링 컨테이너에서 직접 빈 꺼내오기

다음과 같이 프로젝트가 구성되어 있다고 가정합시다.

```java
@Component
public class HelloService {
    public String say() {
        return "Hello";
    }
}

@Component
public class HelloController {
    private final HelloService helloService;

    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }

    public void handle() {
        System.out.println(helloService.say());
    }
}
```

### ✏️ 요구사항

1. 위 컴포넌트들을 **자동 스캔**해서 등록하는 설정 클래스를 작성하세요.

   * `@Configuration`
   * `@ComponentScan` 사용
2. `main` 메서드에서:

   * 스프링 컨테이너를 생성
   * `HelloController` 빈을 가져온 뒤 `handle()` 호출

컨텍스트 생성 예시는 아래처럼 시작하시면 됩니다.

```java
public class App {
    public static void main(String[] args) {
        // TODO: AnnotationConfigApplicationContext 사용
    }
}
```

---

## 🧩 문제 6. 빈 생명주기 로그 찍기 (조금 응용)

입문 수준에서 “스프링이 객체를 관리한다”는 걸 눈으로 보여주는 문제입니다.

```java
@Component
public class ConnectionManager {

    public ConnectionManager() {
        System.out.println("생성자 호출: ConnectionManager");
    }

    public void connect() {
        System.out.println("DB 연결!");
    }

    public void close() {
        System.out.println("DB 연결 종료");
    }
}
```

### ✏️ 요구사항

1. 위 클래스를 **스프링에서 관리되는 빈**으로 사용하면서,
   애플리케이션 시작 시 `connect()`, 종료 시 `close()`가 자동으로 호출되게 만들어 보세요.
2. 힌트:

   * `@PostConstruct`
   * `@PreDestroy`
3. `main`에서 컨테이너를 생성하고, 빈을 한 번만 사용한 뒤 컨테이너를 종료해 보세요.


