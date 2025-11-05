<template>
  <div class="container">
    <div class="input-locale">
      <input v-model="model" placeholder="yyyy-mm-dd" />
      <button class="locale-btn" @click="switchLocale">{{ currentLocale }}</button>
    </div>

    <div class="calendar">
      <div class="calendar-header">
        <button @click="changeMonth(-1)"><</button>
        <div class="calendar-title">{{ monthName }} {{ year }}</div>
        <button @click="changeMonth(1)">></button>
      </div>

      <div class="calendar-weekdays">
        <div v-for="weekday in localeData.weekdays" :key="weekday" class="weekday">
          {{ weekday }}
        </div>
      </div>

      <div class="calendar-grid">
        <div
          v-for="(day, index) in days"
          :key="index"
          class="calendar-day"
          :class="{ empty: !day, selected: day && isSelected(day.date) }"
          @click="day && select(day.date)"
        >
          {{ day?.date.getDate() }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { locales } from '@/constants/locales'
import { formatDate } from '@/utils/date'
import { ref, computed, watch } from 'vue'

const model = defineModel({ default: null })
const currentLocale = ref('en')
function switchLocale() {
  currentLocale.value = currentLocale.value === 'en' ? 'ru' : 'en'
}

const localeData = computed(() => locales[currentLocale.value])

const selected = ref(model.value ? new Date(model.value) : null)
const month = ref(selected.value.getMonth())
const year = ref(selected.value.getFullYear())

const monthName = computed(() => localeData.value.months[month.value])

const days = computed(() => {
  const totalDays = new Date(year.value, month.value + 1, 0).getDate()
  const firstDay = new Date(year.value, month.value, 1)
  const startOffset = (firstDay.getDay() + 6) % 7
  const arr = Array(startOffset).fill(null)

  for (let day = 1; day <= totalDays; day++) {
    const date = new Date(year.value, month.value, day)
    arr.push({
      date,
      day,
    })
  }

  return arr
})

function changeMonth(dir) {
  const newDate = new Date(year.value, month.value + dir, 1)
  month.value = newDate.getMonth()
  year.value = newDate.getFullYear()
}

function select(date) {
  selected.value = date
  model.value = formatDate(date)
}

function isSelected(date) {
  return date.toDateString() === selected.value.toDateString()
}

watch(model, (val) => {
  if (!val) return

  const parsed = new Date(val)

  if (!isNaN(parsed)) {
    selected.value = parsed
    month.value = parsed.getMonth()
    year.value = parsed.getFullYear()
  }
})
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  gap: 8px;
  height: 100%;
  justify-content: center;
  align-items: center;
}

.input-locale {
  display: flex;
  gap: 8px;
}

.locale-btn {
  cursor: pointer;
}

.calendar {
  width: 280px;
  border: 1px solid #ccc;
  border-radius: 12px;
  padding: 10px;
  font-family: sans-serif;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.calendar-header button {
  border: none;
  background: none;
  cursor: pointer;
  font-size: 20px;
}

.calendar-title {
  font-weight: 600;
}

.calendar-weekdays,
.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}

.weekday {
  font-size: 12px;
  font-weight: 600;
  color: #666;
}

.calendar-day {
  padding: 6px;
  border-radius: 6px;
  cursor: pointer;
}

.calendar-day:hover {
  background-color: #f0f0f0;
}

.selected {
  border: 1px solid #1976d2;
}

.calendar-day.empty:hover {
  background-color: transparent;
  cursor: default;
}
</style>
