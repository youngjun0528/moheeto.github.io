---
title: "02 - Spring Security"
description: 
published: true
date: 2021-07-23T07:10:13.631Z
tags: 
- modern-web
- BackEnd
- Spring
- Security
editor: markdown
dateCreated: 2021-07-22T07:22:03.891Z
categories: 
- spring
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "02 - Spring Security"
---

# Web Application Security
## Single Sign-On
프로토콜
- SAML(Security Assertion Markup Lanquage)
- CAS(Cenral Authentication Service)

1. 어플리케이션이 사용자를 아이덴티티 서버(Identity Server)로 리다이렉트
2. 인증을 수행하기 위해 아이덴티티 서버에 응답
3. 인증 결과를 어플리케이션 전달

SAML
서비스 제공자(Service Provider, SP)(어플리케이션)는 SAML asserion(인증 결과)을 인증 제공자(Identity Provider, IdP)(아이덴티티 서버) 로부터 받는다.
인증 결과는 사용자 정보(이름, 이메일 주소, 인증 제공자 측에서 활용하는 사용자 ID)를 포함한다.

CAS
CAS 서버에 인증을 요청하고 성공시 어플리케이션으로 리다이렉트 되면서 서비스 티켓을 벋눈더, 어플리케이션은 받은 티켓이 유효한지 티켓을 이용해 CAS 서버로 요청을 보낸다.

## OAuth 2.0
서드 파티 서비스(third-party service) 가 사용자 데이터에 접근할 때 권한을 부여하기 위해 디자인

- 리소스 소유자 : 리소스 서버에 있는 데이터를 소유한 사람
- 리소스 서버 : 클라이언트가 접근하기를 원하는 데이터를 저장하는 서버
- 권한 부여 서버 : 클라이언트가 요청한 데이터로 접근할 수 있도록 권한 부여를 수행하는 서버
- 클라리언트 : 데이터에 접근하기를 원하는 어플리케이션

![screenshot_20210723-132857~2.png](/assets/img/spring/screenshot_20210723-132857~2.png)

## 권한 부여 방식
- 역할 기반(Role-bsed) : 리소스 종류 단위로 접근을 컨트롤
- 접근 제어 목록(Access Control List, ACL) : 특정 데이터에 대한 사용 및 접근 권한 목록, 작은 단위(fine-grainded)의 섬세한 권한 부여 가능

## 공격 예방
Open Web Application Security Project(OWASP) 에서 정의한 10가지 항목
1. 인젝션(Injection)
2. 취약한 인증(Broken Authentication)
3. 민감한 데이터 노출
4. XML 외부 개체(XML External Entities, XXE)
5. 취약한 접근 통제(Broken Access Control)
6. 잘못되 보안 구성
7. 크로스 사이트 스크립팅(Cross-Site Scripting)
8. 안전하지 않는 역직렬화(Insecure Desserialzation)
8. 알려진 약점이 있는 구성요소 활용
9. 불충분한 로깅 및 모니터링

# Spring Security

![screenshot_20210723-132914~2.png](/assets/img/spring/screenshot_20210723-132914~2.png)

1. HTTP 요청이 서버에 도달하면 그것은 스프링 시큐리티 필터 체인(Spring Security Filter Chain)을 지나간다. 
2. 스프링 시큐리티 필터 체인은 org.springframework.web.filter.DelegatingFilterProxy으로부터 요청을 전달받는다. 
2-1. 스프링 시큐리티 필터 체인은 일반적으로 springSecurityFilterChain이라는 이름을 갖는 스프링 빈으로 생성된다. 
2-2. springSecurityFilterChain은 스프링 시큐리티에 의해 생성되는 필터 빈으로 이뤄진 리스트를 포함한다. 
5. 필터를 통과하면서 스프링은 일련의 작업을 수행하며 요청이 어떻게 처리돼야 하는지 결정한다. 
6. 모든 필터를 통과하고 나면 요청은 요청 매핑을 통해 등록된 컨트롤러에 도달한다. 
7. 대부분 컨트롤러는 명령어를 실행하거나 몇 가지 정보를 가져오려고 서비스의 API를 호출 할 것이다. 
8. 제어의 흐름이 서비스로 가기 전에 스프링 시큐리티는 AOP를 통해서 메소드 단위로 권한 부여를 수행할 수  있다. 

