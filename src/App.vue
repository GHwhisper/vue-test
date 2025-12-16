<template>
  <div id="app">
    <CollapseItem title="面板1">
      <div>
        <p>12313123</p>
        <p>12313123</p>
        <p>12313123</p>
        <p>12313123</p>
        <p>12313123</p>
      </div>
    </CollapseItem>
    <CollapseItem title="面板2">
      <div>
        <p>qwerqwer</p>
        <p>qwerqwer</p>
        <p>qwerqwer</p>
        <p>qwerqwer</p>
        <p>qwerqwer</p>
      </div>
    </CollapseItem>

    <!--<hr>-->

    <!--<section>-->
    <!--  <h3>1、v-model绑定</h3>-->
    <!--  <h4>父级：{{ parentMessage }}</h4>-->
    <!--  <button @click="handleClick">父级按钮</button>-->
    <!--  <child v-model="parentMessage"></child>-->
    <!--</section>-->

    <!--<hr>-->

    <!--<section>-->
    <!--  <h3>2、$attrs的使用</h3>-->
    <!--  <child-two class="child-two" hello="hello" type="password"></child-two>-->
    <!--</section>-->

    <!--<hr>-->

    <!--<section>-->
    <!--  <h3>3、$listeners的使用</h3>-->
    <!--  <child-three-->
    <!--    v-on:fn1="func1"-->
    <!--    @fn2="func2"-->
    <!--    v-on:fn3.native="func3"-->
    <!--    @fn4.native="func4"-->
    <!--    @click="func5"-->
    <!--    @click.native="func6"-->
    <!--  ></child-three>-->
    <!--</section>-->

    <!--<hr>-->

    <!--<section>-->
    <!--  <h3>4、$slots 非作用域插槽的使用</h3>-->
    <!--  <my-input>-->
    <!--    <template slot="prepend">prepend</template>-->
    <!--    <template slot="append">append</template>-->
    <!--  </my-input>-->
    <!--</section>-->

    <!--<hr>-->

    <!--<section>-->
    <!--  <h3>5、$scopedSlots 作用域插槽的使用</h3>-->
    <!--  &lt;!&ndash;<my-input2>&ndash;&gt;-->
    <!--  &lt;!&ndash;  <template slot="prepend">prepend</template>&ndash;&gt;-->
    <!--  &lt;!&ndash;  <template slot="append">append</template>&ndash;&gt;-->
    <!--  &lt;!&ndash;</my-input2>&ndash;&gt;-->
    <!--</section>-->

    <!--<hr>-->

    <!--<section>-->
    <!--  <h3>6、:root 和 css 变量的使用</h3>-->
    <!--  <p class="test-class">hello world</p>-->
    <!--</section>-->
  </div>
</template>

<script>
import CollapseItem from './components/CollapseItem/index.vue'
// import Child from './components/Child'
// import ChildTwo from './components/ChildTwo'
// import ChildThree from './components/ChildThree'
// import MyInput from './components/MyInput'
// import MyInput2 from './components/MyInput2'

export default {
  name: 'App',
  components: {
    CollapseItem,
    // Child,
    // ChildTwo,
    // ChildThree,
    // MyInput,
    // MyInput2,
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
    func5(val) {
      console.log('click func5' + ' ' + val)
    },
    func6() {
      console.log('click func6 原生事件')
    }
  }
}
</script>

<style>
.child-two {
  color: lightgreen;
}

.test-class {
  color: var(--test-color);
  font-size: var(--medium-font);
  font-weight: var(--bold-font);
}
</style>
