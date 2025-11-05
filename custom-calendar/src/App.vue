<template>
  <Calendar v-model="pickedDate" />
</template>
<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'
import { formatDate } from './utils/date'

const pickedDate = ref(formatDate(new Date()))
</script>

<style>
html,
body {
  height: 100%;
  margin: 0;
}

#app {
  height: 100%;
}
</style>
