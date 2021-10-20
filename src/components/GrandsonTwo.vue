<template>
  <div>孙级组件</div>
</template>

<script>
export default {
  name: 'GrandsonTwo',
  mounted() {
    setTimeout(() => {
      this.$emit('fn1', '孙级触发')
    }, 1000)
    setTimeout(() => {
      this.$emit('click')
    }, 2000)
  }
}
</script>

<style lang="scss" scoped>

</style>
