<script setup>
import { ref } from 'vue'

const isMenuOpen = ref(false)

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value
}
</script>

<template>
  <nav class="navbar">
    <div class="nav-container">
      <div class="nav-logo">
        <router-link to="/">Secret Beauty Studio</router-link>
      </div>
      <div class="nav-links" :class="{ 'nav-links--open': isMenuOpen }">
        <router-link to="/" @click="isMenuOpen = false">Home</router-link>
        <router-link to="/services" @click="isMenuOpen = false">Services</router-link>
        <router-link to="/about" @click="isMenuOpen = false">About</router-link>
        <router-link to="/gallery" @click="isMenuOpen = false">Gallery</router-link>
        <router-link to="/booking" @click="isMenuOpen = false">Booking</router-link>
        <router-link to="/contact" @click="isMenuOpen = false">Contact</router-link>
      </div>
      <div class="nav-contact">
        <a href="tel:+1234567890">+1 (234) 567-890</a>
      </div>
      <button class="hamburger" @click="toggleMenu" :class="{ 'hamburger--open': isMenuOpen }">
        <span></span>
        <span></span>
        <span></span>
      </button>
    </div>
  </nav>
</template>

<style scoped>
.navbar {
  background: var(--color-background);
  padding: 1rem 0;
  border-bottom: 1px solid var(--color-border);
  position: sticky;
  top: 0;
  z-index: 100;
}

.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 1rem;
}

.nav-logo a {
  font-family: var(--font-serif);
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--color-text-primary);
}

.nav-links {
  display: flex;
  gap: 2rem;
  align-items: center;
}

.nav-links a {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  color: var(--color-text-secondary);
  transition: color 0.3s;
}

.nav-links a:hover,
.nav-links a.router-link-active {
  color: var(--color-accent);
}

.nav-contact {
  font-size: 0.9rem;
  color: var(--color-text-secondary);
}

.hamburger {
  display: none;
  flex-direction: column;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;
}

.hamburger span {
  width: 25px;
  height: 3px;
  background: var(--color-text-primary);
  margin: 3px 0;
  transition: 0.3s;
}

.hamburger--open span:nth-child(1) {
  transform: rotate(-45deg) translate(-5px, 6px);
}

.hamburger--open span:nth-child(2) {
  opacity: 0;
}

.hamburger--open span:nth-child(3) {
  transform: rotate(45deg) translate(-5px, -6px);
}

@media (max-width: 768px) {
  .nav-links {
    position: fixed;
    top: 100%;
    left: 0;
    width: 100%;
    height: 0;
    overflow: hidden;
    background: var(--color-background);
    flex-direction: column;
    gap: 1rem;
    padding: 1rem;
    transition: height 0.3s;
  }

  .nav-links--open {
    height: auto;
    border-top: 1px solid var(--color-border);
  }

  .nav-contact {
    display: none;
  }

  .hamburger {
    display: flex;
  }
}
</style>
