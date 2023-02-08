---
title: "05 - 도서관 대출 예제"
description: 
published: true
date: 2021-08-02T23:24:58.091Z
tags: 
- database
- modeling
editor: markdown
dateCreated: 2021-07-30T06:02:33.415Z
categories:
- modeling
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "05 - 도서관 대출 예제"
---

## 도서관의 대출관리 예제
- 도서관에는 각 서고/서가에 많은 책들이 있다
- 고객들은 인터넷을 통해서 로그인한 후 도서 목록을 조회할 수 있다
- 고객들은 원하는 책을 대출 받을 수 있다.
- 고객은 책이 있을 경우 대출 예약을 할 수 있으며 대출을 위해서는 직접 방문해서 책을 찾아서 대출을 해야 한다.

1. 명사 추출
	- 도서관, 서고, 서가, 책
	- 고객, 로그인, 도서목록, 조회, 대출, 대출예약
		
2. 테이블(마스터, 관계) 추출
	- 마스터테이블
		- 도서관, 서고, 서가, 책
	- 고객, 도서목록
	- 관계 테이블
		- 로그인, 조회, 대출, 대출예약

## ER Diagram 1
![data-modeling-05-01.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-05-01.jpg)

## ER Diagram 2
![data-modeling-05-02.jpg](..%2F..%2Fassets%2Fimg%2Fdata_modeling%2Fdata-modeling-05-02.jpg)

***
> __참고자료__
>
> [RDMS Modeling 기초](https://www.inflearn.com/course/%EA%B4%80%EA%B3%84%ED%98%95%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-rdbms/dashboard)