## Spring Security 개념
1. Authentication
전송된 인증 요청 또는 인증된 주체(authenticated principal)를 위한 토큰을 표시.
인증 요청은 일반적으로 프런트엔드가 서버로 보내는 로그인 요청.
인증 요청에서 Authentication 객체는 일반적으로 인증에 사용될 사용자 명이나 비밀번호를 포함.
주체(principal)라는 용어는 Authentication이 상속하는 java.security.Principal 인터페이스.

2. GrarvtedAuthority
사용자에게 부여된 권한.
Authentication의 getAuthorities() 메소드로 사용자 에게 부여된 권한 확인 가능.

3. SecurityContext
로그인된 사용자의 정보인 Authentication는 SecurityContext 인스턴스에 캡술화돼서 HTTP 세션에 저장.
각 요청에 대해 SecurityContext를 관리할 책임이 있는 SecurityContextPersistenceFilter를 가진다.

4. SecurityContextHolder
각 요청을 처리할 때 SecurityContextHolder의 SecurityContextHolder.getContext() 메소드를 통해서 SecurityContext를 가져올 수 있다.
내부적으로 SecurityContextHolder는 SecurityContext를 ThreadLocal 변수에 보관. 
따라서 SecurityContext는 요청을 처리하는 현재 스레드에 제한. 
권한 부여를 수행할 때 스프링 시큐리티는 SecurityContext를 SecurityContextHolder에서 가져온다.


## Spring Security Filter
![screenshot_20210723-132939~2.png](/assets/img/spring/screenshot_20210723-132939~2.png)

#### WebAsyncManagerlntegrationFilter
이 필터는 SecurityContext와 비동기 요청 처리를 위한 핵심 클래스인 스프링 웹의 WebAsyncManager 간의 통합을 제공한다.

#### SecurityContextPersistenceFilter
이 필터는 스프링 시큐리티가 정상적으로 동작하기 위해 필터 체인에 꼭 존재해야 하는 필터 중 하나다.
다이어그램에서 보다시피 요청이 들어올 때 이 필터가 SecurityContextRepository에서 SecurityContext를 로딩해서 설정한다.

SecurityContext가 로드되고 나면 SecurityContextHolder.getContext()로 SecurityContext에 접근이 가능하다.

반면에 여러 요청 간에 발생하는 SecurityContext에 대한 모든 변경 시항이 영구적으로 저장될 수 있도록 요청이 이 필터를 통해 나가면 SecurityContext는 SecurityContextRepository로 다시 업데이트된다.

이 필터는 또한 다른 사용자로부터 온 요청으로의 시큐리티 컨텍스트의 유출을 방지하기 위해 SecurityContextHolder를 지운다.

![screenshot_20210817-164311~2.png](/assets/img/spring/screenshot_20210817-164311~2.png)

SecurityContextPersistenceFilter 필터는 SecurityContextRepository 인터페이스를 구현한 HttpSessionSecurityContextRepository의 인스턴스에 대한 참조를 가진다. 

또한 SecurityContextPersistenceFiIter 필터는 SecurityContext를 구성 및 제거하는 데 SecurityContextHolder를 사용한다. 

SecurityContext 구현체인 SecurityContextlmpl은 Authentication의 인스턴스를 가진다.

인증되지 않은 요청에서 SecurityContextRepository는 SecurityContext 객체를 포함하지 않는다. 

이럴 때 SecurityContextPersistenceFilter 필터가 SecurityCcintextHolder에 넣어주는 SecurityContext는 비어 있을 것이다. 
다음 그림에서 보다시피 SecurityContext 객체에 있는 authentication 또한 널 값이다.

![screenshot_20210817-164344~2.png](/assets/img/spring/screenshot_20210817-164344~2.png)

SecurityContext에 있는 authentication이 로그인한 사용자 정보를 포함하는 Authentication 객체로 업데이트되는 것은 인증이 성공적으로 끝난 다음이다. 

