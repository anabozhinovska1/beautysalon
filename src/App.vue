<script setup>
import AppNavbar from './components/AppNavbar.vue'
import AppFooter from './components/AppFooter.vue'
</script>

<template>
  <AppNavbar />
  <main>
    <router-view />
  </main>
  <AppFooter />
</template>

<style scoped>
main {
  min-height: calc(100vh - 200px); /* Adjust based on navbar/footer height */
}
</style>
