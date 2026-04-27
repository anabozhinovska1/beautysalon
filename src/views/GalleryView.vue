<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const galleryImages = ref([
  // Hair images
  {
    src: new URL('@/assets/sliki/10.hair.jpg', import.meta.url).href,
    alt: 'Hair styling service',
    title: 'Hair Styling',
    description: 'Bridal Look',
    loaded: false
  },
  {
    src: new URL('@/assets/sliki/13.hair.jpg', import.meta.url).href,
    alt: 'Hair care service',
    title: 'Hair Coloring',
    description: 'Balayage Technique',
    loaded: false
  },
  // Nails images
  {
    src: new URL('@/assets/sliki/4.nails.jpg', import.meta.url).href,
    alt: 'Nail service',
    title: 'Nail Service',
    description: 'Classic Manicure',
    loaded: false
  },
  {
    src: new URL('@/assets/sliki/7.nailss.jpg', import.meta.url).href,
    alt: 'Nail design',
    title: 'Nail Design',
    description: 'Gel Art',
    loaded: false
  },
  // Face mask images
  {
    src: new URL('@/assets/sliki/1.face-mask.jpg', import.meta.url).href,
    alt: 'Face mask treatment',
    title: 'Face Mask',
    description: 'Hydrating Treatment',
    loaded: false
  },
  // Facial images
  {
    src: new URL('@/assets/sliki/11.facial.jpg', import.meta.url).href,
    alt: 'Facial treatment',
    title: 'Facial Treatment',
    description: 'Anti-Aging',
    loaded: false
  },
  // Lashes images
  {
    src: new URL('@/assets/sliki/8.lashes.jpg', import.meta.url).href,
    alt: 'Lash extension service',
    title: 'Lash Extensions',
    description: 'Volume',
    loaded: false
  },
  // Brow images (only 2.2.eyebrows)
  {
    src: new URL('@/assets/sliki/2.2.eyebrows.jpg', import.meta.url).href,
    alt: 'Eyebrow treatment',
    title: 'Eyebrow Shaping',
    description: 'Lamination',
    loaded: false
  },
  // Massage images
  {
    src: new URL('@/assets/sliki/12.massage.jpg', import.meta.url).href,
    alt: 'Massage treatment',
    title: 'Massage',
    description: 'Relaxing Body Treatment',
    loaded: false
  },
  // Image named "3."
  {
    src: new URL('@/assets/sliki/3..webp', import.meta.url).href,
    alt: 'Beauty service',
    title: 'Beauty Service',
    description: 'Custom Treatment',
    loaded: false
  },
  // Makeup images
  {
    src: new URL('@/assets/sliki/9.hair&makeup.jpg', import.meta.url).href,
    alt: 'Hair and makeup service',
    title: 'Hair & Makeup',
    description: 'Evening Glam',
    loaded: false
  },
  // Bridal images
  {
    src: new URL('@/assets/sliki/5.bridal-service.webp', import.meta.url).href,
    alt: 'Bridal service',
    title: 'Bridal Service',
    description: 'Full Package',
    loaded: false
  }
])

const isLightboxOpen = ref(false)
const currentIndex = ref(0)
const touchStartX = ref(0)
const touchEndX = ref(0)

const openLightbox = (index) => {
  currentIndex.value = index
  isLightboxOpen.value = true
}

const closeLightbox = () => {
  isLightboxOpen.value = false
}

const prevImage = () => {
  currentIndex.value = currentIndex.value > 0 ? currentIndex.value - 1 : galleryImages.value.length - 1
}

const nextImage = () => {
  currentIndex.value = currentIndex.value < galleryImages.value.length - 1 ? currentIndex.value + 1 : 0
}

const handleSwipe = (e) => {
  touchEndX.value = e.changedTouches[0].clientX
  const diff = touchStartX.value - touchEndX.value
  if (Math.abs(diff) > 50) {
    if (diff > 0) nextImage()
    else prevImage()
  }
}

onMounted(() => {
  const handleKeydown = (e) => {
    if (!isLightboxOpen.value) return
    if (e.key === 'Escape') closeLightbox()
    if (e.key === 'ArrowLeft') prevImage()
    if (e.key === 'ArrowRight') nextImage()
  }
  window.addEventListener('keydown', handleKeydown)
  onUnmounted(() => window.removeEventListener('keydown', handleKeydown))
})
</script>

