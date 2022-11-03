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
.day-of-week
  color: black
  background-color: white
  display: grid
  grid-template-columns: repeat(7, 150px [col-start])
  list-style: none
</style>
