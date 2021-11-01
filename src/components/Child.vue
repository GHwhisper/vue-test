<template>
  <div>
    <h4>子级：{{ msg }}</h4>
    <button @click="handleChildClick">子级按钮</button>
  </div>
</template>

<script>
export default {
  name: 'Child',
  model: {
    prop: 'msg',
    event: 'click'
  },
  props: {
    msg: Number
  },
  methods: {
    handleChildClick() {
      this.$emit('click', this.msg + 1)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
