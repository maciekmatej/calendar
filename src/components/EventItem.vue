<script setup lang="ts">
import { onMounted, ref, type Ref, type PropType, computed } from 'vue';
import dayjs from 'dayjs';
import isBetween from 'dayjs/plugin/isBetween';
dayjs.extend(isBetween);

// import locale from 'dayjs/locale/pl';
type EventObject = {
  name: string;
  start: string;
  end: string;
  color: string;
};
const emits = defineEmits(['setNewDate', 'openDayCalendar']);
const props = defineProps({
  eventData: {
    type: Object as PropType<EventObject>,
    default: () => ({}),
  },
});
</script>

<template>
  <div class="EventItem">{{ eventData.start }}</div>
</template>

<style lang="sass"></style>
