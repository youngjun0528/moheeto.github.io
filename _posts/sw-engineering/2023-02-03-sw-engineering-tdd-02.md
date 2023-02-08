---
title: "02 - 테스트프레임워크"
description: 
published: true
date: 2022-03-07T06:12:00.625Z
tags: 
- software-engineering
- test
- TDD
editor: markdown
dateCreated: 2022-03-07T06:11:49.622Z
categories: 
- tdd
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "02 - 테스트프레임워크"
---

# 테스트프레임워크

### Test Method
1. 테스트메서드 호출하기
2. 먼저 setUp 호출
3. 나중에 tearDown 호출
4. 테스트 메서드가 실패하더라도 tearDown 호출
5. 여러 개의 테스트 실행
6. 수집된 결과 출력
7. 로그 문자열 남기기

#### TestMethod
```python
discovered_tests = TestLoader().discover(path, 'test_*.py')
TextTestRunner(verbosity=verbosity).run(discovered_tests)
```

#### setUp
##### 3A
1. 준비(Arrange) : 객체를 생성한다.
2. 행동(Act) : 어떤 행동을 한다.
3. 확인(Assert) : 결과를 검사한다.

##### 테스트 제약 조건
- 성능 : 
-- 빠르게 테스트는 수행되어야 한다.
-- 여러 테스트에서 같은 객체를 사용한다면, 객체 하나만 생성해서 모든 테스트가 같은 객체를 사용하게 한다.
- 격리
-- 한 테스트에서의 성공이나 실패가 다른 테스트에 영향을 주지 않기를 원한다.

: 즉, 테스트 커플링을 만들지 말 것. 일단 테스트를 작성함에 있어서 간결함이 성능 향상보다 더 중요하다.
: 동일한 객체를 공유하게 되면, 테스트 사이의 호출 순서나 객체 상태에 따라 테스트 결과가 달라진다.

##### TestSuite
- 여러 테스트를 모아서 한꺼번에 실행한다.

