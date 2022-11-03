<script setup lang="ts">
import { computed, onMounted, ref, type Ref } from 'vue';
import dayjs from 'dayjs';
import weekday from 'dayjs/plugin/weekday';
import weekOfYear from 'dayjs/plugin/weekOfYear';

import CalendarDateSelector from './CalendarDateSelector.vue';
import CalendarDateHeader from './CalendarDateHeader.vue';
import CalendarMonthDayItem from './CalendarMonthDayItem.vue';
import CalendarDayItem from './CalendarDayItem.vue';
import CalendarWeekdays from './CalendarWeekdays.vue';
// import locale from 'dayjs/locale/pl';

dayjs.extend(weekday);
dayjs.extend(weekOfYear);

type EventObject = {
  name: string;
  start: string;
  end: string;
  color: string;
};
const isOpenDayCalendar = ref(false);
const selectedDate = ref(dayjs());
const selectedDateForDayCalendar = ref('');
const today = computed(() => {
  return dayjs();
});
const month = computed(() => {
  return Number(selectedDate.value.format('M'));
});

const year = computed(() => {
  return Number(selectedDate.value.format('YYYY'));
});
const numberOfDaysInMonth = computed(() => {
  return dayjs(selectedDate.value).daysInMonth();
});
const selectedMonthDays = computed(() => {
  return [...Array(numberOfDaysInMonth.value)].map((day, index) => {
    return {
      date: dayjs(`${year.value}-${month.value}-${index + 1}`).format(
        'YYYY-MM-DD'
      ),
      isCurrentMonth: true,
    };
  });
});
const previousMonthDays = computed(() => {
  const firstDayOfTheMonthWeekday = getWeekday(selectedMonthDays.value[0].date);
  console.log(firstDayOfTheMonthWeekday, selectedMonthDays.value[0].date);
  const previousMonth = dayjs(`${year.value}-${month.value}-01`).subtract(
    1,
    'month'
  );
  const visibleNumberOfDaysFromPreviousMonth = firstDayOfTheMonthWeekday
    ? firstDayOfTheMonthWeekday - 1
    : 6;
  const previousMonthLastMondayDayOfMonth = dayjs(
    selectedMonthDays.value[0].date
  )
    .subtract(visibleNumberOfDaysFromPreviousMonth, 'day')
    .date();

  return [...Array(visibleNumberOfDaysFromPreviousMonth)].map((day, index) => {
    return {
      date: dayjs(
        `${previousMonth.year()}-${previousMonth.month() + 1}-${
          previousMonthLastMondayDayOfMonth + index
        }`
      ).format('YYYY-MM-DD'),
      isCurrentMonth: false,
    };
  });
});
const nextMonthDays = computed(() => {
  const LastDayOfTheMonthWeekday = getWeekday(
    selectedMonthDays.value[selectedMonthDays.value.length - 1].date
  );
  const nextMonth = dayjs(`${year.value}-${month.value}-01`).add(1, 'month');
  const visibleNumberOfDaysFromNextMonth = LastDayOfTheMonthWeekday
    ? 7 - LastDayOfTheMonthWeekday
    : 0;

  return [...Array(visibleNumberOfDaysFromNextMonth)].map((day, index) => {
    return {
      date: dayjs(
        `${nextMonth.year()}-${nextMonth.month() + 1}-${index + 1}`
      ).format('YYYY-MM-DD'),
      isCurrentMonth: false,
    };
  });
});
const getWeekday = (date: string) => {
  return dayjs(date).weekday();
};
const daysToRenderInCalendar = computed(() => {
  return [
    ...previousMonthDays.value,
    ...selectedMonthDays.value,
    ...nextMonthDays.value,
  ];
});
const names = [
  'Meeting',
  'Holiday',
  'PTO',
  'Travel',
  'Event',
  'Birthday',
  'Conference',
  'Party',
];
const colors = [
  'blue',
  'indigo',
  'deep-purple',
  'cyan',
  'green',
  'orange',
  'grey darken-1',
];
const events: Ref<EventObject[]> = ref([]);

