# 09. Event

## v-on

```JSP
<template>
    <div class="hello">
        <button v-on:click="update(1)">Button</button>
    </div>
</template>

<script>
    export default {
      name: "HelloWorld",
      methods: {
        update(i){
          console.log('test : ' + i)
        }
      }
    };
</script>
```

### Basic

```HAML
<button v-on:click="methodName">클릭하세요.</button>
<button @click="methodName">클릭하세요.</button>
<button @click="methodName(param)">클릭하세요.</button>
```

### Event 수식어

```HAML
<form @submit.prevent='onSubmit'></form>
```

* .stop
* .prevent
* .capture
* .self
* .once

### Event Key 수식어

```HAML
<input @keyup.enter='onSubmit'></input>
```

* .enter
* .tab
* .delete : "DELETE" 키와 "Backsapce" 키
* .esc
* .space
* .up
* .down
* .left
* .right
