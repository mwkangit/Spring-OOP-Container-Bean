# Before Starting Spring



- #### Spring은  EJB에서 발전한 프레임워크이다. EJB의 매우 어려운 로직을 해결하기 위해 spring이 탄생하여 객체 지향형 프로그래밍을 사용하기 용이해졌다.

- #### Spring Boot는 스프링을 도와주는 도구로 다른 기능을 사용하기 쉽게 해준다. Reactive programming을 가능하게 하여 non-blocking과 asynchronous programming이 가능하다.

- #### 객체 지향형 프로그램에는 5가지 원칙이 있다.

  #### SOLID

  - #### SRP : 단일 책임 원칙으로 한 클래스는 하나의 책임만 가져야 한다.  변경 사항이 있을 때 파급 효과가 적으면 단일 책임 원칙이 잘 적용된 것이다.

  - #### OCP : 개방-폐쇄 원칙은 확장에는 열려 있으나 변경에는 닫혀 있어야 한다. 즉, 새로운 클래스를 생성해도 client 코드는 변경하지 않고 그대로 사용해야 한다. 객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다. Spring Container의 DI, IoC를 사용해야 구현가능 하다

  - #### LSP : 리스코프 치환 원칙은 정확한 기능을 구현해야한다는 것이다. 즉, 하위 클래스는 인터페이스 규약을 지켜야 한다.

  - #### ISP : 인터페이스 분리 원칙은 하나의 큰 인터페이스를 적절한 여러개의 인터페이스로 관리하여 객체 지향형을 만드는 것이다.

  - #### DIP : 의존관계 역전 원칙은 클라이언트가 인터페이스만 의존해야한다는 것이다. 하지만 인터페이스는 하위 클래스에 상속되어 객체를 만들 때 클라이언트는 하위 클래스도 의존하게 된다. DIP는 spring container의 DI, IoC를 사용해야 구현가능 하다.



# Member



- ### 다형성과 SOLID를 반영하기 위해 각 객체는 인터페이스로 형성 후 상속하여 클래스로 구현한다.

