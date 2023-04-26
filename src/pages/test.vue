<script setup>
const msg = ref('Hello World')
const titleMessage = ref('안녕? 난 타이틀이야')
const count = ref(0)
const isChecked = ref(false)

const handleButtonClick = () => {
  count.value++
}

const changeBoolean = () => {
  isChecked.value = !isChecked.value
}
</script>

<template>
  <div class="flex flex-col items-center">
    <h1 class="text-2xl font-bold">
      TEST
    </h1>
    <p :title="titleMessage">
      {{ msg }}
    </p>
    <p class="mx-auto bg-blue-500 w-50 text-white">
      클릭 횟수 : {{ count }}
    </p>
    <button class="bg-red-500 text-white w-50" @click="handleButtonClick">
      Click Me!
    </button>
    <input v-model="msg" class="text-blue-500 text-2xl">
    <p>조건문 연습</p>
    <span v-if="isChecked">{{ isChecked }}</span>
    <span v-else>집가고싶니</span>
    <button @click="changeBoolean">
      나를 눌러봐라.
    </button>
  </div>
</template>

<style lang="scss" scoped>

</style>
