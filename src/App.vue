<template>
  <div id="app">
    <h1>父级：{{ parentMessage }}</h1>
    <button @click="handleClick">父级按钮</button>
    <child v-model="parentMessage"></child>
    <hr>
    <child-two class="child-two" hello="hello" type="password"></child-two>
  </div>
</template>

<script>
import Child from './components/Child'
import ChildTwo from './components/ChildTwo'

export default {
  name: 'App',
  components: {
    Child,
    ChildTwo
  },
  data() {
    return {
      parentMessage: 1
    }
  },
  methods: {
    handleClick() {
      this.parentMessage += 1
    }
  }
}
</script>

<style>
.child-two {
  color: lightgreen;
}
</style>
