<script setup lang="ts">
import { onMounted, ref, type Ref } from "vue";

type EventObject = {
  name: string;
  start: Date;
  end: Date;
  color: string;
};
onMounted(() => {
  updateRange({ start: "2020-05-12", end: "2020-05-18" });
});
const names = [
  "Meeting",
  "Holiday",
  "PTO",
  "Travel",
  "Event",
  "Birthday",
  "Conference",
  "Party",
];
const colors = [
  "blue",
  "indigo",
  "deep-purple",
  "cyan",
  "green",
  "orange",
  "grey darken-1",
];
const events: Ref<EventObject[]> = ref([]);
const updateRange = ({ start, end }: any) => {
  const min = new Date(`${start}T00:00:00`);
  const max = new Date(`${end}T23:59:59`);
  const days = (max.getTime() - min.getTime()) / 86400000;
  console.log(`${start}T00:00:00`);
  const eventCount = rnd(days, days + 20);

  for (let i = 0; i < eventCount; i++) {
    const allDay = rnd(0, 3) === 0;
    const firstTimestamp = rnd(min.getTime(), max.getTime());
    const first = new Date(firstTimestamp - (firstTimestamp % 900000));
    const secondTimestamp = rnd(2, allDay ? 288 : 8) * 900000;
    const second = new Date(first.getTime() + secondTimestamp);

    events.value.push({
      name: names[rnd(0, names.length - 1)],
      start: first,
      end: second,
      color: colors[rnd(0, colors.length - 1)],
    });
  }
};
const rnd = (a: number, b: number) => {
  return Math.floor((b - a + 1) * Math.random()) + a;
};
</script>

<template>
  <div>{{ events }}</div>
</template>
