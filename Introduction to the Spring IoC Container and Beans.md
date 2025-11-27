🌱 Spring IoC Container와 Bean 소개
Spring Framework는 자바 애플리케이션 개발에서 가장 널리 사용되는 프레임워크 중 하나입니다. 그 핵심에는 제어의 역전(Inversion of Control, IoC) 
원칙을 구현한 IoC 컨테이너가 있습니다.(클래스다 ) 이 글에서는 Spring의 IoC 컨테이너와 Bean 개념에 대해 구체적으로 살펴보겠습니다.
🔹 타이트 커플링 예 (나쁜 예)     타이트 커플링처럼 너무 강하게 묶여 있으면 변경어렵다 루즈 커플링으로 !!!ioc 
🔹 IoC(제어의 역전)
“원래 내가 하던 일을 스프링이 대신 한다.”                     IoC → DI → 루즈 커플링
🔹 DI(의존성 주입)
“스프링이 객체를 만들어서 필요한 곳에 넣어준다.”
🔹 루즈 커플링
“그래서 클래스들이 서로 딱 붙지 않고 느슨하게 연결된다.”
```
public class OrderService {
    private MySQLRepository repo = new MySQLRepository();
}
```
OrderService는 MySQLRepository에 딱 묶여 있음

MySQL → Oracle로 바꾸면 코드 수정해야 함

✔ 루즈 커플링 예 (좋은 예)
public class OrderService {
    private Repository repo;

    public OrderService(Repository repo) {
        this.repo = repo;
    }
}rol(IoC)란?
IoC는 객체 간의 의존성 관리에 대한 제어 권한을 애플리케이션 코드가 아닌 컨테이너에게 위임하는 설계 원칙입니다. 

💡 전통적인 방식: 객체가 직접 다른 객체를 생성하거나 검색
💡 IoC 방식: 컨테이너가 객체를 대신 생성하고 필요한 의존 객체를 주입
 

💉 IoC의 구체적인 구현: Dependency Injection(DI)
Spring에서는 IoC의 구현 방법으로 DI(의존성 주입)를 사용합니다. DI는 객체가 스스로 의존 객체를 생성하거나 찾지 않고, 
필요한 의존성을 외부에서 주입받는 방식입니다.

 

✅ DI 방식
DI는 아래와 같은 방식으로 의존성을 정의합니다:
```
Constructor arguments  생성자 방식
생성자 주입 (DI)
    // Spring IoC 컨테이너가 UserRepository 객체를 찾아서 이 생성자를 통해 주입해줍니다.
    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
        System.out.println("[UserService] 객체 생성 및 UserRepository 의존성 주입 완료.");
    }
팩토리 메서드의 아규먼트(이 팩토리 메서드는 GoF의 Factory Method 디자인 패턴이 아님)@bean 있는 팩토리 메서드``` public foo makeFoo(){ 이게 Factory Method 이다 
                                                                                                                 return new Foo();```  호출할때 AOP로나온다 
객체 생성 이후 설정되는 프로퍼티 (setter injection) 
Spring IoC 컨테이너는 이러한 정의를 기반으로 bean을 생성하면서 의존 객체를 자동으로 주입합니다. 이 구조는 객체가 직접 의존 객체를 생성하거나 찾는 방식(Service Locator 패턴 등)과는 반대이며, 이 점에서 제어의 역전(Inversion of Control)이라 불립니다.
```
public class Main {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		
		ApplicationContext ac =  이게 창고 역할을 한다 스프링에서 만든 객체들을 전부 담고있다 
				new AnnotationConfigApplicationContext(Config.class);
		
		Foo gotFoo = (Foo) ac.getBean("makeFoo"); = 빈으로 팩토리 메서드 
		gotFoo.setName("Your name is Foo");
		System.out.println(gotFoo.getName());

	}

}```
🏗️  Spring IoC 컨테이너의 핵심 패키지
Spring IoC 컨테이너의 핵심 구현은 다음 두 패키지에 있습니다:

```org.springframework.beans       라브이러리에있는 pom -spring context 오픈소스다운받으면 나옴  
org.springframework.context
 

