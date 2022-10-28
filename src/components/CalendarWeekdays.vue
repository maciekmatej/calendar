<script setup lang="ts">
const weekdays = ['Pon', 'Wt', 'Śr', 'Czw', 'Pt', 'Sb', 'Ndz'];
</script>

<template>
  <ol class="day-of-week">
    <li v-for="weekday in weekdays" :key="weekday">
      {{ weekday }}
    </li>
  </ol>
</template>

<style lang="sass">
.CalendarMonthDayItem
  width: 50px
  height: 50px
  border-left: 1px solid black
  border-bottom: 1px solid black
  border-right: 1px solid black
</style>