SecurityContextPersistenceFilter는 SecurityContext를 HttpSession에 저장한다. 

이어지는 요청에서 SecurityContextPersistenceFilter는 SecurityContext를 저장소에서 로드해서 SecurityContextHolder에 저장한다.


#### HeaderWriterFilter
이 필터는 현재 요청에 대한 응답에 헤더를 추가한다.

예를 들어, 브라우저를 보호할 수 있도록 X-Frame-Options, X-XSS-Protection, X-Content-Type-options와 같은 헤더를 추가한다.

#### LogoutFilter
이름이 말해주듯이, 이 필터는 인증된 사용자를 로그아웃시킨다.

기본적으로 로그아웃을 처리하는 URL은 /logout이다.

따라서 이 필터는 요청이 해당 경로로 왔을 때만 로그아웃 절차를 실행한다.

로그아웃을 처리하는 URL은 SecurityConfiguration에서 활용할 수 있는 HttpSecurity로 설정이 가능하다.


### UsernamePasswordAuthenticationFilter
UsernamePasswordAuthenticationFilter는 인증이 시작되는 곳이다.

이 필터는 ProviderManager 인스턴스에 대한 참조를 가진다. 

ProviderManager는 AuthervticationManager 인터페이스의 구현체다. 

ProviderManager는 실제 인증을 수행하기 위해 활용되는 AuthenticationProvider의 리스트를 가진다.

가장 널리 사용되는 인증 제공자는 DaoAuthenticaticjnProvider다. 

DaoAuthenticationProvider는 UserDetailsService로 데이터베이스에서 같은 사용자 명을 갖는 UserDetails를 로드한다. 

그 다음 DaoAuthenticationProvider는 요청으로 받은 비밀번호가 UserDetails에 있는 비밀번호와 일치하는지 검사한다. 일치하면 인증은 성공하고 그렇지 않으면 실패한다. 

인증이 성공하면 UsernamePasswordAuthenticationFilter는 SecurityContext를 업데이트한다. 

그리고 사용자를 인증된 것으로 여긴다.

AuthenticationSuccessHandler가 요청에 대한 주도권을 가지며 사용자는 기본 성공 URL로 리다이렉트된다.

인증이 실패하면 SecurityContextHolder는 지워지며 AuthenticationFailureHandler가 요청에 대한 주도권을 가
진다.

기본으로 사용자는 /login?failed 페이지로 리다이렉트된다. 

UsernamePasswordAuthenticationFilter는 어떤 요청에도 인증 프로세스를 시작하지 않는다. 

UsernamePasswordAuthenticationFilter는 HTTP POST 메소드로 /login 경로로 전송된 요청을 위해서만 인증 프로세스를 시작한다.

![screenshot_20210817-164331~2.png](/assets/img/spring/screenshot_20210817-164331~2.png)

UsernamePasswejrdAuthenticationFilter는 AuthenticationManager의 인스턴스에 대한 참조를 갖는 AbstractAuthenticationProcessingFilter를 상속한다. 

AuthenticationManager는 오직 authenticate()라는 하나의 메소드를 갖는 인터페이스다.

이 메소드는 인증되지 않은 Authentication 인스턴스를 입력으로 받는다. 

그리고 인증된 Authentication 인스턴스를 반환한다.


![screenshot_20210817-164403~2.png](/assets/img/spring/screenshot_20210817-164403~2.png)


AuthenticationManager의 구현체인 ProviderManager는 인증을 수행하는 데 사용할 수 있는 AuthenticationProvider의 리스트를 가진다. 

DaoAuthenticationProvider는 AuthenticationProvider 인터페이스의 구현체 중 하나다. 

다음은 내장 AuthenticationProvider다.

- CasAuthenticationProvider
- DaoAuthenticationProvider
- LdapAuthenticationProvider
- OAuth2LoginAuthenticationProvider
- OpenIDAuthenticationProvider
- RememberMeAuthenticationProvider
- AnonymousAuthenticationProvider

DaoAuthenticationProvider는 PasswordEncoder의 인스턴스와 UserDetailsService의 인스턴스를 가진다. 

