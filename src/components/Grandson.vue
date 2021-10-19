<template>
  <div>
    孙级组件
    <input v-bind="$attrs" />
  </div>
</template>

<script>
export default {
  name: 'Grandson',
  inheritAttrs: false,
}
</script>

<style lang="scss" scoped>

</style>
