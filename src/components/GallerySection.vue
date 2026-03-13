<template>
  <section class="gallery">
    <div class="gallery-container">
      <h2>Our Gallery</h2>
      <div class="gallery-grid">
        <div class="gallery-item" v-for="i in 6" :key="i">
          <img :src="`https://images.unsplash.com/photo-15${i}000000000000000?w=300&h=300&fit=crop`" :alt="`Gallery image ${i}`" />
        </div>
      </div>
      <router-link to="/gallery" class="view-more">View Full Gallery</router-link>
    </div>
  </section>
</template>

<style scoped>
.gallery {
  padding: var(--section-gap) 0;
  background: var(--color-background-soft);
}

.gallery-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
  text-align: center;
}

.gallery h2 {
  font-size: 2.5rem;
  margin-bottom: 3rem;
  color: var(--color-text-primary);
}

.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
  margin-bottom: 2rem;
}

.gallery-item {
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.gallery-item img {
  width: 100%;
  height: 250px;
  object-fit: cover;
  transition: transform 0.3s;
}

.gallery-item:hover img {
  transform: scale(1.05);
}

.view-more {
  display: inline-block;
  background: var(--color-accent);
  color: var(--color-background);
  padding: 1rem 2rem;
  border-radius: 50px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  font-weight: 500;
  transition: background 0.3s;
}

.view-more:hover {
  background: var(--color-accent-dark);
}
</style>
