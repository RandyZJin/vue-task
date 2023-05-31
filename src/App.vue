<script setup>
import Location from './components/Location.vue'
import LocationSearch from './components/LocationSearch.vue'
</script>

<template>
  <main>
    <Location />
    <LocationSearch />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

template {
  display: flex;
  flex-direction: column;
}

</style>
