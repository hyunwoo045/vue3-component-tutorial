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

### - slot

부모 컴포넌트에서 자식 컴포넌트를 사용하면서 해당 컴포넌트 사이에 작성한 내용을 자식 컴포넌트에서 사용할 수 있는 방법.

template 태그 안에서 slot 태그를 사용한다.

```html
<!-- App.vue -->
<template>
  <MyBtn>
    <span style="color: red;">Banana</span>
  </MyBtn>
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
<!-- components/MyBtn.vue -->
<template>
  <div class="btn">
    MyBtn
    <slot></slot>
  </div>
</template>
```

## 속성 상속

template 태그의 바로 아래 요소를 `최상위 요소 (루트)` 라고 한다.
그리고 이 최상위 요소는 컴포넌트에 연결된 값들이 <u>상속</u>되게 된다.

```html
<!-- App.vue -->
<template>
  <MyBtn class="hwoo"> Banana </MyBtn>
</template>

<!-- ... -->
```

```html
<!-- components/MyBtn.vue -->
<template>
  <div class="btn">
    <slot></slot>
  </div>
</template>

<!-- ... -->
```

위 코드를 실행하여 element 를 확인해보면 div태그 class 목록에 'btn' 뿐 아니라 'hwoo'도 적용되는 것을 확인할 수 있으며, 속성이 상속되었음을 알 수 있다.

- 주의) 최상위 요소가 여러 개 일 경우 어떤 요소가 상속을 받아야 할 지 모르므로 상속이 되지 않는다.

```html
<!-- components/MyBtn.vue -->
<!-- 상속되지 않음 -->
<template>
  <div class="btn">
    <slot></slot>
  </div>
  <div></div>
</template>

<!-- ... -->
```

### - inheritAttrs

- 컴포넌트가 상속을 받을 것인지에 대한 여부를 지정하는 옵션

```html
<!-- components/MyBtn.vue -->
<template>
  <div class="btn">
    <slot></slot>
  </div>
</template>

<script>
  export default {
    inheritAttrs: false,
  };
</script>
```

- 컴포넌트가 생성될 때 연결된 속성값을 확인하는 법 (`$attrs`)
- class 명인 'hwoo' 가 target 에 명시 되어 있음을 알 수 있다.

```html
<!-- components/MyBtn.vue -->
<!-- ... -->
<script>
  export default {
    created() {
      console.log(this.$attrs);
    },
  };
</script>
```

![result](./markdown/attr.png)

<br/>

- v-bind 를 통해 attrs의 내용 모두를 요소에 적용 시킬 수도 있다. 이 때는 `:` 기호를 사용하지 않도록 한다.

```html
<!-- App.vue -->
<template>
  <MyBtn class="hwoo" style="color: red;">Banana</MyBtn>
</template>

<!-- ... -->
```

```html
<!-- components/MyBtn.vue -->
<template>
  <div class="btn">
    <slot></slot>
  </div>
  <h1 v-bind="$attrs"></h1>
</template>
```

- h1 태그에 class 속성과 style 속성이 모두 연결 되어 있다.
  ![v-bind:attrs](./markdown/inherit.attrs.png)

<br/>
<br/>

## emit

상위 컴포넌트에서 사용되는 메서드를 하위 컴포넌트에 연결하여 사용할 때에 사용된다.

```html
<!-- App.vue -->
<template>
  <MyBtn @click="log"> Banana </MyBtn>
</template>

<script>
  import MyBtn from "~/components/MyBtn";

  export default {
    components: {
      MyBtn,
    },
    methods: {
      log() {
        console.log("Click!!!");
      },
    },
  };
</script>
```

```html
<!-- components/MyBtn.vue -->
<template>
  <div class="btn">
    <slot></slot>
  </div>
  <h1 @click="$emit('click')">ABC</h1>
</template>

<script>
  export default {
    emits: ["click"],
  };
</script>
```

- `App` 컴포넌트에서 명시한 `MyBtn`의 @click:"log" 부분은 `App` 파트에서 실행되는 것이 아니라 `MyBtn`의 emits 로 넘어간 후 h1 @click 부분에서 실행되는 것이므로 `App` 파트에서 굳이 click이라는 속성 이름을 부여할 필요는 없다.
- 이름을 수정할 경우 emits 부분에서 연결하는 이름도 잘 수정해주어야 한다.
- 아래 코드는 위 코드와 같이 동작한다.

```html
<!-- App.vue -->
<template>
  <!-- 수정한 부분 -->
  <MyBtn @hello="log"> Banana </MyBtn>
</template>

<script>
  import MyBtn from "~/components/MyBtn";

  export default {
    components: {
      MyBtn,
    },
    methods: {
      log() {
        console.log("Click!!!");
      },
    },
  };
</script>
```

```html
<!-- components/MyBtn.vue -->
<template>
  <div class="btn">
    <slot></slot>
  </div>
  <!-- 수정한 부분 -->
  <h1 @click="$emit('hello')">ABC</h1>
</template>

<script>
  export default {
    // 수정한 부분
    emits: ["hello"],
  };
</script>
```

### - Emit 으로 event 객체 넘기기

```html
<template>
  <div class="btn">
    <slot></slot>
  </div>
  <h1 @dblclick="$emit('click', $event)">ABC</h1>
</template>

<script>
  export default {
    emits: ["click"],
  };
</script>
```

<br/>

### 상위 컴포넌트에서 변경점 감지해보기

emit 을 이용하여 하위 컴포넌트 data 의 변경점을 상위 컴포넌트가 감지하여 그 내용을 출력하는 코드를 작성해본다.

- 하위 컴포넌트의 template 부분에 input 태그를 하나 작성.
- 양방향 데이터 바인딩 (v-model) 을 이용해 input 태그의 내용이 바뀔때마다 msg 가 갱신되도록 함

```html
<input type="text" v-model="msg" />
```

- msg 가 변경될 때 마다 emit 로 연결받은 메소드들 중 changeMsg 가 실행되도록 하고, 그 인자로 msg 를 넘겨주도록 한다.

```javascript
export default {
  data() {
    return {
      msg: "",
    };
  },
  emits: ["changeMsg"],
  watch: {
    msg() {
      this.$emit("changeMsg", this.msg);
    },
  },
};
```

- 상위 컴포넌트에서 changeMsg 가 연결되도록 컴포넌트 태그에 changeMsg 속성을 선언하고 메서드를 부여한다.
- 태그에서는 camelCase 가 적용되지 않으므로 HTML 이 정확히 인식하도록 kebab-case 로 작성한다.

```html
<template>
  <MyBtn @change-msg="logMsg"> Banana </MyBtn>
</template>

<script>
  export default {
    methods: {
      logMsg(msg) {
        console.log(msg);
      },
    },
  };
</script>
```
