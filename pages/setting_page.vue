<template>
  <div class="container">
    <div class="content">
      <h2>{{ data }}</h2>
      <h3>Sum : {{ sum }}</h3>
      <h3>Average : {{ (sum / data.length).toFixed() }}</h3>
      <h3>Mode : {{ mode }}</h3>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// original data, sorted from smallest to biggest
const data = ref([5, 6, 3, 9, 3, 7, 8].sort((a, b) => a - b))

// sum
const sum = ref(0)
for (let i = 0; i < data.value.length; i++) {
  sum.value += data.value[i]
}

// mode (find duplicates)
const freqMap: Record<number, number> = {}
for (const num of data.value) {
  freqMap[num] = (freqMap[num] || 0) + 1
}
const mode = Object.keys(freqMap)
  .map(Number)
  .filter((num) => freqMap[num] > 1)
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.content {
  text-align: center;
  font-family: sans-serif;
  font-size: 1.2rem;
}
</style>
