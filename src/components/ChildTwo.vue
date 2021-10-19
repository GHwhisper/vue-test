<template>
  <div class="child-two-container">
    child-two
    <grandson v-bind="$attrs"></grandson>
  </div>
</template>

<script>
import Grandson from './Grandson'

export default {
  name: 'ChildTwo',
  inheritAttrs: false,
  components: {
    Grandson
  },
  // props: {
  //   hello: String
  // },
  mounted() {
    console.log(this.$attrs)
  }
}
</script>

<style scoped>
.child-two-container {
  font-size: 18px;
  color: #08508e;
}
</style>
