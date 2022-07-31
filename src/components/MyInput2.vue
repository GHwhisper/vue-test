<template>
  <el-input v-model="value">
    <slot v-for="(_, slotName) in $slots" :name="slotName" :slot="slotName"></slot>
    <template v-for="(_, slotName) in $scopedSlots" #[slotName]="slotProps">
      <slot :name="slotName" v-bind="slotProps"></slot>
    </template>
  </el-input>
</template>

<script>
export default {
  name: 'MyInput2',
  data() {
    return {
      value: '',
    }
  },
  created() {
    console.log('$scopedSlots', this.$scopedSlots)
  }
}
</script>

<style scoped>

</style>
