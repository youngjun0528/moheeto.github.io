# 13. Style

- 컴포넌트에 한정된(scoped) 스타일 적용
- inline과 src 모두 가능
- 독립된 컴포넌트, 관심사의 분리

```HAML
<style scoped>
    .day ul {padding: 10px;}
    .day ul li {padding: 5px;}
</style>
```

```HAML
<style lang='scss' sec='./assets/css/style.css'/>
```