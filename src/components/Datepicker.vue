<script setup>
  import { ref, computed, onMounted, onBeforeUnmount, watch } from 'vue'
  import MONTHES from '@/constants/MONTHES'
  import WEEKDAYS from '@/constants/WEEKDAYS'

  const props = defineProps({
    lang: {
      type: String,
      default: 'ru',
      validator: val => ['ru', 'en'].includes(val)
    }
  })

  const model = defineModel()

  const currentDate = ref(model.value ? new Date(model.value) : new Date())

  const currentMonth = computed(() => currentDate.value?.getMonth())
  const currentMonthName = computed(() => MONTHES.map(item => item[props.lang])[currentMonth.value])
  const currentYear = computed(() => currentDate.value?.getFullYear())

  const weekDays = computed(() => WEEKDAYS.map(item => item[props.lang]))

  const daysInMonth = computed(() => {
    return new Date(currentYear.value, currentMonth.value + 1, 0).getDate()
  })

  const firstDayOfMonth = computed(() => {
    const first = new Date(currentYear.value, currentMonth.value, 1).getDay()
    return first === 0 ? 6 : first - 1 // сдвигаем, т.к. по умолчанию 0 - вс
  })

  const formatDateStr = date => {
    const y = date.getFullYear()
    const m = String(date.getMonth() + 1).padStart(2, '0')
    const d = String(date.getDate()).padStart(2, '0')
    return `${y}-${m}-${d}`
  }

  const setSelectedDayOnOtherMonth = (year, month, day) => {
    const date = new Date(year, month, day)
    if (date.getDate() !== day) {
      return new Date(year, month + 1, 0)
    }
    return date
  }

  const isSelected = day => {
    const candidate = new Date(currentYear.value, currentMonth.value, day)
    return model.value === formatDateStr(candidate)
  }

  const selectDay = day => {
    const newDate = new Date(currentYear.value, currentMonth.value, day)
    model.value = formatDateStr(newDate)
    currentDate.value = newDate
  }

  const prevMonth = () => {
    const dayForSelect = model.value
      ? new Date(model.value).getDate()
      : new Date().getDate()
    const newDate = setSelectedDayOnOtherMonth(currentYear.value, currentMonth.value - 1, dayForSelect)
    currentDate.value = newDate
    model.value = formatDateStr(newDate)
  }

  const nextMonth = () => {
    const dayForSelect = model.value
      ? new Date(model.value).getDate()
      : new Date().getDate()
    const newDate = setSelectedDayOnOtherMonth(currentYear.value, currentMonth.value + 1, dayForSelect)
    currentDate.value = newDate
    model.value = formatDateStr(newDate)
  }

  let stopModelWatcher = null

  onMounted(() => {
    stopModelWatcher = watch(model, newVal => {
      const regex = /^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])$/
      if (newVal && regex.test(newVal)) {
        currentDate.value = new Date(newVal)
      }
    })
  })

  onBeforeUnmount(() => {
    if (typeof stopModelWatcher === 'function') {
      stopModelWatcher()
    }
  })

</script>

<template>
  <div class="datepicker__container">
    <div class="datepicker__header">
      <div class="datepicker__header-btn" @click="prevMonth"><</div>
      <div>{{ currentMonthName }} {{ currentYear }}</div>
      <div class="datepicker__header-btn" @click="nextMonth">></div>
    </div>

    <div class="datepicker__weekdays">
      <div
        v-for="day in weekDays"
        :key="day"
        class="datepicker__weekday"
      >
        {{ day }}
      </div>
    </div>

    <div class="datepicker__days-grid">
      <div
        v-for="i in firstDayOfMonth"
        :key="`empty-${i}`"
        class="datepicker__empty-day"
      >
      </div>

      <div
        v-for="day in daysInMonth"
        :key="day"
        :class="[
          'datepicker__day',
          { 'datepicker__day_selected': isSelected(day) }
        ]"
        @click="selectDay(day)"
      >
        {{ day }}
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.datepicker {
  &__container {
    width: 350px;
    border: 1px solid $borders-color;
    border-radius: $controls-border-radius;
    overflow: hidden;
    font-size: 14px;
    font-weight: 400;
  }

  &__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    background-color: $datepicker-head-bg;
    font-size: 16px;
    font-weight: 600;
  }

  &__header-btn {
    background: none;
    border: 1px solid #ccc;
    color: #ccc;
    border-radius: 4px;
    width: 45px;
    height: 35px;
    cursor: pointer;
    transition: $transition;
    display: flex;
    align-items: center;
    justify-content: center;

    &:hover {
      color: $primary;
      border-color: $primary;
    }
  }

  &__weekdays {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    text-align: center;
    font-weight: 600;
    background-color: $datepicker-weekdays-bg;
    padding: 8px 0;
  }

  &__weekday {
    padding: 4px;
  }

  &__days-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 2px;
    padding: 8px;
  }

  &__day {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 36px;
    cursor: pointer;
    border-radius: 4px;
    transition: $transition;

    &:not(&_selected):hover {
      background-color: $datepicker-day-hover-bg;
    }

    &_selected {
      background-color: $primary;
      color: #fff;
    }
  }

  &__empty-day {
    visibility: hidden;
  }

}
</style>
