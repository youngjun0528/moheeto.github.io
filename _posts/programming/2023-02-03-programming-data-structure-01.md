---
title: "01 - 배열과 문자열"
description: 
published: true
date: 2021-07-01T07:05:34.529Z
tags: 
- Programming
- Data Structure
editor: markdown
dateCreated: 2021-04-11T23:27:17.470Z
categories: 
- data_structure
author_profile: true
sidebar_main: true
toc: true
toc_sticky: true
toc_label: "01 - 배열과 문자열"
---

# 01. 배열과 문제열 해법

## 해시테이블
탐색을 위한 자료구조, Key를 Value에 대응
- Insert
1. 키의 해시코드를 계산한다. int 형이나 long형
2. 해시코드를 이용해서 배열의 인덱스를 구한다.
3. 각 인덱스에는 Key, Value로 이루어진 연결리스트가 있다.

- Search
1. 주이진 키의 해시코드를 계산한다.
2. 해시코드로 배열의 인덱스를 구한다.
3. 해당 배열의 인덱스의 연결리스트를 탐색한다.


## ArrayList
Python 같은 경우 배열의 크기를 자동으로 조절하는 배열
Java 같은 경우 배열의 크기는 고정되어 있다.

### 1.1 문자 중복 확인

문자 집합에서 특정 문자가 2번 이상 들어간다면 중복된 문자가 존재한다.
문자 길이가 문자 집합 길이보다 길다면 중복된 문자가 존재한다.

```python
def isUniqueChars(str):
    if len(str) > 128:
        return False
    char_set = [False] * 128
    for i in range(len(str)):
        val = ord(str[i])
        if char_set[val]:
            return False
        char_set[val] = True
    return True
```

### 1.2 순열확인

2개의 문자열에서 문자열이 서로 일치한지 확인한다.

```python
def checkSort(str):
    check = sorted(str)
    return check

def permutations(s, t):
    if len(s) != len(t):
        return False
    return checkSort(s) == checkSort(t)
```

### 1.3 URLify

문자열의 모든 공백을 %20으로 바꾸는 메서드를 작성한다.

1. replace 함수 사용


2. 배열의 크기를 늘린 다음 문자열 삽입