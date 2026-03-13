<script setup>
import { ref, reactive } from 'vue'

const form = reactive({
  name: '',
  phone: '',
  email: '',
  service: '',
  specialist: '',
  date: '',
  time: '',
  notes: ''
})

const isSubmitted = ref(false)
const errors = ref({})

const services = [
  'Hairdressing',
  'Manicure-Pedicure',
  'Facial Treatments',
  'Body Procedures'
]

const specialists = [
  'Any',
  'Anna',
  'Maria',
  'Sophia'
]

const validateForm = () => {
  errors.value = {}
  if (!form.name.trim()) errors.value.name = 'Name is required'
  if (!form.phone.trim()) errors.value.phone = 'Phone is required'
  if (!form.email.trim()) errors.value.email = 'Email is required'
  else if (!/\S+@\S+\.\S+/.test(form.email)) errors.value.email = 'Invalid email'
  if (!form.service) errors.value.service = 'Service is required'
  if (!form.date) errors.value.date = 'Date is required'
  if (!form.time) errors.value.time = 'Time is required'
  return Object.keys(errors.value).length === 0
}

const submitForm = () => {
  if (validateForm()) {
    // Mock submission
    console.log('Form submitted:', form)
    isSubmitted.value = true
    // Reset form
    Object.keys(form).forEach(key => form[key] = '')
  }
}
</script>

<template>
  <div class="booking-form">
    <h2>Book Your Appointment</h2>
    <div v-if="isSubmitted" class="success-message">
      <p>Thank you! Your appointment request has been submitted. We'll contact you soon to confirm.</p>
      <button @click="isSubmitted = false">Book Another</button>
    </div>
    <form v-else @submit.prevent="submitForm">
      <div class="form-row">
        <div class="form-group">
          <label for="name">Full Name *</label>
          <input id="name" v-model="form.name" type="text" />
          <span v-if="errors.name" class="error">{{ errors.name }}</span>
        </div>
        <div class="form-group">
          <label for="phone">Phone *</label>
          <input id="phone" v-model="form.phone" type="tel" />
          <span v-if="errors.phone" class="error">{{ errors.phone }}</span>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="email">Email *</label>
          <input id="email" v-model="form.email" type="email" />
          <span v-if="errors.email" class="error">{{ errors.email }}</span>
        </div>
        <div class="form-group">
          <label for="service">Service *</label>
          <select id="service" v-model="form.service">
            <option value="">Select Service</option>
            <option v-for="service in services" :key="service" :value="service">{{ service }}</option>
          </select>
          <span v-if="errors.service" class="error">{{ errors.service }}</span>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="specialist">Preferred Specialist</label>
          <select id="specialist" v-model="form.specialist">
            <option v-for="specialist in specialists" :key="specialist" :value="specialist">{{ specialist }}</option>
          </select>
        </div>
        <div class="form-group">
          <label for="date">Date *</label>
          <input id="date" v-model="form.date" type="date" />
          <span v-if="errors.date" class="error">{{ errors.date }}</span>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="time">Time *</label>
          <input id="time" v-model="form.time" type="time" />
          <span v-if="errors.time" class="error">{{ errors.time }}</span>
        </div>
        <div class="form-group">
          <label for="notes">Notes</label>
          <textarea id="notes" v-model="form.notes" rows="3"></textarea>
        </div>
      </div>
      <button type="submit" class="submit-btn">Submit Booking</button>
    </form>
  </div>
</template>

<style scoped>
.booking-form {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
  background: var(--color-background);
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.booking-form h2 {
  text-align: center;
  margin-bottom: 2rem;
  color: var(--color-text-primary);
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
  margin-bottom: 1rem;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group label {
  margin-bottom: 0.5rem;
  font-weight: 500;
  color: var(--color-text-primary);
  text-transform: uppercase;
  font-size: 0.8rem;
  letter-spacing: 0.5px;
}

.form-group input,
.form-group select,
.form-group textarea {
  padding: 0.75rem;
  border: 1px solid var(--color-border);
  border-radius: 8px;
  font-family: var(--font-sans);
  transition: border-color 0.3s;
}

.form-group input:focus,
.form-group select:focus,
.form-group textarea:focus {
  outline: none;
  border-color: var(--color-accent);
}

.error {
  color: #e74c3c;
  font-size: 0.8rem;
  margin-top: 0.25rem;
}

.submit-btn {
  width: 100%;
  padding: 1rem;
  background: var(--color-accent);
  color: var(--color-background);
  border: none;
  border-radius: 50px;
  font-size: 1rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  cursor: pointer;
  transition: background 0.3s;
  margin-top: 1rem;
}

.submit-btn:hover {
  background: var(--color-accent-dark);
}

.success-message {
  text-align: center;
  padding: 2rem;
}

.success-message p {
  margin-bottom: 1rem;
  color: var(--color-text-secondary);
}

@media (max-width: 768px) {
  .form-row {
    grid-template-columns: 1fr;
  }

  .booking-form {
    padding: 1rem;
  }
}
</style>