![Member Domain Relation](https://user-images.githubusercontent.com/79822924/138109532-bec6ac10-e6bc-4de2-849c-2f27f08dde06.png)

![Member Class Diagram](https://user-images.githubusercontent.com/79822924/138109560-fe4de46e-5a02-4998-9aa0-363605d041b8.png)

- #### Member 도메인과 클래스 다이어그램을 통해 member의 다형성을 확보하기 위해 여러개의 인터페이스와 클래스로 나누었다.



```java
public class Member {

    private Long id;
    private String name;
    private Grade grade;

    public Member(Long id, String name, Grade grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Grade getGrade() {
        return grade;
    }

    public void setGrade(Grade grade) {
        this.grade = grade;
    }
}

```



```java
public class MemoryMemberRepository implements MemberRepository{

    // 원래 concurrent HashMap 사용해야 한다
    private static Map<Long, Member> store = new HashMap<>();

    @Override
    public void save(Member member) {
        store.put(member.getId(), member);
    }

    @Override
    public Member findById(Long memberId) {
        return store.get(memberId);
    }
}

```



```java
public class MemberServiceImpl implements MemberService{

    // interface에 의존중이지만 구현체에도 의존중이다. 즉, 추상화에도 의존하고 구체화에도 의존한다. DIP 위반
    private final MemberRepository memberRepository = new MemoryMemberRepository();



    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}

```

- #### 인터페이스를 생성하여 어떠한 기능을 사용할 것인지 명시한 뒤 데이터를 다루는 repository와 client가 접근 할 service를 나누어서 프로그래밍 하였다.



# Order



![Order Domain](https://user-images.githubusercontent.com/79822924/138109616-4635ba99-63ec-43bb-9627-60bb00364149.png)

![Order Class Diagram](https://user-images.githubusercontent.com/79822924/138109651-6a99ae61-2f6a-4845-babe-be13d4f3c1e7.png)

- #### Order 도메인과 클래스 다이어그램을 통해 사용할 메소드를 지정하여 다형성을 확보하였다.



```java
public class Order {

    private Long memberId;
    private String itemName;
    private int itemPrice;
    private int discountPrice;

    public Order(Long memberId, String itemName, int itemPrice, int discountPrice) {
        this.memberId = memberId;
        this.itemName = itemName;
        this.itemPrice = itemPrice;
        this.discountPrice = discountPrice;
    }

    public int calculatePrice(){
        return itemPrice - discountPrice;
    }

    public Long getMemberId() {
        return memberId;
    }

    public void setMemberId(Long memberId) {
        this.memberId = memberId;
    }

    public String getItemName() {
        return itemName;
    }

    public void setItemName(String itemName) {
        this.itemName = itemName;
    }

    public int getItemPrice() {
        return itemPrice;
    }

    public void setItemPrice(int itemPrice) {
        this.itemPrice = itemPrice;
    }

    public int getDiscountPrice() {
        return discountPrice;
    }

    public void setDiscountPrice(int discountPrice) {
        this.discountPrice = discountPrice;
    }

    // order 객체를 출력하면 toString()이 출력된다
    @Override
    public String toString() {
        return "Order{" +
                "memberId=" + memberId +
                ", itemName='" + itemName + '\'' +
                ", itemPrice=" + itemPrice +
                ", discountPrice=" + discountPrice +
                '}';
    }
}

```



```java
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        // 단일 책임 원칙 잘지켰다. 할인에 대한것은 할인 객체만 변경해주면 되기 때문이다.
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}

```



```java
public class FixDiscountPolicy implements DiscountPolicy{

    private int discountFixAmount = 1000; // 1000원 할인

    @Override
    public int discount(Member member, int price) {
        // enum은 == 사용 가능하다
        if(member.getGrade() == Grade.VIP){
            return discountFixAmount;
        } else {
            return 0;
        }
    }
}

```

```java
public class RateDiscountPolicy implements DiscountPolicy {

    private int discountPercent = 10;

    @Override
    public int discount(Member member, int price) {
        if(member.getGrade() == Grade.VIP){
            return price * discountPercent / 100;
        } else {
            return 0;
        }
    }
}

```

- #### Order에 toString() 메소드를 적용하여 내부의 데이터를 모두 출력할 수 있게 하였다.

- #### 정액 할인과 정률 할인은 도메인에 작성한대로 객체화하여 따로 구현하여 정률 할인 도입 예정이면 간편하게 변경할 수 있도록 하였다.



![DIP, OCP 불충분](https://user-images.githubusercontent.com/79822924/138109708-600fcc48-7996-4ac0-bb3f-51934b61a4cb.png)

```java
//    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
```



- #### DiscountPolicy  인터페이스를 FixDiscountPolicy, RateDiscountPolicy 두개로 나누고 상속하여 다형성을 완성한 것 같지만 아니다. OrderServiceImpl 클래스에서 discountPolicy를 정액 혹은 정률 할인 클래스로 연결해줘야 한다. 이때 정액, 정률 클래스에도 의존하게 되므로 DIP를 만족하지 못 한다. 또한, 할인 정책을 바꿀 때마다 new로 객체를 client 코드에서 변경해줘야 하므로 OCP도 만족하지 못 한다.



```java
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
```



- #### 객체 지향형 프로그래밍을 준수하기 위해 client 코드에서 인터페이스에만 의존하도록 해야 한다.

- #### 현재 NPE(Null Pointer Exception)이 발생하므로 다른 클래스 및 메서드에서 구현 객체를 대신 생성하고 주입해야 한다.



# Separating Interest



- ### 관심사 분리는 객체를 생성하고 연결하는 역할과 실행하는 역할을 명확하게 분리하는 것이다.

- ### Application의 전체 동작 방식을 구성하기 위해, 구현 객체를 생성하고, 연결하는 책임을 가지는 별도의 설정 클래스인 AppConfig를 생성한다.(구성 및 설정 클래스)

![AppConfig 생성자 주입](https://user-images.githubusercontent.com/79822924/138109786-41c959c5-e469-4efc-90d9-d29be6dc3f2f.png)

```java
public class AppConfig {

    // service에 DIP를 확보하기 위해 객체를 주입하는 생성자 주입을 구현한다
    public MemberService memberService(){
        return new MemberServiceImpl(memberRepository());
    }

    // memberService, orderService에 memoryRepository를 생성한다
    // 이렇게 하여 각자의 역할을 나눠 줄 수 있다
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public OrderService orderService(){
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy(){
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```

```java
// MemberServiceImpl 생성자 주입을 위한 생성자 추가
public class MemberServiceImpl implements MemberService{

    // interface에 의존중이지만 구현체에도 의존중이다. 즉, 추상화에도 의존하고 구체화에도 의존한다. DIP 위반
//    private final MemberRepository memberRepository = new MemoryMemberRepository();

    private final MemberRepository memberRepository;

    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```



```java
// OrderServiceImpl 생성자 주입을 위한 생성자 추가
public class OrderServiceImpl implements OrderService{

//    private final MemberRepository memberRepository = new MemoryMemberRepository();
    // DIP 위반 코드 (new 로 구체 클래스도 의존)
//    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
//    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
    // DIP 준수 코드
    // 완성하기 위해 DiscountPolicy를 알아서 주입해주는 클래스나 메소드를 만들어줘야한다.
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

- #### AppConfig에서 application의 실제 동작에 필요한 구현 객체를 생성한다.

- #### 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(injection)해준다. 이러한 로직을 생성자 주입이라고 한다.

- #### 생성자 주입을 통해 DIP를 완성할 수 있다.

- #### DI(Dependency Injection)은 의존관계를 외부에서 주입한다는 뜻이다.

- #### Config 클래스 또한 refactoring하여 역할에 따른 구현이 잘 보일 수 있게 해야한다.(중복 제거 후 변경에 용이하게 한다)

![사용, 구성 영역](https://user-images.githubusercontent.com/79822924/138109863-90c2148b-1841-479d-a533-74c12cce41a1.png)

- #### AppConfig로 application이 사용 영역과 객체를 생성하고 구성하는 영역으로 분리되었다.

- #### 어떠한 변경 사항이 적용될 때 사용 영역의 코드는 변경하지 않으며 구성 영역 코드만 변경한다.

- #### 변경사항이 적용되어도 client 코드는 변경해 줄 필요가 없으므로 OCP가 완성되었다.



# IoC, DI



- ### IoC(Inversion of Control)은 제어의 역전으로 개발자가 호출하는게 아닌 framework가 제어권을 통제하여 대신 호출하는 것이다. 즉, AppConfig가 제어권을 가진다. AppConfig가 정해주는대로 인터페이스가 구현 객체를 실행한다.

- #### Framework는 내가 작성한 코드를 제어하고, 대신 실행하는 것이다. 테스트 수행 시 JUnit test framework가 자신만의 lifecycle생성 후 제어권을 가져서 대신 실행한다.

- #### Library는 개발자가 작성한 코드가 직접 제어의 흐름을 담당하여 직접 불러서 호출하는 것이다.

- #### DI(Dependency Injection)은 의존관계 주입으로 정적인 클래스 의존 관계, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계로 분리된다.

  - #### 정적인 클래스 의존관계 : import 코드 및 클래스 다이어그램을 확인하여 application을 실행하지 않아도 의존관계를 확인할 수 있다.

  - #### 동적인 객체 인스턴스 의존 관계 : Application 실행 시점에 생성된 객체 인스턴스의 참조가 연결된 의존 관계다.

- #### DI를 사용하여 client 코드를 변경하지 않고 client가 호출하는 대상의 타입 인스턴스를 변경할 수 있다. 또한, 정적인 클래스 의존관계를 변경하지 않고 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다.

- #### AppConfig와 같이 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너, DI 컨테이너라고 한다. Applcation을 한 클래스에서 조립한다고 하여 어샘블러, 오브젝트 팩토리라고도 한다.



# Spring Base Refatoring



```java
@Configuration
public class AppConfig {

    // service에 DIP를 확보하기 위해 객체를 주입하는 생성자 주입을 구현한다
    @Bean
    public MemberService memberService(){
        return new MemberServiceImpl(memberRepository());
    }

    // memberService, orderService에 memoryRepository를 생성한다
    // 이렇게 하여 각자의 역할을 나눠 줄 수 있다
    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService(){
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy(){
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}

```

```java
// MemberApp에 Spring Container사용
public class MemberApp {

    public static void main(String[] args) {
//        AppConfig appConfig = new AppConfig();
//        MemberService memberService = appConfig.memberService();
//        MemberService memberService = new MemberServiceImpl();

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

        // Long 타입이므로 L 붙인다
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member " + member.getName());
        System.out.println("find Member = " + findMember.getName());

    }
}
```

```java
// OrderApp에 Spring Container사용
public class OrderApp {
    public static void main(String[] args) {

//        AppConfig appConfig = new AppConfig();
//        MemberService memberService =appConfig.memberService();
//        OrderService orderService = appConfig.orderService();
//        MemberService memberService = new MemberServiceImpl(null);
//        OrderService orderService = new OrderServiceImpl(null, null);

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        OrderService orderService = applicationContext.getBean("orderService", OrderService.class);

        Long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);

        Order order = orderService.createOrder(memberId, "itemA", 10000);

        System.out.println("order = " + order.toString());
        System.out.println("order.calculatePrice() = " + order.calculatePrice());


    }
}

```

- #### @Configuration은 application의 구성정보를 담당한다는 뜻이다.

- #### @Bean은 spring container에 등록할 항목을 지정하는 것이다.

- #### Spring은 모든 것이 ApplicationContext로 시작된다. Spring container이라고 생각해도 되며 bean이 등록된 객체를 관리한다.

- #### ApplicationContext에 configuration은 AnnotationConfigApplicationContext 선언하며 인자로 configuration 클래스를 넣어서 내부의 bean을 모두 등록한다.

- #### Application.getBean()은 spring container에 등록된 bean을 가져오는 것으로 첫 번째 인자로 메서드명을 넣고 두 번째 인자로 타입을 넣는다.

- #### Spring container에 등록된 객체를 spring bean이라고 한다.



# Spring Container & Bean



- #### ApplicationContext를 spring container이라고 부르며 인터페이스여서 다형성이 적용된다.

- #### AnnotationConfigApplicationContext는  java annotation기반  config설정으로 spring bean을 만든다는 뜻이다.


![스프링빈등록](https://user-images.githubusercontent.com/79822924/138466945-aee3344e-7c76-4d46-a72f-820e5c56ea15.png)

- #### 형성된 스프링 컨테이너에 key, value형태로 빈을 등록한다.

- #### 빈 이름은 key로 메소드 이름이 default로 들어간다.

- #### 빈 객체는 value로 반환하는 값이 들어간다.


![스프링빈의존관계완료](https://user-images.githubusercontent.com/79822924/138466978-6a05b33b-d2aa-47c6-b5b7-aa6716e497c7.png)


- #### 스프링 컨테이너는 설정 정보를 참고하여 의존관계(DI)를 주입한다.

- #### 자바코드로 스프링 빈을 등록하면 생성자를 호출하면서 의존관계 주입도 한번에 처리된다.

```java
    @Test
    @DisplayName("애플리케이션 빈 출력하기")
    public void findApplicationBean() throws Exception {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION){
                Object bean = ac.getBean(beanDefinitionName);
                System.out.println("name = " + beanDefinitionName + " object = " + bean);
            }

        }

    }
```

- #### getBeanDefinitionNames()는 스프링에 등록된 모든 빈 이름을 조회한다.

- #### getRole()에서 ROLE_APPLICATION은 일반적으로 사용자가 정의한 빈이고 ROLE_INFRASTRUCTURE은 스프링이 내부에서 사용하는 빈이다.

- #### getBean()을 통해 등록된 빈을 조회한다. 파라미터로는 빈 이름, 반환형을 넣는다(빈 이름만 넣어도 실행 가능하다).

- #### 스프링 빈을 타입으로 조회 시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생하여 빈 이름을 넣어서 사용해야한다.

- #### getBeanOfType()을 사용하면 해당 타입의 모든 빈을 조회할 수 있다.

- #### 스프링 비은 상속관계를 반영하여 부모 타입으로 조회하면 자식 타입도 함께 조회한다. 즉, object 타입으로 조회하면 모든 스프링 빈을 조회한다.

- #### 빈에 등록할 때에는 인터페이스를 반환하는 것이 의존관계 생성 시 유용하다.

- #### BeanFactory는 스프링 컨테이너의 최상위 인터페이스이며 스프링 빈을 관리하고 조회한다.

- #### ApplicationContext는 BeanFactory의 빈 관리기능과 편리한 부가기능을 이용하는 것이다(ApplicationContext는 BeanFactory이외에 다른 인터페이스도 상속받아서 여러 부가 기능을 제공한다).



![ApplicationContext설정사용](https://user-images.githubusercontent.com/79822924/138467018-1a13ceb5-b16a-4814-9626-35f8696cd138.png)

- #### ApplicationContext에 java, XML, 임의로 제작한 설정을 사용하여 빈 등록을 하여 이용할 수 있다.



![BeanDefinition](https://user-images.githubusercontent.com/79822924/138467043-a582ddf4-4ece-4539-8c1d-7418edf20f49.png)

- #### 스프링 컨테이너는 BeanDefinition을 통해 메타정보를 기반으로 스프링 빈을 생성한다. 스프링 컨테이너는 BeanDefinition을 상속받아서 역할과 구현을 나눴다. 즉, 스프링 컨테이너는 설정정보가 java인지 XML인지 알 필요없이 BeanDefinition을 통해 정보를 인지하면 된다.

- #### BeanDefinition에는 Scope(싱글톤), lazyInit(지연처리 여부) 등 정보가 있다.



# Singleton Pattern



- #### 싱글톤 패턴이란 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.

  

```java
public class SingletonService {

    private static final SingletonService instance = new SingletonService();

    public static SingletonService getInstance(){
        return instance;
    }

    private SingletonService(){
    }

    public void logic(){
        System.out.println("싱글톤 객체 로직 호출");
    }

}

```

- #### 객체를 미리 생성해두는 가장 단순하지만 안전한 싱글톤 구현 방법이다.

- #### 클래스가 자기자신을 내부에 private static으로 하나 가지고 있어서 객체가 class level에 올라가므로 실행 시 딱 1개만 생성된다.

- #### 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 막아서 유일한 객체를 유지한다.

- #### 이러한 방법으로 싱글톤을 이용하면 의존관계상 클라이언트가 구체 클래스에 의존하여 DIP와 OCP 원칙을 위반한다.

- #### 또한, 유연성이 떨어져서 DI를 적용하기 어려운 상황이 발생하기도 한다.



```java
    @Test
    @DisplayName("스프링 컨테이너와 싱글톤")
    void springContainer(){

//        AppConfig appConfig = new AppConfig();
        ApplicationContext ac = new AnnotationConfigApplicationContext((AppConfig.class));

        // 1. 조회 : 호출할 때 마다 객체를 생성
        MemberService memberService1 = ac.getBean("memberService", MemberService.class);

        // 2. 조회 : 호출할 때 마다 객체를 생성
        MemberService memberService2 = ac.getBean("memberService", MemberService.class);

        // 참조값이 다른 것을 확인
        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);

        // memberService1 == memberService2
        assertThat(memberService1).isSameAs(memberService2);
    }
```

- #### 스프링 컨테이너는 싱글톤 패턴 문제점을 해결하고 기본적으로 객체를 싱글톤으로 만들어서 저장한다. 스프링 빈이 싱글톤으로 관리되는 빈이다.

- #### 스프링 컨테이너처럼 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라고 한다.

- #### 스프링 컨테이너로 DIP, OCP, 테스트, private 생성자로부터 자유로울 수 있다.

- #### 스프링 컨테이너는 사용자의 요청이 올 때마다 객체를 생성하는 것이 아니라 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.

- #### 싱글톤 객체는 상태를 유지하게 설계하면 안돼며 무상태로 설계해야 한다. 즉, 의존적인 필드가 있으면 안돼고 읽기만 가능한 값 변경이 가능한 필드는 존재하면 안됀다. 필요하다면 자바에서 공유되지 않는 지역변수, 파라미터, threadlocal을 사용해야 한다. 자칫하면 사용자의 개인 데이터가 변하는 현상이 발생할 수 있다.



```java
    @Bean
    public MemberService memberService(){
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public OrderService orderService(){
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }
    
        @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }
```

- #### memberService와 orderService 빈을 통해 MemberRepository 객체가 연속 생성되어 싱글톤이 깨지는 것처럼 보이지만 MemberRepository는 빈에 싱글톤으로 1개의 객체만 등록된다.

```java
    @Test
    void configurationDeep(){
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        AppConfig bean = ac.getBean(AppConfig.class);

        System.out.println("bean = " + bean.getClass());
    }
```



- #### 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 이용하여 싱글톤에 적용한다.

- #### 빈에 등록된 객체의 클래스의 타입을 출력하면 CGLIB를 볼 수 있는데 이것은 내가 만든 클래스가 아니라 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용하여 @Configuration이 붙은 클래스를 상속받은 임의의 다른 클래스를 만들고 그 다른 클래스를 스프링 빈에 등록한 것이다.

- #### CGLIB로 만들어진 다른 클래스는 @Bean이 붙은 메소드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고 스프링 빈이 없으면 생성하여 등록하고 반환하는 코드가 동적으로 생성되어 싱글톤을 보장한다.

- #### 이때 @Configuration 없이 @Bean만 존재하면 CGLIB 라이브러리를 지원하지 않아서 싱글톤이 보장되지 않을 수 있다.



# Component Scan



```java
@Configuration
@ComponentScan(
        basePackages = {"hello.core"},
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {



}

```

```java
@Component
public class OrderServiceImpl implements OrderService{


    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

- #### @Component를 통해 Bean에 자동으로 등록할 수 있으며 @Autowired를 통해 의존관계를 자동으로 주입할 수 있다.

- #### 인터페이스가 아닌 구현 클래스에 @Component를 작성하는 것이다.

- #### @ComponentScan은 @Component가 붙은 모든 클래스를 스프링 빈으로 등록한다. 이때 default로 클래스명으로 빈에 등록하지만 맨 앞글자만 소문자로 사용한다. @Component에 인자로 빈 이름을 부여할 수 있다.



![Autowired](https://user-images.githubusercontent.com/79822924/138467093-b160b7c7-0c51-4a3c-8fe9-7a30169279f0.png)

- #### @Autowired를 생성자에 지정하면 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 의존관계를 주입한다. 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입하는 것이며 getBean(반환 클래스 타입)과 같은 의미이다.



```java
@ComponentScan(
        basePackages = {"hello.core"}
)
```

- #### basePackages로 탐색할 패키지의 시작위치를 지정할 수 있다. 이 패키지를 포함하여 하위 패키지를 모두 탐색한다.

- #### basePackageClasses로 지정한 클래스의 패키지를 탐색 시작 위치로 지정할 수 있다.

- #### @ComponentScan의 default는 어노테이션이 붙은 클래스의 패키지를 시작 위치로 지정한다.

- #### @ComponentScan이 붙은 설정 정보 클래스는 프로젝트의 최상단에 배치하는 것이 좋다.

- #### 스프링 부트 대표 시작 정보인 @SpringBootApplication도 프로젝트 시작 루트 위치에 배치되어 있으며 이 어노테이션 안에 @ComponentScan이 내재되어 있다.

- #### @ComponentScan은 @Component(컴포넌트 스캔에서 사용), @Controller(스프링 MVC 컨트롤러에서 사용), @Service(스프링 비즈니스 로직에서 사용), @Repository(스프링 데이터 접근 계층에서 사용), @Configuration(스프링 설정 정보에서 사용)을 대상으로 빈에 등록한다.

- #### 각 어노테이션은 스프링이 부가기능을 부여한다. @Controller은 스프링 MVC 컨트롤러로 인식, @Repository는 스프링 데이터 접근 계층으로 인식하고 데이터 계층의 예외를 스프링 예외로 변환하고, @Configuration은 스프링 설정 정보로 인식하고 스프링 빈이 싱글톤을 유지하도록 처리하고, @Service는 특별한 처리는 없지만 핵심 비즈니스 로직이 존재하는 비즈니스 계층을 인식하는데 도움을 준다.

- #### 어노테이션은 상속관계라는 것이 없는 것이 일반적이지만 어노테이션이 특정 어노테이션을 상속하는 것처럼 인식할 수 있는 것은 자바 언어가 지원하는 것이 아닌 스프링이 지원하여 가능한 것이다.



```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```

```java
@MyExcludeComponent
public class BeanB {
}
```

```java
	@Configuration
    @ComponentScan(
            includeFilters = {
                @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class)
                             },
            excludeFilters = {
                @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
                @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class)
            }
        
    )
    static class ComponentFilterAppConfig{
    }
```

- #### 어노테이션을 새로 생성하여 ComponentScan기능을 부여한 뒤 각 어노테이션에 대해 포함할지 결정할 수 있다.

- #### FilterType에는 5가지가 있다.

  - #### ANNOTATION : 기본값이며 어노테이션을 인식하여 동작한다.

  - #### ASSIGNABLE_TYPE : 지정한 타입과 자식 타입을 인식해서 동작한다. 즉, 클래스를 직접 지정한다.

  - #### ASPECTJ : AspectJ 패턴을 사용한다.

  - #### REGEX : 정규 표현식이다.

  - #### CUSTOM : TypeFilter이라는 인터페이스를 직접 구현하여 처리한다.



```java
@Configuration
@ComponentScan(
        basePackages = {"hello.core"},
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {
    @Bean(name = "memoryMemberRepository")
    public MemberRepository memberRepository(){
        return new MemoryMemberRepository();
    }
}
```

```java
@Component
public class MemoryMemberRepository implements MemberRepository{}
```

- #### 자동 빈 등록이 중복되어 충돌이 일어나면  ConflictingBeanDefinitionException 예외가 발생하여 실행을 중지한다.

- #### 자동 빈 등록과 수동 빈 등록이 중복되어 실행되면 스프링 부트에서 오류가 발생하도록 해놓았다.

- #### Application.properties에서 default로 overriding이 false로 되어 있는 것을 true로 바꾸면 수동 빈 등록이 자동 빈 등록보다 우선권을 가지게 되어 overriding이 가능해진다.



# Auto Dependency Injection



```java
@Component
public class OrderServiceImpl implements OrderService {
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;
	@Autowired
	public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
	}
}
```

- #### 생성자 주입은 생성자를 통해 클래스 객체 생성 시점에 바로 의존관계를 주입하는 것이다.

- #### 생성자 주입 시 생성자가 1개이면 @Autowired를 제외할 수 있다.

- #### 생성자 주입은 스프링 컨테이너가 만들어질 때 동시에 의존관계 주입이 일어난다.

- #### 생성자 주입은 의존관계가 어플리케이션 종료 시점까지 1번만 호출되어 불변하게 유지할 수 있다. 하지만 수정자 주입은 setter 메소드를 public으로 열어둬서 누군가가 의존관계를 변경하게 될 수도 있으므로 위험한 설계가 될 수 있다.

- #### 수정자 의존관계인 경우 NPE가 발생할 수 있는데 이것은 setter 메소드를 실행하지 않아서 발생한다. 하지만 이미 setter 메소드를 감싸는 클래스를 선언하지 못하므로 setter 메소드를 실행할 수 없는 문제가 발생한다. 또한, 누락된 의존관계를 관리하기 까다롭다.

- #### 생성자 주입은 final 키워드를 사용하여 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에서 방지할 수 있다. Final 키워드는 선언과 정의를 동시에 해야하는 키워드이다.

- #### 생성자 주입은 프레임워크에 의존하지 않고 순수한 자바 언어의 특징을 잘 살리는 방법이다.

- #### 생성자 주입을 선택하고 필요 시 수정자 주입을 선택하고 필드 주입은 테스트 이외에 사용하지 않는 것이 좋다.



```java
@Component
public class OrderServiceImpl implements OrderService {
	private MemberRepository memberRepository;
	private DiscountPolicy discountPolicy;
	@Autowired
	public void setMemberRepository(MemberRepository memberRepository) {
	this.memberRepository = memberRepository;
	}
	@Autowired
	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
	this.discountPolicy = discountPolicy;
	}
}
```

- #### 수정자 주입은 자바 빈 프로퍼티 규약에 있는 수정자 메소드를 사용하는 방식을 사용한 의존관계 주입 방법이다.

- #### Setter을 호출하는 방식으로 컨테이너의 빈을 조작하는 것이 가능해서 위험하다.

- #### @Autowired는 default로 주입할 대상이 없으면 오류가 발생하지만 @Autowired(required = false)를 통해 선택적 주입을 구현할 수 있다.



```java
@Component
public class OrderServiceImpl implements OrderService {
	@Autowired
	private MemberRepository memberRepository;
	@Autowired
	private DiscountPolicy discountPolicy;
}
```

- #### 필드 주입은 필드에 바로 의존관계를 주입하는 것이다.

- #### 외부에서 변경이 불가능해서 테스트하기 힘들다는 단점이 있다.

- #### DI 프레임워크가 없다면 아무것도 할 수 없다.

- #### 주로 테스트 코드나 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서 사용한다.

- #### @SpringBootTest를 이용하여 스프링 컨테이너를 테스트에 통합하여 @Autowired를 자유롭게 사용할 수 있다. @SpringBootTest는 자동으로 스프링 컨테이너에 테스트 토합하여 사용한다.



```java
@Component
public class OrderServiceImpl implements OrderService {
	private MemberRepository memberRepository;
	private DiscountPolicy discountPolicy;
	@Autowired
	public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
	}
}
```

- #### 일반 메소드 주입은 일반 메소드를 통해 의존관계를 주입하는 것이다.

- #### 한번에 여러 필드를 주입할 수 있지만 생성자 주입으로 모두 가능하기 때문에 잘 사용하지 않는다.



```java
    static class TestBean{

        @Autowired(required = false)
        public void SetNoBean1(Member noBean1){
            System.out.println("noBean1 = " + noBean1);
        }

        @Autowired
        public void SetNoBean2(@Nullable Member noBean2){
            System.out.println("noBean1 = " + noBean2);
        }

        @Autowired
        public void SetNoBean3(Optional<Member> noBean3){
            System.out.println("noBean3 = " + noBean3);
        }

    }
```

- #### 주입할 스프링 빈이 없어도 동작할 수 있는 방법이 3가지 있다.

  - #### @Autowired(required = false) : 자동 주입할 대상이 없으면 수정자 메소드 자체가 호출이 되지 않는다.

  - #### org.springframework.lang.@Nullable : 자동 주입할 대상이 없으면 null 입력된다. 

  - #### Optional<> : 자동 주입할 대상이 없으면 Optional.empty가 입력된다. 값이 있으면 optional로 감싼다. Optional은 값이 null일 수도 있는 조건도 감싸는 자료형으로 많이 사용된다.



```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
	private final MemberRepository memberRepository;
	private final DiscountPolicy discountPolicy;
}
```

```groovy
configurations {
	compileOnly{
		extendsFrom annotationProcessor
	}
}	
dependencies{
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
}
```

- #### Lombok은 객체에 대한 getter, setter을 자동으로 생성하는 어노테이션을 제공하는 동시에 @RequiredArgsConstructor을 통해 생성자 주입을 간편하게 구현할 수 있게 한다. 즉, lombok은 자바의 어노테이션 프로세서 기능을 이용하여 컴파일 시점에 생성자 코드를 자동을 생성한다. 실제 클래스를 열어보면 생성자가 만들어진 것을 확인 할 수 있다.

- #### Gradle에 dependencies를 추가하면 lombok을 이용할 수 있다.



```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {}
```

- #### 타입으로 조회 시 같은 타입의 빈이 2개 이상 존재하면 NoUniqueBeanDefinitionException 오류가 발생한다.

- #### 오류를 피하기 위해 하위 타입을 지정할 수 있지만 이것은  역할이 아닌 구현에 의존하는 것으로 DIP를 위배하여 유연성이 떨어질 수 있다.



```java
@Autowired
private DiscountPolicy rateDiscountPolicy
```

- #### @Autowired는 타입 매칭 시 2개 이상으로 타입이 존재하면 필드 이름, 파라미터 이름으로 매칭을 재시도 한다.



```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
```

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
@Qualifier("mainDiscountPolicy") DiscountPolicy
discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
}
```

- #### @Qualifier는 추가 구분자를 붙여줘서 빈을 구별하도록 하는 방법이다. 빈 이름을 변경하는 것이 아닌 추가적인 구분자를 제공하는 것이다.

- #### 타입 매칭 시 Qualifier끼리 매칭을 시도한 뒤 빈 이름으로 매칭한다. 만약 아직까지 빈을 찾지 못하면 예외를 발생한다.



```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

- #### @Primary로 우선권을 부여하여 타입 매칭을 할 수 있다.

- #### 주요 처리를 위한 부분에는 @Primary를 사용하여 간편하게 빈을 이용하고 서브 처리를 위한 부분에는 @Qualifier을 사용하여 필요한 순간에 빈을 사용하는 방법으로 구현하는 것이 좋다.

- #### 스프링은 자동보다 수동이, 넓은 범위의 선택권보다 좁은 범위의 선택권이 우선 순위가 높아서 @Primary와 @Qualifier이 동시에 사용되면 @Qualifier이 우선권을 가진다.



```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {
}

```

- #### 어노테이션을 직접 생성하여 Qualifier과 같은 function을 직접 구현할 수 있다.



```java
@Test
    void findAllBean(){
        ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

        DiscountService discountService = ac.getBean(DiscountService.class);
        Member member = new Member(1L, "userA", Grade.VIP);
        int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");

        assertThat(discountService).isInstanceOf(DiscountService.class);
        assertThat(discountPrice).isEqualTo(1000);

        int rateDiscountPrice = discountService.discount(member, 20000, "rateDiscountPolicy");

        assertThat(rateDiscountPrice).isEqualTo(2000);
    }

    static class DiscountService{
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policies;

        @Autowired
        public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
            this.policyMap = policyMap;
            this.policies = policies;
            System.out.println("policyMap = " + policyMap);
            System.out.println("policies = " + policies);
        }

        public int discount(Member member, int price, String discountCode) {
            DiscountPolicy discountPolicy = policyMap.get(discountCode);
            return discountPolicy.discount(member, price);
        }
    }
```

- #### List는 이름 없이 인스턴스에 대한 정보를 입력받고 Map은 이름이 key로 저장되고 빈 정보 타입이 value로 저장된다. 즉, 같은 타입으로 되어있는 빈이 모두 Map에 저장되는 것이다.

- #### Map에 입력된 빈은 이름이 key값이므로 이름을 통해 value를 반환할 수 있다.

- #### 만약 해당 타입 빈이 존재하지 않는다면 list와 map은 비어있는 값을 가지게 된다.



## DI in Business



- #### 어플리케이션은 2가지 로직으로 나눌 수 있다.

  - #### 업무 로직 빈 : 컨트롤러, 서비스. 리포지토리 등 비즈니스 요구사항을 개발할 때 추가되거나 변경된다.

  - #### 기술 지원 빈 : 공통 관심사(AOP) 등을 처리할 때 사용되며 업무 로직을 지원하기 위한 하부 기술이나 공통 기술이다.

- #### 업무 로직은 숫자가 많으며 유사한 패턴이 존재하여 자동 기능을 적극 사용하는 것이 좋다.

- #### 기술 지원 로직은 수가 적고 광범위하게 영향을 미치기 때문에 가급적 수동 빈 등록을 사용하여 빠르게 파악할 수 있도록 하는 것이 좋다.

- #### 비즈니스 로직 중에서도 다형성을 적극 활용할 때 수동 빈 등록으로 명확하게 표현하는 것이 좋다. 설정정보를 한눈에 보지 못한다면 오류가 발생해도 탐지하기 어렵다. 또 다른 방법으로는 자동 빈 등록을 해도 특정 패키지에 같이 묶어서 한눈에 알아볼 수 있도록 하는 것이다.

- #### 즉, 스프링 부트가 아닌 내가 직접 기술 지원 객체를 스프링 빈에 등록한다면 수동으로 등록해서 명확하게 들어내는 것이 좋다.



# Bean Lifecycle Callback



```java
private String url;
public NetworkClient() {
	System.out.println("생성자 호출, url = " + url);
	connect();
	call("초기화 연결 메시지");
}
public void setUrl(String url) {
	this.url = url;
}
//서비스 시작시 호출
public void connect() {
	System.out.println("connect: " + url);
}
public void call(String message) {
	System.out.println("call: " + url + " message = " + message);
}
//서비스 종료시 호출
public void disconnect() {
	System.out.println("close: " + url);
}
```

```java
@Test
    public void lifeCycleTest(){
        ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
        NetworkClient client = ac.getBean(NetworkClient.class);
        ac.close();
    }

    @Configuration
    static class LifeCycleConfig{

        @Bean
        public NetworkClient networkClient(){
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
    }
```

- #### 스프링 빈 생성과 삭제의 순간이 존재하여 각 순간에 필요한 로직을 정의할 수 있다.

- #### ConfigurableApplicationContext는 AnnotationConfigApplicationContext의 부모 인터페이스 개념이다.

- #### 현재 코드는 임의로 설정하여  url도 set되어있지 않아서 생성 시점을 알 수 있어도 삭제 시점은 알 수 없다.

- #### 스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메소드를 통해 초기화 시점을 알려주는 기능을 제공한다. 또한 동일하게 스프링 컨테이너가 종료되기 직전에 소멸 콜백 메소드도 제공한다.

- #### 스프링 빈의 라이프사이클은 스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료 로 이루어진다.

- #### 여기서 초기화 콜백은 빈이 생성되고 빈의 의존관계 주입이 완료된 직후에 호출된다. 소멸전 콜백은 빈이 소멸되기 직전에 호출된다.

- #### 객체를 생성하는 생성자 부분과 초기화하는 초기화 부분을 명확하게 나누는 것이 좋다. 초기화 부분은 생성된 값을 활용하여 외부 커넥션 등 무거운 동작을 수행하며 동작을 지연시켜서 사용자 요청 전까지 동작을 안하고 요청 시 바로 초기화하게 만들 수도 있기 때문이다.



```java
public class NetworkClient implements InitializingBean, DisposableBean {
    @Override
    public void afterPropertiesSet() throws Exception
    {
		connect();
		call("초기화 연결 메시지");
    }
	@Override
	public void destroy() throws Exception {
		disConnect();
    }
}
```

- #### InitializingBean 인터페이스는 afterPropertiesSet()메소드로 초기화를 지원한다.

- #### DisposableBean 인터페이스는 destroy()메소드로 소멸을 지원한다.

- #### 이 인터페이스는 스프링 전용 인터페이스이다.

- #### 이름을 변경할 수 없으며 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.



```java
public void init() {
	System.out.println("NetworkClient.init");
	connect();
	call("초기화 연결 메시지");
}
public void close() {
	System.out.println("NetworkClient.close");
	disConnect();
}
```

```java
@Configuration
static class LifeCycleConfig {
	@Bean(initMethod = "init", destroyMethod = "close")
	public NetworkClient networkClient() {
		NetworkClient networkClient = new NetworkClient();
		networkClient.setUrl("http://hello-spring.dev");
		return networkClient;
	}
}
```

- #### @Bean(initMethod = , destroyMethod=) 를 통해 직접 일반 메소드를 초기화 메소드와 소멸 메소드로 지정할 수 있다. 메소드명을 통해 호출 가능하다.

- #### 이 방법은 메소드 이름을 자유롭게 부여할 수 있다.

- #### 스프링 코드에 의존하지 않는다.

- #### 설정 정보를 사용하므로 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 소멸 메소드를 적용할 수 있다.

- #### @Bean의 destroyMethod 속성은 default 값이 (inferred)인데 이것은 close, shutdown이라는 이름의 메서드를 자동으로 호출한다는 뜻이다. 추론 기능을 사용하지 않으려면 destroyMethod =""를 하면 된다.



```java
    // 이걸로 생성 콜백 구현
    @PostConstruct
    public void init() {
        System.out.println("NetworkClient.afterPropertiesSet");
        connect();
        call("초기화 연결 메시지");
    }

    // 이걸로 소멸 콜백 구현
    @PreDestroy
    public void close() {
        System.out.println("NetworkClient.destroy");
        disconnect();
    }
```

```java
        @Bean
        public NetworkClient networkClient(){
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
```

- #### JSR-250 자바 표준을 따르는 방법으로 스프링에 종속적이지 않다.

- #### 외부 라이브러리에는 적용하지 못한다. 만약 외부 라이브러리를 초기화, 소멸 해야하면 @Bean기능을 이용해야 한다.



# Bean Scope



- #### 싱글톤은 기본 스코프이며 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.

- #### 프로토타입은 스프링 컨테이너가 프로토타입 빈 생성, 의존관계 주입, 초기화 까지만 관리하고 더이상 관리하지 않는 짧은 범위의 스코프이다. 초기화 과정이 끝나면 이후에는 client에서 객체를 이용하게 된다.

- #### 웹 스코프에는 3가지가 있다.

  - #### request : 웹 request가 오고 response가 나갈때 까지 유지되는 스코프이다.

  - #### session : 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프이다.

  - #### application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 긴 범위의 스코프이다.



![prototypebean1](https://user-images.githubusercontent.com/79822924/139865663-3f9fbf32-57a8-41df-ad7a-ac9a7b8f5ed4.png)

- #### 스프링 컨테이너는 요청한 빈에 대해 의존관계 주입, 초기화 단계까지 관리한다.



![prototypebean2](https://user-images.githubusercontent.com/79822924/139865699-7abf2503-c1a4-44aa-b97e-1e153ff60b4d.png)

- #### 과정을 마친 빈을 client에 반환하지만 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환한다.

- #### 초기화 과정까지만 스프링 컨테이너가 관리하므로 @PreDestroy와 같은 소멸 메소드는 호출되지 않는다.



```java
    @Scope("prototype")
    static class PrototypeBean{
        @PostConstruct
        public void init(){
            System.out.println("PrototypeBean.init");
        }

        @PreDestroy
        public void destroy(){
            System.out.println("PrototypeBean.destroy");
        }
    }
```

- #### Scope 범위를 프로토타입으로 설정하여 프로토타입 빈을 구현할 수 있다.

- #### 프로토타입 스코프의 빈은 스프링 컨테이너에서 빈을 조회할 때 생성되고, 초기화 메소드도 실행된다.

- #### 생성된 프로토타입 스코프는 스프링 컨테이너가 관리하지 않기 때문에 소멸 메소드를 호출하지 않는다. 그래서 client가 빈을 관리하고 소멸 메소드도 직접 해야한다.



```java
static class ClientBean {
    private final PrototypeBean prototypeBean;
    @Autowired
    public ClientBean(PrototypeBean prototypeBean) {
        this.prototypeBean = prototypeBean;
    }
    public int logic() {
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
}

@Scope("prototype")static class PrototypeBean {
    private int count = 0;
    public void addCount() {
        count++;	
    }
    public int getCount() {
        return count;	
    }
    
    @PostConstruct
    public void init() {
        System.out.println("PrototypeBean.init " + this);
    }
    
    @PreDestroy	public void destroy() {	
        System.out.println("PrototypeBean.destroy");
    }
}
```

- #### 일반적으로 프로토타입 빈 2개 생성후 각각의 client 변수를 증가하면 따로 증가한다.

- #### 위 코드의 경우 싱글톤 빈 이 프로토타입 빈을 의존관계로 주입받고 있으므로 생성된 프로토타입 빈은 소멸되지 않고 싱글톤 빈 내부에 계속 남게 된다. 결국 의존관계를 주입받은 싱글톤 빈에서 계속 프로토타입 빈 인스턴스를 이용하게 되는 것이다.

- #### 프로토타입 빈은 사용할 때마다 생성되어야 효율이 좋기 때문에 이러한 방법은 보완해야한다.



```java
static class ClientBean {
    @Autowired
    private ApplicationContext ac;
    public int logic() {
        PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
}
```

- #### 싱글톤 빈이 프로토타입 빈을 사용할 때마다 스프링 컨테이너에 요청하는 방법이다.

- #### 의존관계를 외부에서 주입(DI)받는게 아니라 직접 필요한 의존관계를 찾는 것을  Dependency Lookup(DL) 의존관계 조회(탐색)이라고 한다.

- #### 위 코도와 같이 ApplicationContext 전체를 주입받게 되면 스프링 컨테이너에 종속적인 코드가 되고 단위 테스트도 어려워진다.



```java
	@Autowired
	private ObjectProvider<PrototypeBean> prototypeBeanProvider;
	public int logic() {
        PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
        prototypeBean.addCount();
        int count = prototypeBean.getCount();
        return count;
    }
```

- #### ObjectProvider의 getObject()는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환하는 DL을 실행한다.

- #### ObjectProvider은 스프링이 자동으로 DI 주입으로 사용이 가능하다.

- #### GetObject() 호출시 그때서야 스프링 컨테이너에서 프로토타입 빈을 찾아서 반환한다.

- #### ObjectProvider은 프로토타입을 위해 있는 것이 아닌 DL을 더 편리하게 도와주기 위한 용도로 사용하며 스프링에 의존적이다.



```java
	@Autowired
	private Provider<PrototypeBean> provider;
	
	public int logic() {
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```

```groovy
implementation 'javax.inject:javax.inject:1'
```

- #### Provider은 JSR-330 자바 표준을 사용하며 gradle에 javax.inject:javax.inject:1 라이브러리를 추가해야 한다.

- #### Provider은 간단하게 get() 함수를 통해 DL을 실행한다. 

- #### 자바 표준을 사용하기 때문에 스프링에 의존적이지 않다.

- #### 두 빈이 서로 의존관계를 형성할 경우 먼저 사용하는 빈에 대해 provider이 알아서 의존관계를 형성하도록 할 수 있다.

- #### 프로토타입 만들 시 주로 ObjectProvider을 이용하게 된다. 대부분 스프링에서 제공하는 기능을 이용하고 특별한 경우 자바 표준을 이용하는 것이 좋다.



![web_scope](https://user-images.githubusercontent.com/79822924/139865755-10cf38e5-69bb-4b74-b554-22544618cebf.png)

- #### 웹 스코프는 웹환경에서만 동작하며 스프링이 해당 스코프의 종료시점까지 관리하여 소멸 메소드가 호출된다.

- #### 웹 스코프는 4가지로 나눌 수 있다.

  - #### request : HTTP 요청이 들어오고 나갈 때까지 유지되는 스코프이며 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고 관리된다.

  - #### session : HTTP Session과 동일한 생명주기를 가지는 스코프이다.

  - #### application :  서블릿 컨텍스트(ServletContext)와 동일한 생명주기를 가지는 스코프이다.

  - #### websocket : 웹 소켓과 동일한 생명주기를 가지는 스코프이다.

- #### 동시 2명이상의 사용자가 HTTP 요청을 해도 각각 다른 스프링 빈이 생성되며 서비스 요청을 해도 각각의 빈에 요청이 들어가서 각각의 서비스가 실행된다.

- #### 즉, HTTP request에 맞춰서 모든 것이 각각 할당된다.



```groovy
implementation 'org.springframework.boot:spring-boot-starter-web'
```

- #### Web환경이 동작하기 위해 web 라이브러리를 gradle에 추가해야한다.

- #### 스프링 부트는 웹 라이브러리가 없으면 AnnotationConfigApplicationContext를 기반으로 어플리케이션을 구동한다. 웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로 AnnotationConfigServletWebServerApplicationContext를 기반으로 어플리케이션을 구동한다.

```groovy
server.port=9090
```

- #### Application.properties에서 포트를 변경할 수 있다.



```java
@Component
@Scope(value = "request")
public class MyLogger {
    private String uuid;
    private String requestURL;
    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }
    
    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }
    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }
    
    @PreDestroy	public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }
}
```

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final MyLogger myLogger;
    @RequestMapping("log-demo")	
    @ResponseBody	
    public String logDemo(HttpServletRequest request){
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final MyLogger myLogger;
    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

- #### UUID는 유일한 고유의 ID라고 할 수 있다. 즉, UUID로 HTTP 요청을 구분할 수 있따.

- #### @Scope(value = "request")로 설정하여 빈이 HTTP 요청 당 하나씩 생성되며 HTTP 요청이 끝나는 시점에 소멸된다. 정확한 빈 생성 시점은 스프링 컨테이너 요청 시점이다.

- #### @ResponseBody는 뷰 화면 없이 문자를 바로 반환할 때 사용한다. 즉, 메소드의 문자열을 그대로 응답으로 보낼 수 있다.

- #### HttpServletRequest는 자바에서 제공하는 표준 servlet 규약으로 고객 요청 정보를 받을 수 있다.

- #### getRequestURL()를 통해 고객이 요청한 URL을 확인할 수 있다.

- #### RequestURL을 저장하는 부분은 컨트롤러보다 공통 처리가 가능한 스프링 인터셉터나 서블릿 필터 같은 곳을 활용하는 것이 좋다.

- #### 인터셉터란 HTTP request가 컨트롤러 호출을 하기 직전에 공통화하여 처리를 하는 것이다.

- #### Request scope을 사용하지 않고 파라미터로 모든 정보를 서비스 계층에 넘기면 파라미터가 많아서 지저분해진다. RequestURL과 같은 웹 관련 정보가 웹과 관련없는 서비스 계층에 넘어가게 된다. 웹 관련 부분은 컨트롤러까지만 사용해야 한다. 서비스 계층은 웹 기술에 종속되지 않고 가급적 순수하게 유지하는 것이 좋다.

- #### 현재 위의 코드에 의존관계 주입 시 request가 오지 않았기 때문에 request 빈이 생성되지 않았다. 따라서 위 코드는 오류를 발생하여 실행되지 않는다.



```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;
    @RequestMapping("log-demo")	
    @ResponseBody	
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final ObjectProvider<MyLogger> myLoggerProvider;
    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

- #### MyLogger을 찾을 수 있는 DL인 ObjectProvider을 주입하는 방법으로 해결 가능하다.

- #### 이전에는 의존관계를 바로 형성하려고 했지만 지금 코드는 의존관계를 사용자의 request가 온 시점에 형성하여 새로운 request 빈을 만든다.

- #### ObjectProvider을 사용하여 getObject() 호출 시점까지 request scope 빈 생성을 지연할 수 있다. 즉, 스프링 컨테이너에 요청하는 시점을 지연하는 것이다.

- #### getObject()를 컨트롤러와 서비스 계층에서 각각 한번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환된다.



```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {}
```

- #### Proxy를 사용하여 이전의 오류가 발생하던 코드를 정상적으로 실행되게 만들 수 있다.

- #### ProxyMode에서 적용대상이 클래스이면 TARGET_CLASS를 선택하고 인터페이스이면 INTERFACES를 선택한다.

- #### 프록시는 가짜 클래스를 만들어서 사용하는 방법이다.

- #### CGLIB 라이브러리는 바이트코드 조작을 통해 지정된 클래스에 대한 가짜 클래스를 생성하여 의존관계 주입에 사용한다. 그 후 프록시 객체의 기능을 수행하고자할 때 진짜 클래스를 호출하여 의존관계를 형성 후 사용하게 된다.



![proxy](https://user-images.githubusercontent.com/79822924/139865812-774fec59-7ff2-4ec1-a78d-017133d83ddd.png)

- #### 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.

- #### 사용자가 프록시에게 로직을 호출하면 프록시는 진짜 객체의 로직을 호출하여 사용자 입장에서 사실 원본인지 아닌지 모르게 동일하게 사용이 가능하여 다형성을 형성할 수 있다.

- #### 프록시 객체로 client는 싱글톤 빈을 사용하듯 편리하게 request scope을 이용할 수 있다.

- #### 객체 조회를 필요한 시점까지 지연처리 해야할 때 provider이나 프록시를 이용한다.

- #### 다형성과 DI컨테이너의 장점은 단지 어노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다는 것이다.

- #### 각 메소드들의 실행 시간을 알아볼때 프록시와 AOP를 사용하면 매우 편리하다



# Keyboard Shortcut



- #### soutm은 메소드명으로 sout한다.

- #### soutv는 변수명으로 sout한다.

- #### ctrl + alt + enter은 가르키는 곳 윗 부분에 새로운 줄을 생성한다

- #### ctrl + shift + enter은 가르키는 곳 아래 부분에 새로운 줄을 생성한다.





Image 출처 : [김영한 스프링 핵심-원리-기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)