인증 과정에서 DaoAuthenticati()nProvider는 UserDetailsService에게 UserDetails 인스턴스
를 로드하라고 요청한다. 

그리고 UserDetails가 존재하면 DaoAuthenticationProvider는 Authenticationn 인스턴스를 통해 넘겨받은 비밀번호와 UserDetails 인스턴스의 비밀번호 중 일치하는 것이 있는지 확인하는 데 PasswordEncoder를 활용한다. 

비밀번호가 일치하면 해당 요청을 인증된 것으로 여기며 DaoAuthenticationProvider는 Authentication 인터페이스의 구현체인 UsernamePasswordAuthenticatbnToken 인스턴스를 생성한다.

생성된 UsernamePassw()rdAuthenticationToken은 UsernamePasswordAuthenticationFilter로 반환되고 SecurityContextHolder로 업데이트된다.

하나의 인증 요청이 UsernamePasswordAuthenticationFilter에 도달할 때면 Authentication 객체는 SecurityContext에 존재한다. 

그리고 아래 왼쪽 그림과 비슷하게 구성된다. 

인증이 성공적으로 끝나면 Authentication 객체는 오른쪽 그림과 같이 구성된다. 

SecurityContextPersistenceFilter에서 인증된 Authentication 객체는 HttpSession에 저장된다.

![screenshot_20210817-164411~2.png](/assets/img/spring/screenshot_20210817-164411~2.png)

인증 전 Authentication 인스턴스의 principal, credentials 프로퍼티의 값은 로그인 요청을 통해 전송된 이메일 주소 또는 사용자 명과 비밀번호다. 

authorities는 비어 있고 authenticated는 false다.

인증이 성공하면 Authentication 객체에 있는 principal 값은 UserDetails 인스턴스로 변경된다. 

그리고 credentials는 비밀번호를 보호하기 위해 지워진다

#### RequestCacheAwareFilter
이 필터는 이전에 캐시된 요청을 복원한다.

이전에 캐시된 요창은 대게 요청이 실패할 때 ExceptionTranslationFilter에 의해 저장된다.

예를 들어,하나의 인증된 요청이 보호되는 리소스에 접근하기 위해 전송됐을 때 스프링 시큐리티는 이 요청을 차단한다.

그리고 사용자를 로그인 페이지로 보낸다. 인증이 성공한 후에 사용자는 다시 리다이렉트돼서 보호 리소스로 온다.

스프링 시큐리티는 캐시된 요청을 확인해서 인증 후 사용자가 어디로 가야 하는지 알 수 있다.


#### SecurityContextHolderAwareRequestFilter
이 필터는 다음과 같은 서블릿 API 보안 메소드를 구현한 래퍼(wrapper)를 활용하는 요청에 대한 책임이 있다.

  - HttpServletRequest.authenticate(HttpServletResponse)
  - HttpServletRequest.login(String, String)
  - HttpServletRequest.logout()

#### AnonymousAuthenticationFilter
이 필터는 SecurityContext에 어떤 Authentication도 존재하지 않으면 SecurityContext를 AnonymousAuthenticationToken으로 업데이트한다.

이 필터는 익명 사용자에 대해 실제로 안증을 수행하자는 않는다.

이 필터는 요청이 익명 사용자로부터 온 것임을 확실히 하기 위해 플레이스홀더 인증(placeholder authentication)을 SecurityContext에 저장한다.

#### SessionManagementFilter
이 필터는 세션 고정 보호(session—fixation protection)를 활성화하는 책임과 동시 세션(concurrent sessions)을 컨트롤하는 책임이 있다.

동시 세션은 ConcurrentSessionFilter가 필터 체인에 있어야 한다.

HttpSecurity가 제공하는 다음 설정으로 동시 세션 관리를 활성화할 수 있다.

기본적으로 세션 수에 제한은 없다.

http.sessionManagement().maximumSessions(n)；

#### ExceptionTranslationFilter
필터 체인의 마지막에서 두 번째 필터다. 이름이 말해주듯이, 이 필터는 스프링 시큐리티의 예외를 해석하는 책임이 있다.

AuthenticationException 예외가 잡히면 이 필터는 인증 엔드 포인트를 시작한다.

