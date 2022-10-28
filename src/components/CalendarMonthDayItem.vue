<script setup lang="ts">
import { onMounted, ref, type Ref, type PropType, computed } from 'vue';
import dayjs from 'dayjs';
// import locale from 'dayjs/locale/pl';
type DayObject = {
  date: string;
  isCurrentMonth: boolean;
};
const emits = defineEmits(['setNewDate']);
const props = defineProps({
  selectedDate: {
    type: dayjs.Dayjs,
    default: () => ({}),
  },
  day: {
    type: Object as PropType<DayObject>,
    default: () => ({}),
  },
});
const dayNumber = computed(() => {
  return dayjs(props.day.date).date();
});
</script>

<template>
  <li
    class="CalendarMonthDayItem"
    :class="{
      'calendar-day--not-current': !props.day.isCurrentMonth,
    }"
  >
    <span>{{ dayNumber }}</span>
  </li>
</template>

<style lang="sass">
.CalendarMonthDayItem
  width: 50px
  height: 50px
  color: black
  list-style: none
</style>
