---
title: "01 - Spring"
description: 
published: true
date: 2021-05-17T02:13:04.735Z
tags: 
- modern-web
- BackEnd
- Spring
editor: markdown
dateCreated: 2021-05-14T00:01:30.145Z
categories: 
- spring
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "01 - Spring"
---

## 스프링 제어의 역전(IoC)과 의존성 주입(DI)
### 빈(Bean)
스프링 컨테이너가 관리하는 객체

### Java 객체 의존성 관리
1. 객체가 직접 의존 관계에 있는 객체들의 생성자를 호출
```java
public class RegService(){
	private MSender mSender;
  public RegService(){
  	// 의존하는 객체를 인스턴스화 한다.
    this.mSender = new MSender();
  }
}
```
2. Look-Up 패턴을 활용해 의존성을 찾아 배치
```java
public class RegService(){
	private MSender mSender;
  public RegService(MSender mSender){
  	this.mSender = mSender;
  }
}

```
위의 RegService에서는 객체의 의존성을 제어하지 못하고, 스프링이 객체의 인스턴스화가 이루어진다.
이를 제어의 역전(Inversion of Control, IoC) 라고 한다.


### 어노테이션 기반의 설정

#### 빈 선언
1. @Component : 제네릭 스테레오 타입, 해당 클래스를 인스턴스화 한다.
2. @Service : 컴포넌트 어노테이션을 특수화 한 것으로 서비스 영역에서 선언한다.
3. @Repository : DAO(Data Sccess Object)를 나타낸다.
4. @Controller : 컴포넌트가 HTTP 요청을 받을 수 있는 Web 컨트롤러 임을 나타낸다.

#### 의존성 주입
반드시 필요한 의존선을 생성자를 통해 주입해야 마며, 최초 주입된 이후 의존성을 ReadOnly 상태이다.
선택적인 의존성을 세터/메소드를 통해 주입할 수 있다. 필드 기반 기반 주입은 지양한다.
1. 생성자 기반의 주입 : 생성자에 @Autowired 적용
```java
@Autowired
public MsgService(MsgRepository repo){
	this.repo = repo
}
```
2. 세터 기반/메소드 기반의 주입 : 메소드 선언 후 @Autowired 또는 @Required 적용
```java
@Required
public void setMsgService(MsgRepository repo){
	this.repo = repo
}
```
3. 핃드 기반의 주입 : 이 방법 사용시 세터 메소드는 별도 선언할 필요가 없다.
```java
@Autowired
private MsgRepository repo
```

## 스프링 MVC
### 자바 EE 서블릿
톰캣과 같은 애플리케이션 서버인 서블릿 컨테이너 내에서 동작
1. HTTP 요청이 도착하면 인증,로깅,감사와 같은 필터링 작업을 수행하는 필터리스트를 통과
	1.1 애플리케이션 서버는 첫번째 요청이 들어오면 HttpSession 인스턴스를 생성
  1.2 각 HttpSession 인스턴스는 Session ID 라는 Id를 가진다.
  1.3 HttpSessionListener / ServeltRequestListener 인터페이스를 구현해 HttpSession의 라이프사이클 이벤트를 수신
2. 특정 패턴과 일치하는 URI를 포함하는 요청을 처리할 수 있게 등록된 서블릿으로 전달
	2.1 Java EE 에서는 모든 HTTP 요청에 대해 HttpServletRequest 인스턴스가 생성
  2.2 서블릿은 @WebServlet 어노테이션을 적용
  2.3 doGet / doPost / doPost / doDelete
3. 서블릿이 요청에 대한 처리를 마치면 HTTP 응답은 동일 필터리스트를 통과한 후 클라이언트로 다시 전송
	3-1. Java EE 에서는 모든 HHTP 응답에 대해 HttpServletResponse 인스턴스가 생성
  3-2. Seesion ID를 HTTP의 응답해더에 쿠키로 전송

### DispatcherServelt
클래스 생성후 @Controller 어노테이션을 추가하고 @RequestMapping 어노테이션으로 특정 URI 패턴에 매핑 가능
```java
import org.springframework.web.bind.annotation.RequestBoby;
@Controller
@RequestMapping("/messages")
public class MessageController{
	@GetMapping("/welcome")
  @ResponseBoby
  public String welcome(){
  	return "welcome"
  }
}
```

### Views
핸들러의 반환값을 View 이름으로 사용하고 응답을 생성.
```java
import org.springframework.web.bind.annotation.RequestBoby;
@Controller
@RequestMapping("/messages")
public class MessageController{
	@GetMapping("/welcome")
  public String welcome(Model model){
  	model.addAttribute("message", "Hello")
  	return "welcome"
  }
}
```

```html
// welcome.html
<strong> th:text="${message}"><strong>
```

### Filter
책임 연쇄 패턴(Chain of Responsibility)
서블릿에 도달하기 전에 HTTP 요청에 대한 필터링 작업 수행
web.xml 파일에 \<filter>와 \<filter-mapping> 추가 / FilterRegistrationBean 를 만들어 설정 클래스에 등록

