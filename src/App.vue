<template>
  <MyBtn text='Banana' />
  <MyBtn color="royalblue" text='Cherry'/>
  <MyBtn large color='orange' text='Orange'/>
</template>

<script>
import MyBtn from '~/components/MyBtn'

export default {
  components: {
    MyBtn
  }
}
</script>