<template>
  <div>
    childTree
    <grandson-two v-on="$listeners"></grandson-two>
  </div>
</template>

<script>
import GrandsonTwo from './GrandsonTwo'

export default {
  name: 'ChildThree',
  components: {
    GrandsonTwo
  },
  mounted() {
    console.log('$listeners:', this.$listeners)
    this.$emit('fn1', '子级触发')
    this.$emit('fn2', '子级触发')
  }
}
</script>

<style lang="scss" scoped>

</style>
