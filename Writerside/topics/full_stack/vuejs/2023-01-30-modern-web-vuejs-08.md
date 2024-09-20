# 08. Loop

## v-for

```JSP
<template>
    <div class="hello">
        <ul>
            <li v-for="(item, index) in items" v-bind:key="item.name">
                {{index}}:{{item.name}}, {{item.age}}
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
      name: "HelloWorld",
      data() {
        return {
          items:[
            {name:'red', age: 15},
            {name:'blue', age: 29},
            {name:'green', age: 32}
          ]
        };
      },
    };
</script>
```