<template>
  <div class="gallery-page">
    <div class="page-header">
      <h1>Gallery</h1>
      <p>Explore our work and inspiration.</p>
    </div>
    <section class="full-gallery">
      <div class="gallery-container">
        <div class="gallery-grid">
          <div class="gallery-item" v-for="(image, index) in galleryImages" :key="index" @click="openLightbox(index)" role="button" tabindex="0" :aria-label="`View ${image.title}`">
            <div class="skeleton" v-if="!image.loaded"></div>
            <img :src="image.src" :alt="image.alt" loading="lazy" @load="image.loaded = true" />
            <div class="overlay">
              <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                <circle cx="12" cy="12" r="3"></circle>
              </svg>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Lightbox -->
    <div v-if="isLightboxOpen" class="lightbox-overlay" @click="closeLightbox">
      <div class="lightbox-content" @click.stop>
        <button class="close-btn" @click="closeLightbox" aria-label="Close lightbox">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <line x1="18" y1="6" x2="6" y2="18"></line>
            <line x1="6" y1="6" x2="18" y2="18"></line>
          </svg>
        </button>
        <button class="nav-btn prev-btn" @click="prevImage" aria-label="Previous image">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="15,18 9,12 15,6"></polyline>
          </svg>
        </button>
        <img :src="galleryImages[currentIndex].src" :alt="galleryImages[currentIndex].alt" @touchstart="touchStartX = $event.touches[0].clientX" @touchend="handleSwipe" />
        <button class="nav-btn next-btn" @click="nextImage" aria-label="Next image">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="9,18 15,12 9,6"></polyline>
          </svg>
        </button>
        <div class="caption">
          <h3>{{ galleryImages[currentIndex].title }} – {{ galleryImages[currentIndex].description }}</h3>
          <button class="cta-btn" @click="router.push('/booking')">
            Book your appointment now
            <svg class="heart-icon" viewBox="0 0 24 24" fill="currentColor">
              <path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"/>
            </svg>
          </button>
        </div>
      </div>
    </div>
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
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  row-gap: 3rem;
  column-gap: 2rem;
  justify-content: center;
}

.gallery-item {
  width: 320px;
  height: 320px;
  overflow: hidden;
  border-radius: 6px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  position: relative;
  z-index: 1;
  cursor: pointer;
  transition: box-shadow 0.3s ease;
}

.gallery-item:hover {
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.15);
}

.skeleton {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: loading 1.5s infinite;
}

@keyframes loading {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

.gallery-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
  display: block;
  transition: transform 0.3s ease;
}

.gallery-item:hover img {
  transform: scale(1.05);
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.gallery-item:hover .overlay {
  opacity: 1;
}

.icon {
  width: 2rem;
  height: 2rem;
  color: white;
}

.lightbox-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease;
}

.lightbox-content {
  position: relative;
  max-width: 90%;
  max-height: 90%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.lightbox-content img {
  max-width: 100%;
  max-height: 70vh;
  object-fit: contain;
  animation: scaleIn 0.3s ease;
}

.close-btn {
  position: absolute;
  top: -40px;
  right: 0;
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  z-index: 1001;
  padding: 0.5rem;
  border-radius: 50%;
  transition: background 0.3s ease;
}

.close-btn:hover {
  background: rgba(255, 255, 255, 0.1);
}

.close-btn svg {
  width: 1.5rem;
  height: 1.5rem;
}

.nav-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(0, 0, 0, 0.5);
  border: none;
  color: white;
  cursor: pointer;
  padding: 1rem;
  border-radius: 50%;
  transition: background 0.3s ease;
}

.nav-btn:hover {
  background: rgba(0, 0, 0, 0.7);
}

.nav-btn svg {
  width: 1.5rem;
  height: 1.5rem;
}

.prev-btn {
  left: -60px;
}

.next-btn {
  right: -60px;
}

.caption {
  margin-top: 1rem;
  text-align: center;
  background: rgba(0, 0, 0, 0.6);
  padding: 1rem;
  border-radius: 8px;
  backdrop-filter: blur(5px);
}

.caption h3 {
  margin: 0 0 1rem 0;
  font-size: 1.8rem;
  font-weight: 500;
  color: white;
  text-shadow: 0 1px 3px rgba(0, 0, 0, 0.7);
}

.cta-btn {
  background: var(--color-background-soft);
  color: var(--color-text-primary);
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 500;
  transition: background 0.3s ease, transform 0.2s ease;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin: 0 auto;
}

.cta-btn:hover {
  background: var(--color-text-secondary);
  transform: translateY(-2px);
}

.heart-icon {
  width: 1.2rem;
  height: 1.2rem;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes scaleIn {
  from { transform: scale(0.9); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

@media (max-width: 768px) {
  .gallery-grid {
    grid-template-columns: repeat(2, 1fr);
    row-gap: 3rem;
    column-gap: 2rem;
  }

  .gallery-item {
    width: 100%;
    height: auto;
    aspect-ratio: 1;
  }

  .nav-btn {
    display: none; /* Hide arrows on mobile, use swipe */
  }

  .close-btn {
    top: 10px;
    right: 10px;
  }

  .lightbox-content img {
    max-height: 80vh;
  }
}

@media (max-width: 480px) {
  .gallery-grid {
    grid-template-columns: 1fr;
    row-gap: 3rem;
    column-gap: 2rem;
  }

  .gallery-item {
    width: 100%;
    height: auto;
    aspect-ratio: 1;
  }
}
</style>
