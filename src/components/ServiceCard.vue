<script setup>
defineProps({
  title: String,
  description: String,
  image: String
})
</script>

<template>
  <div class="service-card">
    <div class="service-image">
      <img :src="image" :alt="title" />
    </div>
    <div class="service-content">
      <h3>{{ title }}</h3>
      <p>{{ description }}</p>
    </div>
  </div>
</template>

<style scoped>
.service-card {
  background: var(--color-background);
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  transition: transform 0.3s, box-shadow 0.3s;
}

.service-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.12);
}

.service-image {
  height: 200px;
  overflow: hidden;
}

.service-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s;
}

.service-card:hover .service-image img {
  transform: scale(1.05);
}

.service-content {
  padding: 1.5rem;
}

.service-content h3 {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
  color: var(--color-text-primary);
}

.service-content p {
  color: var(--color-text-secondary);
  line-height: 1.5;
}
</style>
