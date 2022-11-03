<script setup lang="ts">
import { computed, onMounted, ref, type Ref } from 'vue';
import dayjs from 'dayjs';
// import locale from 'dayjs/locale/pl';

const props = defineProps({
  selectedDate: {
    type: dayjs.Dayjs,
    default: () => ({}),
  },
});
const selectedMonth = computed(() => {
  return props.selectedDate.format('MMMM YYYY');
});
</script>

<template>
  <div class="CalendarDateHeader">{{ selectedMonth }}</div>
</template>
