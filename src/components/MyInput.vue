<template>
  <el-input v-model="value">
    <template v-for="(_, slotName) in $slots" #[slotName]>
      <slot :name="slotName"></slot>
    </template>
  </el-input>
</template>

<script>
export default {
  name: 'MyInput',
  data() {
    return {
      value: '',
    }
  },
  created() {
    console.log('$slots', this.$slots)
  }
}
</script>

<style scoped>

</style>
