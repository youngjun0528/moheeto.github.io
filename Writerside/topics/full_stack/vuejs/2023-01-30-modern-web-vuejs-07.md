# 07. Binding

## Class, Style Binding

### v-bind:class

```HAML
<div :class="{active: isActive}">활성화</div>
```

* result

```HAML
# IF isActive False
<div class="">활성화</div>
# IF isActive True
<div class="active">활성화</div>
```

### v-bind:style

```HAML
<div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}"></div>
```

* result

```HAML
# IF activeColor is red and fontSize is 16
<div style="color:red;font-size:16px"></div>
```

```JSP
<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
        <div :class="{active: isActive}">활성화</div>
        <div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}"></div>
    </div>
</template>

<script>
    export default {
      name: "HelloWorld",
      data() {
        return {
          msg: "반갑습니다!",
          isActive: true,
          activeColor: 'red',
          fontSize: 16
        };
      },
    };
</script>
```
