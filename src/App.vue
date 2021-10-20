<template>
  <div id="app">
    <h1>父级：{{ parentMessage }}</h1>
    <button @click="handleClick">父级按钮</button>
    <child v-model="parentMessage"></child>
    <hr>
    <child-two class="child-two" hello="hello" type="password"></child-two>
    <hr>
    <child-three v-on:fn1="func1" @fn2="func2" v-on:fn3.native="func3" @fn4.native="func4" @click="func5"></child-three>
  </div>
</template>

<script>
import Child from './components/Child'
import ChildTwo from './components/ChildTwo'
import ChildThree from './components/ChildThree'

export default {
  name: 'App',
  components: {
    Child,
    ChildTwo,
    ChildThree,
  },
  data() {
    return {
      parentMessage: 1
    }
  },
  methods: {
    handleClick() {
      this.parentMessage += 1
    },
    func1(val) {
      console.log('v-on绑定的fn1事件' + ' ' + val)
    },
    func2(val) {
      console.log('@写法绑定的fn2事件' + ' ' + val)
    },
    func3() {
      console.log('v-on绑定的fn3事件')
    },
    func4() {
      console.log('@写法绑定的fn4事件')
    },
    func5() {
      console.log('click')
    }
  }
}
</script>

<style>
.child-two {
  color: lightgreen;
}
</style>
