<script setup lang="ts">
import { onMounted, ref, type Ref, type PropType, computed } from 'vue';
import EventItem from './EventItem.vue';
import dayjs from 'dayjs';
import isBetween from 'dayjs/plugin/isBetween';
dayjs.extend(isBetween);

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
    type: String,
    default: '',
  },
  day: {
    type: Object as PropType<DayObject>,
    default: () => ({}),
  },
  isToday: {
    type: Boolean,
    default: false,
  },
  hour: {
    type: Number,
    default: 0,
  },
  data: {
    type: Array as PropType<EventObject[]>,
    default: () => [],
  },
});

const formatedHourName = computed(() => {
  return props.hour < 11 ? `0${props.hour - 1}:00` : `${props.hour - 1}:00`;
});
const formatedNextHourName = computed(() => {
  return props.hour < 10 ? `0${props.hour}:00` : `${props.hour}:00`;
});

const goToClickedMonth = (): void => {};
const openSelectedDayCalendar = (): void => {
  emits('openDayCalendar');
};
const filteredDataPerHour = computed(() => {
  return props.data.filter((item) => {
    return dayjs(item.start).isBetween(
      dayjs(`${props.selectedDate} ${formatedHourName.value}`).subtract(
        1,
        'minute'
      ),
      dayjs(`${props.selectedDate} ${formatedNextHourName.value}`)
    );
  });
});
const filteredDataToRender = computed(() => {
  return filteredDataPerHour.value.reduce((acc: any, x) => {
    acc[x.start] = [...(acc[x.start] || []), x];
    return acc;
  }, {});
});
</script>

<template>
  <li
    class="CalendarDayItem"
    :class="{
      'CalendarDayItem--not-current': !props.day.isCurrentMonth,
    }"
    @click="goToClickedMonth"
  >
    <span
      class="CalendarDayItem__day"
      :class="{
        'CalendarDayItem__day--today': false,
      }"
      @click="openSelectedDayCalendar"
      >{{ formatedHourName }}</span
    >
    <div class="hour-container">
      <div
        class="CalendarDayItem__event-single-minute"
        v-for="item in filteredDataToRender"
        :key="item"
      >
        <EventItem
          v-for="element in item"
          :key="element.name"
          class="CalendarDayItem__single-event"
          :style="{ backgroundColor: element.color }"
          :event-data="element"
        >
        </EventItem>
      </div>
    </div>
  </li>
</template>

<style lang="sass">
.CalendarDayItem
  height: auto
  min-height: 30px
  color: black
  list-style: none
  display: flex
  flex-direction: row
  align-items: center
  border-bottom: 1px solid grey

  &--not-current
    background-color: lightgrey

  &__day
    border-radius: 50%
    width: 50px
    height: 24px
    display: flex
    justify-content: center
    align-items: center
    cursor: pointer
    align-self: baseline

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

  &__event-single-minute
    display: flex

  &__single-event
    min-height: 40px
    width: 100px
    border: 1px solid grey
    border-radius: 3px
    margin: 5px

  .hour-container
    flex: 1
</style>
