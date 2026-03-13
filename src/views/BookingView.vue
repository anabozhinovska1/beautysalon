<script setup>
import BookingForm from '../components/BookingForm.vue'
</script>

<template>
  <div class="booking-page">
    <div class="page-header">
      <h1>Book an Appointment</h1>
      <p>Schedule your beauty treatment today.</p>
    </div>
    <BookingForm />
  </div>
</template>

<style scoped>
.booking-page {
  padding: 2rem 0;
}

.page-header {
  text-align: center;
  padding: 2rem 0;
  background: var(--color-background-soft);
  margin-bottom: var(--section-gap);
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
</style>
