<script setup>
const galleryImages = [
  {
    src: new URL('@/assets/sliki/1.face-mask.jpg', import.meta.url).href,
    alt: 'Face mask treatment'
  },
  {
    src: new URL('@/assets/sliki/2.eyebrows.jpg', import.meta.url).href,
    alt: 'Eyebrow service'
  },
  {
    src: new URL('@/assets/sliki/4.nails.jpg', import.meta.url).href,
    alt: 'Nail service'
  },
  {
    src: new URL('@/assets/sliki/8.lashes.jpg', import.meta.url).href,
    alt: 'Lash extension service'
  },
  {
    src: new URL('@/assets/sliki/10.hair.jpg', import.meta.url).href,
    alt: 'Hair styling service'
  },
  {
    src: new URL('@/assets/sliki/12.massage.jpg', import.meta.url).href,
    alt: 'Massage treatment'
  }
]
</script>

<template>
  <section class="gallery">
    <div class="gallery-container">
      <h2>Our Gallery</h2>
      <div class="gallery-grid">
        <div class="gallery-item" v-for="(image, index) in galleryImages" :key="index">
          <img :src="image.src" :alt="image.alt" />
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
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.gallery-item {
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  aspect-ratio: 1;
}

.gallery-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.gallery-item:hover img {
  transform: scale(1.08);
}

@media (max-width: 768px) {
  .gallery-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 1.2rem;
  }
}

@media (max-width: 480px) {
  .gallery-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
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
