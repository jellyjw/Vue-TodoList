<script setup>
import { onMounted } from 'vue'

const props = defineProps({
  item: {
    type: Object,
    required: false,
  },
})

console.log(props.item)
</script>

<template>
  <div class="flex items-center">
    <el-checkbox :label="todo" />
    <div i-carbon-edit />
    <div i-carbon-delete />
  </div>
</template>

<style lang="scss" scoped>

</style>
