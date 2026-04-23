<script setup>
import { ref, computed } from 'vue'

const services = [
  { category: "Hair Services", name: "Haircut", price: 1000 },
  { category: "Hair Services", name: "Blow dry", price: 600 },
  { category: "Hair Services", name: "Hair styling", price: 800 },
  { category: "Hair Services", name: "Hair coloring", price: 2000 },
  { category: "Hair Services", name: "Highlights", price: 2500 },
  { category: "Hair Services", name: "Balayage", price: 3000 },
  { category: "Hair Services", name: "Ombre", price: 2800 },
  { category: "Hair Services", name: "Keratin treatment", price: 3500 },
  { category: "Hair Services", name: "Hair botox", price: 3200 },
  { category: "Hair Services", name: "Hair extensions", price: 4000 },
  { category: "Hair Services", name: "Deep conditioning", price: 1200 },
  { category: "Hair Services", name: "Scalp treatment", price: 1500 },
  { category: "Nail Services", name: "Classic manicure", price: 800 },
  { category: "Nail Services", name: "Classic pedicure", price: 1000 },
  { category: "Nail Services", name: "Gel manicure", price: 1200 },
  { category: "Nail Services", name: "Gel pedicure", price: 1400 },
  { category: "Nail Services", name: "French manicure", price: 1000 },
  { category: "Nail Services", name: "Acrylic nails", price: 1500 },
  { category: "Nail Services", name: "Nail extensions", price: 1800 },
  { category: "Nail Services", name: "Nail refill", price: 1200 },
  { category: "Nail Services", name: "Nail art", price: 500 },
  { category: "Nail Services", name: "Spa manicure", price: 1300 },
  { category: "Nail Services", name: "Spa pedicure", price: 1600 },
  { category: "Facial Treatments", name: "Classic facial", price: 1800 },
  { category: "Facial Treatments", name: "Deep cleansing facial", price: 2000 },
  { category: "Facial Treatments", name: "Hydrating facial", price: 1900 },
  { category: "Facial Treatments", name: "Anti-aging facial", price: 2500 },
  { category: "Facial Treatments", name: "Acne treatment", price: 2200 },
  { category: "Facial Treatments", name: "Chemical peel", price: 2800 },
  { category: "Facial Treatments", name: "Microdermabrasion", price: 3000 },
  { category: "Lash & Brow", name: "Lash extensions (classic)", price: 1500 },
  { category: "Lash & Brow", name: "Lash extensions (volume)", price: 1800 },
  { category: "Lash & Brow", name: "Lash lift", price: 1200 },
  { category: "Lash & Brow", name: "Lash tint", price: 500 },
  { category: "Lash & Brow", name: "Brow shaping", price: 600 },
  { category: "Lash & Brow", name: "Brow tint", price: 400 },
  { category: "Lash & Brow", name: "Brow lamination", price: 1000 },
  { category: "Body Treatments", name: "Relaxing massage", price: 1500 },
  { category: "Body Treatments", name: "Deep tissue massage", price: 2000 },
  { category: "Body Treatments", name: "Hot stone massage", price: 2500 },
  { category: "Body Treatments", name: "Body scrub", price: 1800 },
  { category: "Body Treatments", name: "Body wrap", price: 2200 },
  { category: "Body Treatments", name: "Cellulite treatment", price: 3000 },
  { category: "Hair Removal", name: "Eyebrow waxing", price: 300 },
  { category: "Hair Removal", name: "Lip waxing", price: 250 },
  { category: "Hair Removal", name: "Chin waxing", price: 300 },
  { category: "Hair Removal", name: "Full face waxing", price: 800 },
  { category: "Hair Removal", name: "Arm waxing", price: 600 },
  { category: "Hair Removal", name: "Leg waxing", price: 1000 },
  { category: "Hair Removal", name: "Bikini waxing", price: 800 },
  { category: "Hair Removal", name: "Underarm waxing", price: 400 },
  { category: "Hair Removal", name: "Laser hair removal", price: 1500 },
  { category: "Makeup", name: "Day makeup", price: 1200 },
  { category: "Makeup", name: "Evening makeup", price: 1500 },
  { category: "Makeup", name: "Glam makeup", price: 2000 },
  { category: "Makeup", name: "Bridal makeup", price: 3000 },
  { category: "Makeup", name: "Photoshoot makeup", price: 1800 },
  { category: "Makeup", name: "Prom makeup", price: 1600 },
  { category: "Makeup", name: "Makeup trial", price: 1000 },
  { category: "Bridal Services", name: "Bridal hair styling", price: 2500 },
  { category: "Bridal Services", name: "Bridal makeup", price: 3000 },
  { category: "Bridal Services", name: "Bridal skincare prep", price: 2000 },
  { category: "Bridal Services", name: "Full bridal package", price: 5000 }
]

const categories = computed(() => [...new Set(services.map(s => s.category))])

const selectedCategory = ref(null)

const toggleCategory = (category) => {
  selectedCategory.value = selectedCategory.value === category ? null : category
}

const getServicesByCategory = (category) => {
  return services.filter(s => s.category === category)
}
</script>

<template>
  <section class="services">
    <div class="services-container">
      <h2>Our Services</h2>
      <div class="services-list">
        <div
          v-for="cat in categories"
          :key="cat"
          class="service-category"
          :class="{ 'expanded': selectedCategory === cat }"
          @click="toggleCategory(cat)"
        >
          <div class="service-header">
            <h3>{{ cat }}</h3>
            <span class="toggle-icon">{{ selectedCategory === cat ? '−' : '+' }}</span>
          </div>
          <Transition name="expand">
            <div v-if="selectedCategory === cat" class="service-items">
              <div
                v-for="item in getServicesByCategory(cat)"
                :key="item.name"
                class="service-item"
              >
                <span class="service-name">{{ item.name }}</span>
                <span class="service-price">{{ item.price }} MKD</span>
              </div>
            </div>
          </Transition>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.services {
  padding: var(--section-gap) 0;
  background: var(--color-background-soft);
}

.services-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.services h2 {
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 3rem;
  color: var(--color-text-primary);
}

.services-list {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.service-category {
  background: var(--color-background);
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  transition: transform 0.3s, box-shadow 0.3s;
  cursor: pointer;
  overflow: hidden;
}

.service-category:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.12);
}

.service-category.expanded {
  box-shadow: 0 8px 40px rgba(0, 0, 0, 0.15);
}

.service-header {
  padding: 1.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.service-header h3 {
  font-size: 1.25rem;
  margin: 0;
  color: var(--color-text-primary);
  font-family: var(--font-serif);
}

.toggle-icon {
  font-size: 1.5rem;
  font-weight: bold;
  color: var(--color-accent);
  transition: transform 0.3s;
}

.service-category.expanded .toggle-icon {
  transform: rotate(180deg);
}

.service-items {
  padding: 0 1.5rem 1.5rem 1.5rem;
  border-top: 1px solid var(--color-border);
}

.service-item {
  display: flex;
  justify-content: space-between;
  padding: 0.5rem 0;
  color: var(--color-text-secondary);
  border-bottom: 1px solid var(--color-border);
}

.service-item:last-child {
  border-bottom: none;
}

/* Transition animations */
.expand-enter-active,
.expand-leave-active {
  transition: all 0.3s ease;
}

.expand-enter-from,
.expand-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

.expand-enter-to,
.expand-leave-from {
  opacity: 1;
  transform: translateY(0);
}
</style>
