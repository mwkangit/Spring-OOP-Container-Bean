# Spring-OOP-Container-Bean




## Description

본 프로젝트에서는 Spring Framework를 사용하여 OOP, Spring Container, Spring Bean 구현 및 작동 원리를 다뤄 본다. OOP의 조건인 SOLID 원칙을 지키며 안전하고 편리한 방법으로 로직을 구현 한다. IoC, DI, DL의 기능을 자세하게 접근하여 Singleton, Lifecycle, Scope을 이용하여 클라이언트에게 Bean이 적용되는 방법을 구현해보았다. 필요 시 Annotation과 서비스를 새로 생성해보며 Spring 내부 로직이므로 Junit 5를 통한 통합, 단위테스트를 통해 확인 작업을 거치게 된다.



------



## Environment

<img alt="framework" src ="https://img.shields.io/badge/Framework-SpringBoot-green"/><img alt="framework" src ="https://img.shields.io/badge/Language-java-b07219"/>

Framework: `Spring Boot` 2.5.5

Project: `Gradle`

Packaging: `Jar`

IDE: `Intellij`

Dependencies: `Spring Web`, `Lombok`



------



## Installation



![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black) 

```
./gradlew build
cd build/lib
java -jar hello-spring-0.0.1-SNAPSHOT.jar
```



![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white) 

```
gradlew build
cd build/lib
java -jar hello-spring-0.0.1-SNAPSHOT.jar
```



------



## Core Feature

```java
// MyLogger
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
    private String uuid;
    private String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

    public void log(String message){
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }

    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }

    @PreDestroy
    public void close(){
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }
}
```

```java
// LogDemoController
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
// LogDemoService
@Service
@RequiredArgsConstructor
public class LogDemoService {
	private final MyLogger myLogger;

    public void logic(String id){
        myLogger.log("service id = " + id);
    }
}
```

MyLogger 클래스의 Scope를 request로 하여 요청이 발생했을 때 빈이 생성되도록 구현한 코드이다. Request의 lifecycle은 요청이 발생하고 응답할 때까지 이어지지만 요청이 발생해야 빈이 생성되므로 Controller에서 DI가 이루어지지 않는다. 이때 ObjectProvider, Proxy를 사용으로 스프링 컨테이너에 요청하는 시점을 지연하여 request Scope 로직을 Controller에 적용하는 것 가능하다. 이러한 많은 상황에 적용할 Bean에 대한 Scope, Lifecycle, OOP에 대해 다룬다.



------



## Demonstration Video



- Scope - Prototype

![Spring-OOP-Container-Bean1](/home/mwkang/Downloads/Spring-OOP-Container-Bean1.gif)



- Bean - Lifecycle

![Spring-OOP-Container-Bean2](/home/mwkang/Downloads/Spring-OOP-Container-Bean2.gif)



------



## More Explanation