⚙️ BeanFactory vs ApplicationContext```
🧰 BeanFactory

Spring IoC 컨테이너의 가장 기본적인 인터페이스
모든 타입의 객체를 구성 및 관리 가능
매우 가볍고 유연한구성  프레임워크
하지만 엔터프라이즈 애플리케이션에 필요한 다양한 기능은 부족합니다.

 

🏢 ApplicationContext (BeanFactory의 하위 인터페이스) 자식

ApplicationContext는 BeanFactory보다 풍부한 기능을 제공합니다:

기능	설명
🎯 AOP 통합	Spring AOP와의 손쉬운 통합 지원
🌐 국제화 지원	메시지 리소스 처리 (i18n)
📢 이벤트 발행	컨테이너 이벤트 처리 기능
🌍 웹 애플리케이션 특화 컨텍스트	WebApplicationContext 등 지원
 

🔍 정리하면:   구성이라는게 중요한 단어 키워드이다!! 어노테이션을  붙여야지만 이클래스가 스프링 컨테이너가빈에대한 정보  구성을 가지고있구나라고 생각함 
BeanFactory: 최소한의 구성 기능 제공
ApplicationContext: BeanFactory의 상위 superset으로, 엔터프라이즈 애플리케이션에 필수적인 기능들을 포함
📌 본 장에서는 설명의 일관성을 위해 전반적으로 ApplicationContext를 기준으로 설명합니다. BeanFactory
의 사용은 별도 섹션에서 다룹니다.
 

🫘 Bean이란?
Spring에서 bean은 IoC 컨테이너가 관리하는 핵심 객체입니다. 

 

🎯 Bean의 정의
Spring IoC 컨테이너에 의해 생성, 조립, 관리되는 객체
실제로는 단순한 자바 객체(POJO)이지만, 컨테이너의 관리 대상이 되면 Bean이라 부름
📌 다시 말해, 애플리케이션 내부에는 수많은 객체가 존재하지만, 그 중 Spring 컨테이너에 의해 관리되는 객체만이 "bean"입니다.
 

🧩 Bean 구성 정보: Configuration Metadata
Spring IoC 컨테이너는 Bean과 그 사이의 의존성을 이해하기 위해 구성 메타데이터(configuration metadata)를 사용합니다. 이 메타데이터는 보통 다음 형태로 제공됩니다:

XML 설정(옛날방식) 안쓴다 `
Java Config (@Configuration, @Bean) 이거  @걸써야지 정보를 컨테이너한테 준다 
Annotation 기반 자동 감지 (@Component, @Service, @Repository 등)이거 사용 
🧠 구성 메타데이터는 IoC 컨테이너에게 "무엇을, 어떻게" 생성하고 연결해야 할지를 알려주는 설계도입니다.
 

🧭 정리
요소	설명
🎯 IoC	객체 생성 및 의존성 주입 제어를 컨테이너가 수행
💉 DI	IoC의 구현 방식: 생성자, 메서드, 프로퍼티 기반 주입
🏗️ BeanFactory	IoC의 기본 컨테이너 인터페이스
🏢 ApplicationContext	BeanFactory + 추가 기능 (AOP, 이벤트, i18n 등)
🫘 Bean	Spring IoC 컨테이너가 관리하는 애플리케이션 객체
🧾 구성 메타데이터	Bean과 의존성 정보를 기술하는 설계도
 

✅ 요약
Spring의 IoC 컨테이너는 객체 생명주기와 의존성 관리를 프레임워크 차원에서 처리함으로써,
애플리케이션의 유연성과 테스트 용이성을 크게 향상시켜줍니다.
이 개념은 Spring을 제대로 이해하기 위한
첫 관문이기도 하니, 확실히 익혀두시길 바랍니다! 💪
```
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() { static 이먼저 싱글턴 private static 일때                                return clientService; // static 메서드가 객체를 반환
명령 프롬프트에 검색
절대경로 알아보기 C:
..알리아싱?이라고생각해라 상대경로 







절대경로 
	
    }
}```

 public 메서드 호출하면 private static final 클라인서비스 호출되면서 인스턴스가 만들어지고 


properties +필드+객


디폴트<value>

client.set tatgetname(target bean.getclass().getsimplename; 체이닝 메서드 호출 세개으 ㅣ스테이먼트를 하나으 ㅣ스테이먼트로 해결할수있음 

스트림=>특정파일읽고씀
// 다음 두 필드로 컨테이너에 의해서 필드 주입이 된다.
	@Value("${jdbc.driver.className}")
    private String driverClassName;

    @Value("${jdbc.url}")
    private String url;
	
	@Bean
    public static PropertySourcesPlaceholderConfigurer properties() {
        PropertySourcesPlaceholderConfigurer configurer = 
        		new PropertySourcesPlaceholderConfigurer();
        
        //configurer.setLocation(example.properties);
        
        Properties properties = new Properties();
        properties.setProperty("jdbc.driver.className", "com.mysql.cj.jdbc.Driver");마리아 db POSTGRESQL드라이버
        properties.setProperty("jdbc.url", "jdbc:mysql://localhost:3306/testdb");//로컬호스트 도메인 이름이 127.0.0.1로 변환됨 (컴퓨터 자기자신을 가리키는 로컬호스트)ㅣocalhost:3306/내부적으로 돈다 루프백 loopback  밖으로 안나간다 os1,2로 가면 나간다 
        configurer.setProperties(properties);
        
        return configurer;
    }	

    @Bean
    public DataSource myDataSource() throws ClassNotFoundException {
    	// Simple implementation of the standard JDBC javax.sql.DataSource interface, 
    	// configuring a plain old JDBC java.sql.Driver via bean properties, 
    	// and returning a new java.sql.Connection from every getConnection call.
    	SimpleDriverDataSource dataSource = new SimpleDriverDataSource();   데이터 소스를 구현한 
    	
    	//Class 클래스를 자주 언급함 : reflection 관련클래스
    	
    	dataSource.setDriverClass((Class<? extends java.sql.Driver>) Class.forName(driverClassName));
    	dataSource.setUrl(url);
        dataSource.setUsername("root");
        dataSource.setPassword("1234");
//        dataSource.getConnection();
        return dataSource;
    }
}

