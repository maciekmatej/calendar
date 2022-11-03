<script setup lang="ts">
import { onMounted, ref, type Ref, type PropType, computed } from 'vue';
import dayjs from 'dayjs';
// import locale from 'dayjs/locale/pl';
type DayObject = {
  date: string;
  isCurrentMonth: boolean;
};
type EventObject = {
  name: string;
  start: string;
  end: string;
  color: string;
};
const emits = defineEmits(['setNewDate', 'openDayCalendar']);
const props = defineProps({
  selectedDate: {
    type: dayjs.Dayjs,
    default: () => ({}),
  },
  day: {
    type: Object as PropType<DayObject>,
    default: () => ({}),
  },
  isToday: {
    type: Boolean,
    default: false,
  },
  data: {
    type: Array as PropType<EventObject[]>,
    default: () => [],
  },
});
const dayNumber = computed(() => {
  return dayjs(props.day.date).date();
});
const goToClickedMonth = (): void => {};
const openSelectedDayCalendar = (): void => {
  emits('openDayCalendar', props.day.date);
};
</script>

<template>
  <li
    class="CalendarMonthDayItem"
    :class="{
      'CalendarMonthDayItem--not-current': !props.day.isCurrentMonth,
    }"
    @click="goToClickedMonth"
  >
    <span
      class="CalendarMonthDayItem__day"
      :class="{
        'CalendarMonthDayItem__day--today': isToday,
      }"
      @click="openSelectedDayCalendar"
      >{{ dayNumber }}</span
    >
    <div
      v-for="(item, index) in data"
      :key="index"
      class="CalendarMonthDayItem__event"
      :style="{ backgroundColor: item.color }"
    >
      {{ item.start }}
    </div>
  </li>
</template>

<style lang="sass">
.CalendarMonthDayItem
  height: 150px
  color: black
  list-style: none
  display: flex
  flex-direction: column
  align-items: center
  &--not-current
    background-color: lightgrey

  &__day
    border-radius: 50%
    width: 24px
    height: 24px
    display: flex
    justify-content: center
    align-items: center
    cursor: pointer

    &:hover
      background-color: grey
    &--today
      background-color: orange
      border-radius: 50%
      width: 24px
      height: 24px
      display: flex
      justify-content: center
      align-items: center


  &:hover
    box-shadow: inset 0 0 5px grey

  &__event
    padding:0 10px
    border: 1px solid black
    margin: 1px
</style>