AccessDeniedException 예외가 잡히고 사용자가 익명일지라도 이 필터는 인증 엔드 포인트를 시작한다.

사용자가 익명이 아니면 AccessDeniedHandler는 기본적으로 HTTP 403 응답을 전송한다.


#### FilterSecurityInterceptor
필터 체인의 마지막 필터는 FilterSecurityInterceptor다.

FilterSecuritylnterceptor는 AccessDecisionManager에 대한 참조를 가진다.

이 필터는 SecurityContext를 가져온다. 

그 다음 허용된 요청인지 결정하는 작업을 AccessDecisionManager에게 위임한다.

그리고 요청이 거부되면 예외를 발생시킨다.

요청이 허용되면 요청은 대응하는 Controller에 도달한다.AccessDecisionManager가 내리는 결정은 투표에 기반한다.
여기서 결정은 요청 단위다.

이것은 스프링 시큐리티가 요청의 경로를 HttpSecurity의 설정과 비교하여 확인하고 SecurityContext에 있는 부여된 권한이 접근을 허용하기에 충분한지 확인한다는 의미다.

![screenshot_20210817-164424~2.png](/assets/img/spring/screenshot_20210817-164424~2.png)

FilterSecuritylnterceptor는 AccessDecisionManager의 인스턴스에 대한 참조를 갖는 AbstractSecurityInterceptor를 상속한다. 

그리고 AffirmativeBased, ConcensusBased, UnanimousBased 세 개의 AccessDecisionManager 구현체가 있다. 

이러한 의사결정 관리자는 AaiessDecisionVoter 리스트를 가진다. 

그리고 AccessDecisionVoter의 구현체로는 RoleVoter, AuthenticatedVoter 두 개가 있다. 

RoleVoter 구현체는 Authentication 인스턴스에 부여된 권한이 요청에 접근하는 데 필요한 역할을 포함하는지 확인
한다. 

역할이 부여됐다면 RoleVoter는 긍정적인 투표를 던지고，그렇지 않으면 부정적인 투표를 던진다.

AuthenticatedVoter는 단순히 SecurityContext에 있는 authentication 인스턴스가 인증됐는지 아닌지를 확인한다. 

그리고 인증됐으면 긍정적인 투표를 던지고, 그렇지 않으면 부정적인 투표를 던진다.

AccessDecisionManager는 자신이 가진 투표자로부터 온 투표를 기반으로 결정을 내린다.

AffirmativeBased는 AccessDecisionVoter로부터 하나라도 긍정적인 응답을 받으면 접근 권한을 부여한다.

그리고 ConsensusBased는 긍정적인 표가 부정적인 표보다 많을 때 접근 권한을 부여한다. 

UnanimousBased는 부정적인 투표가 없을 때만 접근 권한을 부여한다. 

접근 권한이 부여되면 요청은 컨트롤러를 향해간다. 

권한이 없다면 AccessDeniedException 예외가 발생하며 제어의 흐름은 ExceptionTranslationFilter로 넘어간다.


## Method 보안
### 특정 어노테이션으로 메소드 호출시 필요한 권한 및 역할을 명시
1. @Secured 어노테이션
```java
@EnableGlobalMethodSecurity(securedEnabled = true)
public class MethodSecurityConfig {
// …
}
```
2. @RoleAllowed 어노테이션
```java
@EnableGlobalMethodSecurity(jsr250Enabled = true)
public class MethodSecurityConfig {
// …
}
```
3. @Secured 어노테이션
```java
public interface PaymentService {
    @Secured("ROLE_PAYMENT_ADMIN")
    List<Payment> getPayments(Long userid);
}
```
4. @RoleAllowed 어노테이션
```java
public interface PaymentService {
    @RoleAllowed("ROLE_PAYMENT_ADMIN")
    List<Payment> getPayments(Long userid);
}
```
### 스프링 표현언어(Spring Expression Language) 방식
1. 표현언어 메소드 보안 활성화
```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig {
// …
}
```
```java
public interface BoardService {
@PreAuthorize("isBoardMember()")
    List<Card> getCards(long boardld);
}
```
