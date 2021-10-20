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
    console.log(this.$listeners)
    this.$emit('fn2', '子级触发')
  }
}
</script>

<style lang="scss" scoped>

</style>
