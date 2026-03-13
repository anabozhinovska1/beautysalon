<template>
  <div class="gallery-page">
    <div class="page-header">
      <h1>Gallery</h1>
      <p>Explore our work and inspiration.</p>
    </div>
    <section class="full-gallery">
      <div class="gallery-container">
        <div class="gallery-grid">
          <div class="gallery-item" v-for="i in 12" :key="i">
            <img :src="`https://images.unsplash.com/photo-15${i}000000000000000?w=400&h=400&fit=crop`" :alt="`Gallery image ${i}`" />
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
.gallery-page {
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

.full-gallery {
  padding: var(--section-gap) 0;
  background: var(--color-background);
}

.gallery-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}

.gallery-item {
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.gallery-item img {
  width: 100%;
  height: 300px;
  object-fit: cover;
  transition: transform 0.3s;
}

.gallery-item:hover img {
  transform: scale(1.05);
}
</style>
