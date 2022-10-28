<script setup lang="ts">
import { onMounted, ref, type Ref } from 'vue';
import dayjs from 'dayjs';
import locale from 'dayjs/locale/pl';

const emits = defineEmits(['setNewDate']);
const props = defineProps({
  selectedDate: {
    type: dayjs.Dayjs,
    default: () => ({}),
  },
  currentDate: {
    type: dayjs.Dayjs,
    default: () => ({}),
  },
});
const selectPrevious = () => {
  let newSelectedDate = dayjs(props.selectedDate).subtract(1, 'month');
  emits('setNewDate', newSelectedDate);
};
const selectCurrent = () => {
  let newSelectedDate = dayjs(props.currentDate);
  emits('setNewDate', newSelectedDate);
};
const selectNext = () => {
  let newSelectedDate = dayjs(props.selectedDate).add(1, 'month');
  emits('setNewDate', newSelectedDate);
};
</script>

<template>
  <div class="calendar-date-selector">
    {{ selectedDate }}
    <span @click="selectPrevious">﹤</span>
    <span @click="selectCurrent">Today</span>
    <span @click="selectNext">﹥</span>
  </div>
</template>
