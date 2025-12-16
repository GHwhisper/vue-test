<template>
  <div>
    <h4>子级：{{ msg }}</h4>
    <button @click="handleChildClick">子级按钮</button>
    <el-dialog :visible="false" title="弹窗" v-dialog-drag>
      hello world
    </el-dialog>
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
