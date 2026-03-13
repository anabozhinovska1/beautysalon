<template>
  <section class="hero">
    <div class="hero-container">
      <div class="hero-content">
        <h1>Beauty studio Bagira</h1>
        <p>Indulge in luxury beauty treatments tailored to your unique style. Our expert team provides exceptional service in a serene, elegant environment.</p>
        <router-link to="/booking" class="cta-button">Book now</router-link>
      </div>
      <div class="hero-image">
        <img src="https://images.unsplash.com/photo-1560066984-138dadb4c035?w=600&h=800&fit=crop&crop=face" alt="Beauty salon interior" />
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero {
  padding: 4rem 0;
  background: var(--color-background);
}

.hero-container {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 4rem;
  align-items: center;
  padding: 0 1rem;
}

.hero-content h1 {
  font-size: 3rem;
  line-height: 1.2;
  margin-bottom: 1.5rem;
  color: var(--color-text-primary);
}

.hero-content p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: 2rem;
  color: var(--color-text-secondary);
}

.cta-button {
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

.cta-button:hover {
  background: var(--color-accent-dark);
}

.hero-image {
  position: relative;
}

.hero-image img {
  width: 100%;
  height: auto;
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  object-fit: cover;
}

@media (max-width: 768px) {
  .hero-container {
    grid-template-columns: 1fr;
    gap: 2rem;
  }

  .hero-content h1 {
    font-size: 2rem;
  }

  .hero-image {
    order: -1;
  }
}
</style>
