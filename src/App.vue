<template>
  <div id="app">
    <h1>父级：{{ parentMessage }}</h1>
    <button @click="handleClick">父级按钮</button>
    <child v-model="parentMessage"></child>
  </div>
</template>

<script>
import Child from './components/Child'

export default {
  name: 'App',
  components: {
    Child
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

</style>
