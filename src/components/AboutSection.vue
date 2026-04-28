<template>
  <section class="about">
    <div class="about-container">
      <div class="about-image">
        <img src="https://images.unsplash.com/photo-1582095133179-bfd08e2fc6b3?w=600&h=400&fit=crop" alt="Salon interior" />
      </div>
      <div class="about-content">
        <h2>About Secret Beauty Studio</h2>
        <p>At Secret Beauty Studio, we believe beauty is an art form. Our team of skilled professionals is dedicated to providing personalized treatments that enhance your natural beauty and boost your confidence.</p>
        <p>With years of experience and a passion for excellence, we offer a serene environment where you can relax and rejuvenate.</p>
        <div class="promo-cards">
          <div class="promo-card">
            <h4>Expert Team</h4>
            <p>Certified professionals with years of experience.</p>
          </div>
          <div class="promo-card">
            <h4>Premium Products</h4>
            <p>Only the finest, salon-grade products used.</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.about {
  padding: var(--section-gap) 0;
  background: var(--color-background);
}

.about-container {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 4rem;
  align-items: center;
  padding: 0 1rem;
}

.about-image img {
  width: 100%;
  height: auto;
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.about-content h2 {
  font-size: 2.5rem;
  margin-bottom: 1.5rem;
  color: var(--color-text-primary);
}

.about-content p {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: 1rem;
  color: var(--color-text-secondary);
}

.promo-cards {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
  margin-top: 2rem;
}

.promo-card {
  background: var(--color-background-soft);
  padding: 1.5rem;
  border-radius: 12px;
  text-align: center;
}

.promo-card h4 {
  font-size: 1rem;
  margin-bottom: 0.5rem;
  color: var(--color-text-primary);
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.promo-card p {
  color: var(--color-text-secondary);
  font-size: 0.9rem;
}

@media (max-width: 768px) {
  .about-container {
    grid-template-columns: 1fr;
    gap: 2rem;
  }

  .promo-cards {
    grid-template-columns: 1fr;
  }
}
</style>
