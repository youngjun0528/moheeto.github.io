---
title: "Clean Code"
description: 
published: true
date: 2023-05-03T07:07:00.000Z
tags: 
- Programming
- CleanCode
editor: markdown
dateCreated: 2023-05-03T07:07:00.000Z
categories: 
- java
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "Clean Code"
---

# 2장. 의미 있는 이름
## 의도를 분명히 밝혀라
- 의도가 분명하게 이름을 지어라
## 그릇된 정보를 피하라
- 널리 쓰이는 의미가 있는 단어는 변수 이름으로 적합하지 않다.
- 유사한 개념은 유사한 표기법으로 사용한다. 
> 이미 대부분의 IDE 에서는 예약어 혹은 며확하지 않은 단어에 대해서는 알려준다.
## 의미 있게 구분하라
- 연속된 숫자를 붙이거나 불용어(noise word) 사용을 피하라
- 읽는 사람이 차이를 알 수 있도록 구분하여 이름을 지어라
## 발음하기 쉬운 이름을 사용하라
## 검색하기 쉬운 이름을 사용하라
## 인코딩을 피하라
- 아래 제시된 방법은 OLD 한 방식
  - 헝가리식 표기법
  - 멤버변수 접두어
  - 인터페이스 클래스와 구현 클래스 구분
    > - 이건 구분 해야 한다고 생각
    > - 유자보수의 입장에서 인터페이스와 구현 클래스를 구분하는 것은 중요하다.
## 자신의 기억력을 자랑하지 마라
- 자신만이 아는 단어로 변수 이름을 작성하지 마라
## 클래스 이름
- 클래스 이름과 객체 이름은 명사, 명사구
## 메서드 이름
- 동사나 동사구
- 접근자, 변경자, 조건자의 경우 javabean 표준에 따라 get, set, is 를 붙인다.
- 생성자를 중복 정의할 때는 정적 팩토리 메서드를 사용한다.
## 기발한 이름을 피하라
## 한 개념에 한 단어를 사용하라
- 실행 관련 코드에 controller, manager, driver 를 혼용해서 쓰지 마라
## 말장난을 하지 마라
- 객체를 추가한다고 해서 모든 이름을 add 라고 붙일 필요는 없다.
- 상황에 따라 append, insert 도 고려해라
## 해법 영역에서 가져온 이름을 사용해라
- 일반적인 프로그래밍 언어로 기술한다.
## 뮨제 영역에서 가져온 이름을 사용하라
- 고객이 사용하는 언어로 기술한다. 
## 의미 있는 맥락을 추가하라
## 불필요한 맥락을 제거하라

# 3장. 함수
## 작게 만들어라
## 하나의 기능만 수행하라
## 함수 당 추상화 수준은 하나로 유지하라
- 위에서 아래로 내려가도록 함수를 구성한다.
- switch 문
  - ```java
    public Money calculatePay(Employee e) throws InvalidEmployeeType {
      switch (e.type) {
        case COMMISSIONED:
          return calculateCommissionedPay(e);
        case HOURLY:
          return calculateHourlyPay(e);
        case SALARIED:
          return calculateSalariedPay(e);
        default:
          throw new InvalidEmployeeType(e.type);
      }
    }
    ```
    - 문제점
      - 함수가 길다
      - 한가지 작업만 수행하지 않는다.
      - SRP(Single Responsibility Principle)를 위반
      - OCP(Open Closed Principle) 위반
      - 정리하면, 
        - 새로운 직원의 타입이 생성이 되면 새로운 직원 타입에 대한 급여 계산 함수가 추가되고, 추가된 함수를 Switch 문에 추가하여야 한다.
        - 만약, 유사한 급여 계산 방식이면 동일한 함수가 반복적으로 구현되어야 한다.
        - 즉, 직원 타입을 분리하는 Switch 문과 직원 타입에 대한 계산 함수가 동일한 레벨에 존재하면 유지보수의 어려움이 존재한다. 
        - 아래와 같은 방식으로 전환하면, 새로운 직원 타입에 대한 Class 객체는 별도로 생성하고, 급여 계산 방식도 Class 함수로 존재한다.
        - Switch 문에서는 가볍게 직원 타입에 따른 Class 객체를 반환하는 형태로 구현한다.
  - ```java
    public abstract class Employee {
        public abstract boolean isPayday();
        public abstract Money calculatePay();
        public abstract void deliverPay(Money pay);
    }
    public interface EmployeeFactory {
        public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
    }
    public class EmployeeFactoryImpl implements EmployeeFactory {
        public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
          switch (r.type) {
            case COMMISSIONED:
              return new CommissionedEmployee(r);
            case HOURLY:
              return new HourlyEmployee(r);
            case SALARIED:
              return new SalariedEmployee(r);
            default:
              throw new InvalidEmployeeType(e.type);
          }
        }
    } 
    ```