onMounted(() => {
  updateRange({ start: '2022-11-03', end: '2022-11-31' });
});
const setNewDate = (date: dayjs.Dayjs) => {
  selectedDate.value = date;
};
const openDayCalendar = (date: string) => {
  isOpenDayCalendar.value = true;
  selectedDateForDayCalendar.value = date;
};
const updateRange = ({ start, end }: any) => {
  const min = new Date(`${start}T00:00:00`);
  const max = new Date(`${end}T23:59:59`);
  const days = (max.getTime() - min.getTime()) / 86400000;
  const eventCount = rnd(days, days + 40);

  for (let i = 0; i < eventCount; i++) {
    const allDay = rnd(0, 3) === 0;
    const firstTimestamp = rnd(min.getTime(), max.getTime());
    const first = new Date(firstTimestamp - (firstTimestamp % 900000));
    const secondTimestamp = rnd(2, allDay ? 288 : 8) * 900000;
    const second = new Date(first.getTime() + secondTimestamp);

    events.value.push({
      name: names[rnd(0, names.length - 1)],
      start: first.toISOString().slice(0, 16).split('T').join(' '),
      end: second.toISOString().slice(0, 16).split('T').join(' '),
      color: colors[rnd(0, colors.length - 1)],
    });
  }
};
const rnd = (a: number, b: number) => {
  return Math.floor((b - a + 1) * Math.random()) + a;
};
const filteredDataPerHour = () => {
  events.value.filter(
    (item) => item.start === selectedDateForDayCalendar.value
  );
};
</script>

<template>
  <div class="TheCalendar">
    <div class="calendar-month" v-if="!isOpenDayCalendar">
      <div class="calendar-month-header">
        <CalendarDateHeader :selected-date="selectedDate" />
        <CalendarDateSelector
          :selected-date="selectedDate"
          :current-date="today"
          @set-new-date="setNewDate"
        />
      </div>
      <CalendarWeekdays />
      <ol class="month-calendar-grid">
        <CalendarMonthDayItem
          v-for="(day, index) in daysToRenderInCalendar"
          :key="index"
          :day="day"
          :is-today="today.format('YYYY-MM-DD') === day.date"
          :data="events.filter((item) => item.start.slice(0, 10) === day.date)"
          @open-day-calendar="openDayCalendar"
        />
      </ol>
    </div>
    <div class="calendar-day" v-else>
      <div class="calendar-day-header">
        <CalendarDateHeader :selected-date="selectedDate" />
        <CalendarDateSelector
          :selected-date="selectedDate"
          :current-date="today"
          @set-new-date="setNewDate"
        />
      </div>
      <ol class="day-calendar-grid">
        <CalendarDayItem
          v-for="hour in 24"
          :key="hour"
          :hour="hour"
          :selected-date="selectedDateForDayCalendar"
          :data="
            events.filter(
              (item) => item.start.slice(0, 10) === selectedDateForDayCalendar
            )
          "
        />
      </ol>
    </div>
  </div>
</template>

<style lang="sass">
.TheCalendar
  width: 100%
.calendar-month
  .month-calendar-grid
    background-color: white
    display: grid
    grid-template-columns: repeat(7, 150px [col-start])



  .CalendarMonthDayItem
    border-top: 1px solid black
    border-right: 1px solid black
    &:nth-child(7n + 1)
      border-left: 1px solid black

    &:nth-child(7n + 1):nth-last-child(-n + 7), &:nth-child(7n + 1):nth-last-child(-n + 7) ~ .CalendarMonthDayItem
      border-bottom: 1px solid black

.calendar-day
  .day-calendar-grid
      background-color: white
      display: flex
      flex-direction: column
      grid-template-columns: 20px 1fr
      padding: 0
</style>