@Configuration
public class AppConfig {
	
	@Bean
    public TargetBean theTargetBean() {
        return new TargetBean();
    }

    @Bean
    public ClientBean theClientBean(/*TargetBean targetBean*/) {
        ClientBean client = new ClientBean();
//        client.setTargetName(targetBean.getClass().getSimpleName()); // Bean 이름 설정
        client.setTargetName("theTargetBean");
        return client;
    }
    
    @Bean
    public ManagerBean theManagerBean() {
    	ManagerBean managerBean = new ManagerBean();
    	managerBean.setTargetBean(theTargetBean());이건 타겟 빈을 호출하는메서드가 아니
    	managerBean.setClientBean(theClientBean());
    	return managerBean;
    }
	
}

public class MainApplication {	
	
	public static void testPropertiesFile() {
		
		// Spring Framework 기반의 작업을 할때,
		// 직접적으로 Properties 클래스를 사용할 일은 거의 없을 것
		Properties properties = new Properties();

        // 클래스패스 기준으로 properties 파일 읽기
		// try resource catch
        try (BufferedInputStream inputStream = 
        		(BufferedInputStream) MainApplication.class.
        		getClassLoader().getResourceAsStream("config.properties")) {

            if (inputStream == null) {
                System.out.println("config.properties 파일을 찾을 수 없습니다.");
                return;
            }

            // Properties 로드
            properties.load(inputStream);

            // 값 읽기
            String appName = properties.getProperty("app.name");
            String appVersion = properties.getProperty("app.version");
            String appAuthor = properties.getProperty("app.author");
                        

            // 출력
            System.out.println("App Name    : " + appName);
            System.out.println("App Version : " + appVersion);
            System.out.println("App Author  : " + appAuthor);

        } catch (IOException e) {
            e.printStackTrace();
        }
    
	}
	
	public static void main(String[] args) throws SQLException, IOException {
		
		testPropertiesFile();
	
		ApplicationContext context = 
				new AnnotationConfigApplicationContext(AppConfig.class);
		
		DataSource dataSource = context.getBean(DataSource.class);
		
		System.out.println(dataSource.getConnection());
	}
}

public class AppConfig {

    // Define MovieFinder bean
    @Bean
    public MovieFinder movieFinder() {
        return new SimpleMovieFinder();
    }

    // Define SimpleMovieLister bean
    @Bean
    public SimpleMovieLister movieLister(/*MovieFinder movieFinder*/) {
    	SimpleMovieLister movieLister = new SimpleMovieLister();
        movieLister.setMovieFinder(movieFinder()); // Setter-based DI
        return movieLister;
        
        //return new SimpleMovieLister(movieFinder);
    }
    
    // @Value 어노테이션으로 주입되려면, 반드시 다음 configure가 필요함!
    // Placeholder? 란? : 구글 검색창에 디폴트로 나타나는 "Google 검색 또는 URL 입력" 이 스트링을 플레이스홀더
    @Bean
    public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
        PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
        // 절대 경로=C:\development\Workspace\codes\spring_legacy\SpringIoCDemos\src\main\resources\example.properties
        // The default class loader will be used for loading the resource.
        configurer.setLocation(new ClassPathResource("example.properties"));
        return configurer;
    }
}
ASSOCIATION 연관관계 나옴 논리적연결 uml 릴레이션 구글= 프로필보기
방향성
종속적-케스케이드 
집계모델
트랜잭션
부모를통해서만 외부에서 ->AGGREGATE 하나의 트랜잭션  엔티티 조작
도메인은 비즈니스가 해결하고자하는 문제 영역전체
DDD AGGRE모델과 같음 
many to many 관계는 만들면 xxxx


비교 연관vs의존=연관관계만생각함..ㅎ 
프록시 가짜  공통관심사=나온게프록시
log=콘솔에출력 하는걸 기록하는거

자바 다이나믹 프록시 //리프렉션 파트 만gtp
클라이언트 에서-프록시 진짜 서비스 두가지중에서 서비스말고 프록시에(공통관심사) 기능을 넣음 +프록시안에다가 진짜서비스 부르는 메서드 무조건 들어가야함 
진짜 서비스는 특정 인터페이스를 (임의 인터페이스를 구현해야함 )
프록시를 런타임때 서비스에서 오버라이드기능사용해서 기능추가 (공통관심사)
cglib=인터페이스가없거나 

aspevtj 공통관심사를 타켓클래스에게 넣기위해 프록시같은것이 필요없지?공통관리정리할려고
public void xxx(){
int a=0;

싱글턴stateless 상태가 없다  stateful 상태가 있음 (존재있음)재사용중 공유중 충돌될까봐
싱글톤 주입하면 한번생성하고 ->필요할때마다 새인스턴스 생성


