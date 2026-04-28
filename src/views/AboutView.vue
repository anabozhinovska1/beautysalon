<script setup>
import AboutSection from '../components/AboutSection.vue'
</script>

<template>
  <div class="about-page">
    <div class="page-header">
      <h1>About Us</h1>
      <p>Learn more about Secret Beauty Studio.</p>
    </div>
    <AboutSection />
  </div>
</template>

<style scoped>
.about-page {
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
