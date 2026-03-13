<template>
  <section class="contact">
    <div class="contact-container">
      <h2>Contact Us</h2>
      <div class="contact-grid">
        <div class="contact-item">
          <h3>Address</h3>
          <p>123 Beauty Street<br>City, State 12345</p>
        </div>
        <div class="contact-item">
          <h3>Phone</h3>
          <p><a href="tel:+1234567890">+1 (234) 567-890</a></p>
        </div>
        <div class="contact-item">
          <h3>Email</h3>
          <p><a href="mailto:info@bagira.com">info@bagira.com</a></p>
        </div>
        <div class="contact-item">
          <h3>Hours</h3>
          <p>Mon-Fri: 9am - 7pm<br>Sat: 8am - 5pm<br>Sun: Closed</p>
        </div>
      </div>
      <div class="social-section">
        <h3>Follow Us</h3>
        <div class="social-links">
          <a href="#" aria-label="Facebook">Facebook</a>
          <a href="#" aria-label="Instagram">Instagram</a>
          <a href="#" aria-label="Twitter">Twitter</a>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.contact {
  padding: var(--section-gap) 0;
  background: var(--color-background-soft);
}

.contact-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
  text-align: center;
}

.contact h2 {
  font-size: 2.5rem;
  margin-bottom: 3rem;
  color: var(--color-text-primary);
}

.contact-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
  margin-bottom: 3rem;
}

.contact-item {
  background: var(--color-background);
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.contact-item h3 {
  font-size: 1.25rem;
  margin-bottom: 1rem;
  color: var(--color-text-primary);
}

.contact-item p {
  color: var(--color-text-secondary);
  line-height: 1.5;
}

.contact-item a {
  color: var(--color-accent);
  text-decoration: none;
}

.social-section h3 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: var(--color-text-primary);
}

.social-links {
  display: flex;
  justify-content: center;
  gap: 1rem;
}

.social-links a {
  padding: 0.75rem 1.5rem;
  background: var(--color-accent);
  color: var(--color-background);
  border-radius: 50px;
  text-decoration: none;
  transition: background 0.3s;
}

.social-links a:hover {
  background: var(--color-accent-dark);
}
</style>
