<template>
  <footer class="footer">
    <div class="footer-container">
      <div class="footer-section">
        <h3>Bagira Beauty Salon</h3>
        <p>Luxury beauty services in the heart of the city.</p>
      </div>
      <div class="footer-section">
        <h4>Contact</h4>
        <p>123 Beauty Street<br>City, State 12345</p>
        <p>Phone: <a href="tel:+1234567890">+1 (234) 567-890</a></p>
        <p>Email: <a href="mailto:info@bagira.com">info@bagira.com</a></p>
      </div>
      <div class="footer-section">
        <h4>Hours</h4>
        <p>Mon-Fri: 9am - 7pm</p>
        <p>Sat: 8am - 5pm</p>
        <p>Sun: Closed</p>
      </div>
      <div class="footer-section">
        <h4>Follow Us</h4>
        <div class="social-links">
          <a href="#" aria-label="Facebook">FB</a>
          <a href="#" aria-label="Instagram">IG</a>
          <a href="#" aria-label="Twitter">TW</a>
        </div>
      </div>
    </div>
    <div class="footer-bottom">
      <p>&copy; 2024 Bagira Beauty Salon. All rights reserved.</p>
    </div>
  </footer>
</template>

<style scoped>
.footer {
  background: var(--color-background-soft);
  padding: 2rem 0 1rem;
  margin-top: var(--section-gap);
}

.footer-container {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
  padding: 0 1rem;
}

.footer-section h3 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
}

.footer-section h4 {
  font-size: 1rem;
  margin-bottom: 1rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.footer-section p {
  margin-bottom: 0.5rem;
  color: var(--color-text-secondary);
}

.footer-section a {
  color: var(--color-accent);
}

.social-links {
  display: flex;
  gap: 1rem;
}

.social-links a {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--color-accent);
  color: var(--color-background);
  border-radius: 50%;
  transition: background 0.3s;
}

.social-links a:hover {
  background: var(--color-accent-dark);
}

.footer-bottom {
  text-align: center;
  padding-top: 1rem;
  border-top: 1px solid var(--color-border);
  margin-top: 2rem;
  color: var(--color-text-secondary);
  font-size: 0.9rem;
}
</style>