```java
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
public class AppConfig {
	@Bean
	public FilterRegistrationBean<AuditingFilter>auditingFilterRegistrationBean(){
		FilterRegistrationBean<AuditingFilter> registration = new FilterRegistrationBean<>();
		AuditingFilter filter = new AuditingFilter();
    registration.setFilter(filter);
    registration.setOrder(Integer.MAX_VALUE) ；
    registration.setUrlPatterns(Arrays.asList("/messages/*"));
    return registration;
  }
}
```

## Spring JDBC와 JPA
### JDBC
Java Database Connectivity
관계형 데이터베이스에 저장된 데이터에 접근하는 방법을 정의한다.

### JPA
Java Persistence API
자바객체의 영속성을 위한 자바의 표준화된 접근 방식을 정의한다.
객체 관계형 매핑(ORM, Object-Relational Mapping) : 객체지향 모델과 관계형 데이터베이스의 사이를 정의

### Spring JDBC
스프링에서 제공하는 JDBC API 위의 추상화계층
JdbcTemplate Class

### Example
##### pom.xml
```xml
<dependencies>
  <dependency>
		<groupId>org. springframework.boot</groupId>
		<artifactId>spring-boot-starter-jdbc</artifactId>
  </dependency>
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
  </dependency>
</dependencies>
```

##### application.properties
```json
spring.datasource.url=jdbc:mysql://localhost:3306/app_messages?useSSL=false
spring.datasource.username=<username>
spring.datasource.password=<password>
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

##### Repasitory.java
```java
public class MessageRepository {
	private DataSource dataSource;
	public MessageRepository(DataSource dataSource) {
		this.dataSource = dataSource;
	}
  
	public Message saveMessage(Message message) {
		Connection c = DataSourceUtils.getConnection(dataSource);
		try {
			String insertSql = "INSERT INTO messages ('id','text','created_date') VALUE (null, ? ,
			?)";
      PreparedStatement ps = c.prepareStatement( insertSql, Statement. RETURN_GENERATED_KEYS);
			//SQL에 필요한 매개변수를 준비한다
	 		ps.setString(1, message.getText());
	 		ps.setTimestamp(2, new Timestamp(message.getCreatedDate().getTime()));
			int rowsAffected = ps.executeUpdate();
      if (rowsAffected > 0) {
				 // 새로 저장된 메시지 id 가져오기
					ResultSet result = ps.getGeneratedKeys();
					if(result.next()) {
						int id = result.getlnt(1);
						return new Message(id, message.getText(),message.getCreatedDate());
					} else {
						logger.error( "Failed to retrieve id . No row in result set");
						return null;
					}
			} else {
				// Insert 실패
				return null;
			}
		} catch (SQLException ex) {
			logger.error("Failed to save message", ex);
			try {
				c.close();
			} catch (SQLException e) {
				logger.error("Failed to close connection", e);
			}
		} finally {
		 	DataSourceUtils.releaseConnection(c, dataSource);
		}
		return null;
}
```


## Spring AOP 
### Concerns(관심사)
보안에 관련된 관심사 또는 애플리케이션이 충족해야 하는 목표

### Aspects(애스팩트)
위의 Conserns 를 모듈화하는 것, @Aspect 어노테이션

### Join Points(조인포인트)
특정 프로그램이 실행되는 지전
항상 메소드 호출을 나타낸다.

### Advise(어드바이스)
특정 관심사를 처리하는 행동

- Before advise(이전) : 조인 포인트 이전에 실행되는 어드바이스, 예외를 던지지 않는 한 조인 포인트에 도달하는 코드실행을 막을 수 없다. (@Before)
- After returning advise(정상적 반환 이후) : 예외를 던지지 않고 조인 포인트가 정상적으로 완료도니 후 실행되는 어드바이스 (@AfterReturning)
- After throwing advise(예외 발생 이후) : 예외를 던져 메소드가 종료될때 실행 되는 어드바이스 (@AfterThrowing)
- After Advise(이후) : 조인 포인트 실행과 관계없이 실행되는 어드바이스 (@After)
- Around advise(메소드 실행 전후) : 조인 포인트를 둘러싼 어드바이스, 코드실행을 완전히 제어 (@Around)

### PointCut(포인트컷)
일치하는 여러 조인 포인트를 결합

### AOP Proxy
표준 JDK 동적 프락시로 AOP 프작시를 생성한다. JDK 동적 프락시는 인터페이스를 기반으로 프락시 객체를 생성한다. 대상 클래스가 인터페이스를 구현하지 않으면 스프링 AOP는 CGLIB로 대상 클래스를 하위 클래스로 만들어 프락시를 생성한다.

### Weaving(위빙)
다른 필수 객체와 에스팩트를 연결해 어드바이스 객체를 생성하는 프로세스.
스프링 AOP 에서 위빙은 런타임 동안에 발생 / AspectJ는 위빙을 컴파일 타임 또는 로드 타임 동안에 발생한다.


## 스프링 트랜잭션 관리
전역 크랜잭션으로 동작하는 JTA(Java Transaction API), JDBC, Hibernate, JPA 등 다양한 트랜잭션 API의 추상화를 제공한다.
- 프로그래밍적 트랜잭션 관리
TransactionTemplate API로 관리
PlatformTransactionManager API로 직접 관리

- 선언적 드랜잭션 관리
@Transcational 어노테이션으로 클래스 혹은 메소드에 적용. 스프링 AOP 프래임워크 기반

