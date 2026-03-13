<script setup>
import ServicesSection from '../components/ServicesSection.vue'
</script>

<template>
  <div class="services-page">
    <div class="page-header">
      <h1>Our Services</h1>
      <p>Discover our range of premium beauty treatments.</p>
    </div>
    <ServicesSection />
  </div>
</template>

<style scoped>
.services-page {
  padding: 2rem 0;
}

.page-header {
  text-align: center;
  padding: 2rem 0;
  background: var(--color-background-soft);
}

.page-header h1 {
  font-size: 3rem;
  margin-bottom: 1rem;
  color: var(--color-text-primary);
}

.page-header p {
  font-size: 1.2rem;
  color: var(--color-text-secondary);
}
</style>
