<script setup>
import ContactSection from '../components/ContactSection.vue'
</script>

<template>
  <div class="contact-page">
    <div class="page-header">
      <h1>Contact Us</h1>
      <p>Get in touch with Secret Beauty Studio.</p>
    </div>
    <ContactSection />
  </div>
</template>

<style scoped>
.contact-page {
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
