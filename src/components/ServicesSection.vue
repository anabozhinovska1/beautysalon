<script setup>
import ServiceCard from './ServiceCard.vue'

const services = [
  {
    title: 'Hairdressing',
    description: 'Professional cuts, styling, coloring, and treatments for all hair types.',
    image: 'https://images.unsplash.com/photo-1605497787865-93dd15b4cd05?w=400&h=300&fit=crop'
  },
  {
    title: 'Manicure-Pedicure',
    description: 'Luxurious nail care with the latest trends and techniques.',
    image: 'https://images.unsplash.com/photo-1616394584738-fc6e612e71b9?w=400&h=300&fit=crop'
  },
  {
    title: 'Facial Treatments',
    description: 'Rejuvenating facials and skincare treatments for radiant skin.',
    image: 'https://images.unsplash.com/photo-1570172619644-dfd03ed5d881?w=400&h=300&fit=crop'
  },
  {
    title: 'Body Procedures',
    description: 'Relaxing massages, body scrubs, and wellness treatments.',
    image: 'https://images.unsplash.com/photo-1544161515-4ab6ce6db874?w=400&h=300&fit=crop'
  }
]
</script>

<template>
  <section class="services">
    <div class="services-container">
      <h2>Our Services</h2>
      <div class="services-grid">
        <ServiceCard
          v-for="service in services"
          :key="service.title"
          :title="service.title"
          :description="service.description"
          :image="service.image"
        />
      </div>
    </div>
  </section>
</template>

<style scoped>
.services {
  padding: var(--section-gap) 0;
  background: var(--color-background-soft);
}

.services-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.services h2 {
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 3rem;
  color: var(--color-text-primary);
}

.services-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
}
</style>
