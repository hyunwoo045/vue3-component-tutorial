# Component 기초

## props

부모 - 자식 간의 데이터 통신을 구현화 하기 위한 방법

```html
// App.vue
<template>
  <MyBtn />
  <MyBtn color="royalblue" />
</template>

<script>
  import MyBtn from "~/components/MyBtn";

  export default {
    components: {
      MyBtn,
    },
  };
</script>
```

```html
// components/MyBtn.vue
<template>
  <div class="btn" :style="{ backgroundColor: color }">Apple</div>
</template>

<script>
  export default {
    props: {
      color: {
        type: String,
        default: "gray",
      },
    },
  };
</script>
```
