<script setup lang="ts">
import TheCalendar from './components/TheCalendar.vue';
</script>

<template>
  <header>
    <img
      alt="Vue logo"
      class="logo"
      src="./assets/logo.svg"
      width="125"
      height="125"
    />

    <div class="wrapper"></div>
  </header>

  <main>
    <TheCalendar />
  </main>
</template>

<style scoped></style>
