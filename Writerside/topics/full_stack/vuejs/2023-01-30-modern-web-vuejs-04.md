# 04. 컴포넌트 라이프사이클

- 컴포넌트 기반 프레임워크의 핵심
- 컴포넌트 입장에서 생성(화면에 등장) 부터 소멸(화면에서 사라짐)까지의 큰 흐름으로 이해

## Creation 생성

### beforeCreate

- data와 event가 셋팅되지 않아 data에 접근 불가

### created

- data, event가 셋팅되어 접근 가능
- api에서 데이터를 받아 data에 전달하는 등의 초반 동작 수행

## Mounting 마운트

### beforeMount

- 첫 렌더링이 일어나기 직전

### mounted

- 랜더링 후 컴포넌트, 템플릿에 접근 가능

## Updating 업데이트

### beforeUpdate

- 데이터가 변경되어 다시 랜더링될 때

### updated

- 데이터가 변경되어 랜더링 된 후

## Destruction 소멸,해체

## beforeDestroy

- 소멸되지 전에 호출

## destroyed

- 소멸된 후 호출

# 컴포넌트 추가 및 import 하기

1. /src/components 폴더에서 컴포넌틑 파일을 추가한다.
2. 컴포넌트가 필요한 곳에서 import 한다.
3. 스크립트의 components 오브젝트에 등록한다.(camelCase)
4. 템플릿에 <components></components> 와 같은 html 문법으로 사용한다.(kebab-case)

```JSP
<template>
    <div>
        <nav></nav>
        <pagination></pagination>
    </div>
</template>

<script>
    import Nav from '@components/Nav'
    import Pagination from '@components/Pagination'
    export default {
      components: {
        Nav,
        Pagination
      }
    };
</script>
```

# props

- 부모로부터 컴포넌트에 전달되는 데이터

## 데이터 줄때

```HAML
<child :msg="msg" :author="author"></child>
```

## 데이터 받을때

```JSP
props: {
msg: String,
author: String
}
```